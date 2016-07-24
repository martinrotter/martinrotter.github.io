---
layout: post
title: Integrating NSIS with qmake
author: Martin Rotter
date: 2016-07-20 08:42:00 +0200
category: it-programming
language: en
---

NSIS is a tool for creating fancy and modern GUI-based installers for your software. It is, in fact, fully-featured programming language. It can be used to generate very basic and simple installers as well as more advanced ones. I will show you how to integrate NSIS into `qmake`-based project.
<!--more-->

### Important links

Just make sure to check these:

* [tutorials](http://nsis.sourceforge.net/Category:Tutorials),
* [examples](http://nsis.sourceforge.net/Category:Code_Examples),
* [language reference](http://nsis.sourceforge.net/Docs/Chapter4.html).

### qmake

`qmake` is build tool used by Qt framework. qmake takes `*.pro` file as an input and generates all necessary makefiles which are in turn executed and produce desired binary artifacts, including target executables, installers, documentation and other stuff.

`qmake` does not have any native support for NSIS, but NSIS support can be added in a elegant via with custom target. Let's call this target `nsis`. So, after implementing the target, user could call the creating of installer with command `make nsis` (if using GNU Make).

### Needed NSIS scripts.

You need to have two scripts in your project. First, you need NSIS script to hold your variables. It can look like this:

```text
!define VERSION "@APP_VERSION@"
!define APP_VERSION "@APP_VERSION@"
!define APP_NAME "@APP_NAME@"
!define EXE_NAME "@EXE_NAME@"
!define README_FILE "README"
!define LICENSE_FILE "@PWD@\resources\text\COPYING_GNU_GPL"
!define MUI_ICON "@PWD@\resources\graphics\@APP_LOW_NAME@.ico"
!define MUI_UNICON "@PWD@\resources\graphics\@APP_LOW_NAME@.ico"
!define PATCH  "0"
!define OUTPUT_FILE "@OUT_PWD@\@APP_LOW_NAME@-@APP_VERSION@-win32.exe"
!define BINARY_TREE "@OUT_PWD@\app"
```

Let's name this file `NSIS.definitions.nsh.in` and kepp it in your project folder structure somewhere.

Second, you need the actual main NSIS script which includes the above script with variables and compiles the actual desired installer. It can look like this (long file!):

```text
;--------------------------------
; Do necessary inclusions.
!include NSIS.definitions.nsh
!include MUI2.nsh

;--------------------------------
; Basic values definitions.

; Name and file.
Name "${APP_NAME} portable"
OutFile "${OUTPUT_FILE}"

; Set custom branding text.
BrandingText "${APP_NAME}"

; Set compression.
SetCompressor /SOLID /FINAL lzma

; Default installation folder.
InstallDir "$PROGRAMFILES\${APP_NAME}"
InstallDirRegKey HKCU "Software\${APP_NAME}" "Install Directory"

; Require administrator access.
RequestExecutionLevel admin

;--------------------------------
; Interface Settings

; Show "are you sure" dialog when cancelling installation.
!define MUI_ABORTWARNING

;--------------------------------
; Pages

; Pages for installator.
!insertmacro MUI_PAGE_WELCOME
!insertmacro MUI_PAGE_LICENSE "${LICENSE_FILE}"
!insertmacro MUI_PAGE_DIRECTORY
!insertmacro MUI_PAGE_COMPONENTS

; Start menu folder page configuration.
!define MUI_STARTMENUPAGE_REGISTRY_ROOT "HKCU" 
!define MUI_STARTMENUPAGE_REGISTRY_KEY "Software\${APP_NAME}" 
!define MUI_STARTMENUPAGE_REGISTRY_VALUENAME "Start Menu Folder"

!insertmacro MUI_PAGE_INSTFILES

; Offer user to launch the application right when it is installed.
!define MUI_FINISHPAGE_RUN "$INSTDIR\${EXE_NAME}"

!insertmacro MUI_PAGE_FINISH

; Pages for uninstallator.
!insertmacro MUI_UNPAGE_CONFIRM
!insertmacro MUI_UNPAGE_INSTFILES
!insertmacro MUI_UNPAGE_FINISH

;--------------------------------
; Languages.

!insertmacro MUI_LANGUAGE "English"

;--------------------------------
; Helper macros.

!macro ExecWaitJob _exec
StrCpy $9 0
System::Call 'kernel32::CreateIoCompletionPort(i -1,i0,i0,i0)i.r1'
${IfThen} $1 != 0 ${|} IntOp $9 $9 + 1 ${|}
System::Call 'kernel32::CreateJobObject(i0,i0)i.r2'
${IfThen} $2 != 0 ${|} IntOp $9 $9 + 1 ${|}
System::Call '*(i 0,i $1)i.r0'
System::Call 'kernel32::SetInformationJobObject(i $2,i 7,i $0,i 8)i.r3'
${IfThen} $3 != 0 ${|} IntOp $9 $9 + 1 ${|}
System::Free $0
System::Call '*(i,i,i,i)i.r0'
System::Alloc 72
pop $4
System::Call "*$4(i 72)"
System::Call 'kernel32::CreateProcess(i0,t ${_exec},i0,i0,i0,i 0x01000004,i0,i0,i $4,i $0)i.r3'
${IfThen} $3 != 0 ${|} IntOp $9 $9 + 1 ${|}
System::Free $4
System::Call "*$0(i.r3,i.r4,i,i)"
System::Free $0
System::Call 'kernel32::AssignProcessToJobObject(i $2,i $3)i.r0'
${IfThen} $0 != 0 ${|} IntOp $9 $9 + 1 ${|}
System::Call 'kernel32::ResumeThread(i $4)i.r0'
${IfThen} $0 != -1 ${|} IntOp $9 $9 + 1 ${|}
System::Call 'kernel32::CloseHandle(i $3)'
System::Call 'kernel32::CloseHandle(i $4)'
!define __ExecWaitJob__ ExecWaitJob${__LINE__}
${__ExecWaitJob__}ioportwait:
System::Call 'kernel32::GetQueuedCompletionStatus(i $1,*i.r3,*i,*i.r4,i -1)i.r0'
${IfThen} $0 = 0 ${|} StrCpy $9 0 ${|}
${IfThen} $3 != 4 ${|} goto ${__ExecWaitJob__}ioportwait ${|}
System::Call 'kernel32::CloseHandle(i $2)'
System::Call 'kernel32::CloseHandle(i $1)'
!undef __ExecWaitJob__
${IfThen} $9 < 6 ${|} MessageBox mb_iconstop `ExecWaitJob "${_exec}" failed!` ${|}
!macroend
  
; If you are using solid compression, files that are required before
; the actual installation should be stored first in the data block,
; because this will make your installer start faster.
!insertmacro MUI_RESERVEFILE_LANGDLL

;--------------------------------
; Sections.

; Installer sections.
Section "!Core" Core
  IfFileExists $INSTDIR\Uninstall.exe +1 NotInstalled
  MessageBox MB_OK|MB_ICONEXCLAMATION "${APP_NAME} is already installed. $\n$\nClick 'OK' to automatically uninstall it, installer will then automatically continue with current installation." IDOK Uninstall
  
Uninstall:  
  !insertmacro ExecWaitJob '"$INSTDIR\Uninstall.exe /S"'

NotInstalled:
  SetOutPath "$INSTDIR"
  
  ; Install core application files.
  File /r "${BINARY_TREE}\"
  
  ; Store installation folder.
  WriteRegStr HKCU "Software\${APP_NAME}" "Install Directory" $INSTDIR
  
  ; Create uninstaller.
  WriteUninstaller "$INSTDIR\Uninstall.exe"
SectionEnd

Section "Desktop Icon" DesktopIcon
  CreateShortCut "$DESKTOP\${APP_NAME}.lnk" "$INSTDIR\${EXE_NAME}"
SectionEnd

Section "Start Menu Shortcuts" StartMenuShortcuts
  CreateDirectory "$SMPROGRAMS\${APP_NAME}"
  CreateShortCut "$SMPROGRAMS\${APP_NAME}\${APP_NAME}.lnk" "$INSTDIR\${EXE_NAME}"
  CreateShortCut "$SMPROGRAMS\${APP_NAME}\Uninstall.lnk" "$INSTDIR\Uninstall.exe"
SectionEnd

LangString DESC_Core ${LANG_ENGLISH} "Core installation files for ${APP_NAME}."
LangString DESC_DesktopIcon ${LANG_ENGLISH} "Desktop icon for ${APP_NAME}."
LangString DESC_StartMenuShortcuts ${LANG_ENGLISH} "Start Menu Shortcuts for ${APP_NAME}."

!insertmacro MUI_FUNCTION_DESCRIPTION_BEGIN
  !insertmacro MUI_DESCRIPTION_TEXT ${Core} $(DESC_Core)
  !insertmacro MUI_DESCRIPTION_TEXT ${DesktopIcon} $(DESC_DesktopIcon)
  !insertmacro MUI_DESCRIPTION_TEXT ${StartMenuShortcuts} $(DESC_StartMenuShortcuts)
!insertmacro MUI_FUNCTION_DESCRIPTION_END

; Uninstaller section.
Section "Uninstall"
  ; Here remove all files, but skip "data" folder.
  Push "$INSTDIR"
  Push "data"
  Call un.RmDirsButOne
  
  ; Remove uninstaller.
  Delete "$INSTDIR\*"
   
  ; Remove rest of installed files.
  ; Custom files are left intact.
  RMDir "$INSTDIR"
    
  Delete "$SMPROGRAMS\${APP_NAME}\${APP_NAME}.lnk"
  Delete "$SMPROGRAMS\${APP_NAME}\Uninstall.lnk"
  RMDir "$SMPROGRAMS\${APP_NAME}"
  
  Delete "$DESKTOP\${APP_NAME}.lnk"

  DeleteRegKey /ifempty HKCU "Software\${APP_NAME}"
SectionEnd

;--------------------------------
; Custom functions.

Function un.RmDirsButOne
  Exch $R0 ; exclude dir
  Exch
  Exch $R1 ; route dir
  Push $R2
  Push $R3

  ClearErrors
  FindFirst $R3 $R2 "$R1\*.*"
  IfErrors Exit

  Top:
    StrCmp $R2 "." Next
    StrCmp $R2 ".." Next
    StrCmp $R2 $R0 Next
    IfFileExists "$R1\$R2\*.*" 0 Next
    RmDir /r "$R1\$R2"

    #Goto Exit ;uncomment this to stop it being recursive (delete only one dir)

  Next:
    ClearErrors
    FindNext $R3 $R2
    IfErrors Exit
    Goto Top

  Exit:
    FindClose $R3

  Pop $R3
  Pop $R2
  Pop $R1
  Pop $R0
FunctionEnd

; Executed when installer starts.
Function .onInit
  IntOp $0 ${SF_SELECTED} | ${SF_RO}
  SectionSetFlags ${Core} $0
FunctionEnd
```

You can see that this script includes the variables script on its first line. Also, the variables from first short script are used in this script on many times. Search them if you wish.

You can see that the first NSIS smaller script with variables does not contain the actual values, only placeholders, for example `"@APP_VERSION@"` is probably not desired version string. It should probably be something like `"1.2.0"` or something. You need to write another script, which takes this placeholder script and replaces all placeholders with needed real values. You can do that in custom NSIS target in your qmake project file.

### Defining custom NSIS target

One can easily define custom NSIS target in qmake project file like this:

```text
# Create NSIS installer target on Windows.
win32 {
  nsis.target = nsis
  nsis.depends = install
  nsis.commands = \
    $$shell_path($$shell_quote($$PWD/resources/scripts/findreplace/findreplace/bin/Release/findreplace.exe)) @APP_VERSION@ $$shell_quote($$APP_VERSION) @APP_NAME@ $$shell_quote($$APP_NAME) @APP_LOW_NAME@ $$shell_quote($$APP_LOW_NAME) @EXE_NAME@ $$shell_quote($${APP_LOW_NAME}.exe) @PWD@ $$shell_path($$shell_quote($$PWD)) @OUT_PWD@ $$shell_path($$shell_quote($$OUT_PWD)) $$shell_path($$shell_quote($$PWD/resources/nsis/NSIS.definitions.nsh.in)) > $$shell_path($$shell_quote($$OUT_PWD/NSIS.definitions.nsh)) && \
    xcopy /Y $$shell_path($$shell_quote($$PWD/resources/nsis/NSIS.template.in)) $$shell_path($$shell_quote($$OUT_PWD/)) && \
    $$shell_path($$shell_quote($$PWD/resources/scripts/nsis/makensis.exe)) $$shell_path($$shell_quote($$OUT_PWD/NSIS.template.in))

  QMAKE_EXTRA_TARGETS += nsis
}
```

You can see that line `nsis.commands` is very long, but in fact is very simple. This line calls special program `findreplace.exe` which has one and only purpose. It takes pairs of input arguments + one last extra argument. Last argument is file which should be used for finding and replacing (our small NSIS script with variables). The predecessing pairs are string which should be searched and replaced.

So the above command opens file `$$PWD/resources/nsis/NSIS.definitions.nsh.in` and performs bunch of string replacing like this:

```text
@APP_VERSION@ --> $$APP_VERSION
@APP_NAME@ --> $$APP_NAME
.
.
.
```

What does `@APP_VERSION@` mean? We talked about this. It is just placeholder and it now gets replaced with value of qmake variable `$$APP_VERSION`. You must define this variable somewhere in your `*.pro` file. You can also see that the output of `findreplace.exe` is redirected to new file `$$OUT_PWD/NSIS.definitions.nsh`. This is the final NSIS script with real variables. It is copied to output build directory (shadow build).

You can also see next line of target, which copies the main NSIS script also to output build directory. Last line of target executes NSIS compiler and passes the main NSIS script as argument.

### Real-world project using this?
Yes, of course. I have used this approach in one my project, [RSS Guard](https://github.com/martinrotter/rssguard). Check it out and examine file `rssguard.pro` and subdirectory `resources/nsis`.