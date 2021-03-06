---
layout: post
title: Integrating NSIS with CMake
author: Martin Rotter
date: 2014-05-09 09:00:00 +0200
category: it-programming
language: en
---

NSIS is a tool for creating fancy and modern GUI-based installers for your software. It is, in fact, fully-featured programming language. It can be used to generate very basic and simple installers as well as more advanced ones.
<!--more-->

### Important links

Just make sure to check these:

* [tutorials](http://nsis.sourceforge.net/Category:Tutorials),
* [examples](http://nsis.sourceforge.net/Category:Code_Examples),
* [language reference](http://nsis.sourceforge.net/Docs/Chapter4.html).

### NSIS & CMake & CPack

Users of CMake are probably familiar with option to generate NSIS installers for your CMake-based projects.  Usually you have two options to generate your project with CMake or CPack:

1. You can use "package" target like this: `make package`.
2. You use use CPack directly to produce your NSIS installer like this: `cpack -G NSIS`.

Both commands need to be called from binary directory root.

### Adding necessary code to CMakeLists.txt

Well, you must enhance your `CMakeLists.txt` project file to enable NSIS support. I usually add new option `USE_NSIS` to the script. The option is bolean and it is added like this:

```text
option(USE_NSIS "Use NSIS generator to produce installer" OFF)
```

Then, depending on settings of the option, I set `make package` target like this:

```text
# Custom target for packaging.
if(USE_NSIS)
  set(CPACK_GENERATOR "NSIS")
else(USE_NSIS)
  set(CPACK_GENERATOR "ZIP")
endif(USE_NSIS)

set(CPACK_PACKAGE_NAME ${APP_LOW_NAME})
set(CPACK_PACKAGE_VERSION ${APP_VERSION})
set(CPACK_PACKAGE_VERSION_PATCH "0")
set(CPACK_PACKAGE_VENDOR ${APP_COMPANY})
set(CPACK_PACKAGE_INSTALL_REGISTRY_KEY ${APP_NAME})
set(CPACK_IGNORE_FILES "\\.psd$;/CVS/;/\\.svn/;/\\.git/;\\.swp$;/CMakeLists.txt.user;\\.#;/#;\\.tar.gz$;/CMakeFiles/;CMakeCache.txt;\\.qm$;/build/;\\.diff$;.DS_Store'")
set(CPACK_SOURCE_GENERATOR "TGZ")
set(CPACK_SOURCE_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}")
set(CPACK_SOURCE_IGNORE_FILES ${CPACK_IGNORE_FILES})
Plus, I set up some extra NSIS-related variables:

set(CPACK_NSIS_INSTALLED_ICON_NAME "${APP_LOW_NAME}.ico")
set(CPACK_NSIS_HELP_LINK ${APP_URL})
set(CPACK_NSIS_URL_INFO_ABOUT ${APP_URL})
set(CPACK_NSIS_CONTACT ${APP_EMAIL})
```

Note that variables `${APP_LOW_NAME}`, `${APP_VERSION}`, `${APP_COMPANY}`, `${APP_NAME}`, `${APP_URL}`, `${APP_EMAIL}` are string variables containing application lower-case name, version, name of developer, human-readable name of application, homepage URL of application, email to authors.

As the final NSIS-related part of your `CMakeLists.txt` file, you need to configure main NSIS definitons for our NSIS script and include CPack support:

```text
# Load packaging facilities.
include(CPack)

# Configure file with custom definitions for NSIS.
configure_file(
  ${PROJECT_SOURCE_DIR}/resources/nsis/NSIS.definitions.nsh.in
  ${CMAKE_CURRENT_BINARY_DIR}/resources/nsis/NSIS.definitions.nsh
)
```

You can see that sample project contains NSIS definitions in its source tree and they are moved (during configuration) into binary tree. Here are the contents of the definitions file (as you can see, it contains some basic constants which will be used in main NSIS script:

```text
!define VERSION "@APP_VERSION@"
!define APP_VERSION "@APP_VERSION@"
!define APP_NAME "@APP_NAME@"
!define EXE_NAME "@EXE_NAME@"
!define README_FILE "README"
!define LICENSE_FILE "@PROJECT_SOURCE_DIR@resourcestextLICENSE"
!define MUI_ICON "@PROJECT_SOURCE_DIR@resourcesgraphics@APP_LOW_NAME@.ico"
!define MUI_UNICON "@PROJECT_SOURCE_DIR@resourcesgraphics@APP_LOW_NAME@.ico"
!define MUI_WELCOMEFINISHPAGE_BITMAP "@PROJECT_SOURCE_DIR@resourcesgraphics@APP_LOW_NAME@-banner.bmp"
!define MUI_UNWELCOMEFINISHPAGE_BITMAP "@PROJECT_SOURCE_DIR@resourcesgraphics@APP_LOW_NAME@-banner.bmp"
!define PATCH  "0"
```

This is version which is created by CMake (after configuration):

```text
!define VERSION "2.0.1"
!define APP_VERSION "2.0.1"
!define APP_NAME "BuildmLearn Toolkit"
!define EXE_NAME "buildmlearn-toolkit"
!define README_FILE "README"
!define LICENSE_FILE "C:/Users/rotter/Documents/Projekty/BuildmLearn-ToolkitresourcestextLICENSE"
!define MUI_ICON "C:/Users/rotter/Documents/Projekty/BuildmLearn-Toolkitresourcesgraphicsbuildmlearn-toolkit.ico"
!define MUI_UNICON "C:/Users/rotter/Documents/Projekty/BuildmLearn-Toolkitresourcesgraphicsbuildmlearn-toolkit.ico"
!define MUI_WELCOMEFINISHPAGE_BITMAP "C:/Users/rotter/Documents/Projekty/BuildmLearn-Toolkitresourcesgraphicsbuildmlearn-toolkit-banner.bmp"
!define MUI_UNWELCOMEFINISHPAGE_BITMAP "C:/Users/rotter/Documents/Projekty/BuildmLearn-Toolkitresourcesgraphicsbuildmlearn-toolkit-banner.bmp"
!define PATCH  "0"
```

Now, all CMake-related parts of NSIS generation process are done. We just need to write main NSIS script which will actually generate our installer.

Important thing is to understand the process of how CMake works NSIS to produce the installer. Basic steps are these:

1. CMake checks if files `NSIS.template.in`, `NSIS.InstallOptions.ini.in` are in the module path (module path in sample project was set with this command: `set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/resources/nsis ${CMAKE_MODULE_PATH})`). If those files are found, then they are used for generating the installer.
2. CMake takes input NSIS script template file and generates output into binary tree folder: `${CMAKE_BINARY_DIR}_CPack_Packages${CPACK_TOPLEVEL_TAG}${CPACK_GENERATOR}` and files are renamed. During this stage, only SOME CMake and CPack variables are replaced in source NSIS template script. CPack (CMake) ignores other variables you define in your `CMakeLists.txt` file! Only variables `${CPACK_*}` are correctly used.
3. CMake runs NSIS compilator with generated NSIS files.
4. Generated installer is placed in binary tree folder.

### Main NSIS script

As stated before, NSIS script is usually placed in `${CMAKE_BINARY_DIR}_CPack_Packages${CPACK_TOPLEVEL_TAG}${CPACK_GENERATOR}` subdirectory during CMake generation step. Our sample NSIS script uses definitions from our `NSIS.definitions.nsh.in` file which was renamed during generation step and copied into binary build directory (see a few chapters above).

File with custom definitions is insluded in main NSIS script like this:

```text
;--------------------------------
; Do necessary inclusions.
!include ......resourcesnsisNSIS.definitions.nsh
!include MUI2.nsh
Basic variables and macros are defined:

;--------------------------------
; Basic values definitions.
!define INST_DIR "@CPACK_TEMPORARY_DIRECTORY@"

; Name and file.
Name "${APP_NAME}"
OutFile "@CPACK_TOPLEVEL_DIRECTORY@/@CPACK_OUTPUT_FILE_NAME@"

; Set custom branding text.
BrandingText "${APP_NAME}"

; Set compression.
SetCompressor /SOLID /FINAL lzma

;Default installation folder.
InstallDir "@CPACK_NSIS_INSTALL_ROOT@${APP_NAME}"
InstallDirRegKey HKCU "Software${APP_NAME}" "Install Directory"

; Require administrator access.
RequestExecutionLevel admin
```

Moreover, pages of the installer and uninstaller, which are displayed to users, are defined in a standard way:

```text
;--------------------------------
;Pages

; Pages for installator.
!insertmacro MUI_PAGE_WELCOME
!insertmacro MUI_PAGE_LICENSE "${LICENSE_FILE}"
!insertmacro MUI_PAGE_DIRECTORY
!insertmacro MUI_PAGE_COMPONENTS

;Start Menu Folder Page Configuration
!define MUI_STARTMENUPAGE_REGISTRY_ROOT "HKCU" 
!define MUI_STARTMENUPAGE_REGISTRY_KEY "Software${APP_NAME}" 
!define MUI_STARTMENUPAGE_REGISTRY_VALUENAME "Start Menu Folder"

!insertmacro MUI_PAGE_INSTFILES

; Offer user to launch the application right when it is installed.
!define MUI_FINISHPAGE_RUN "$INSTDIR${EXE_NAME}"

!insertmacro MUI_PAGE_FINISH

; Pages for uninstallator.
!insertmacro MUI_UNPAGE_CONFIRM
!insertmacro MUI_UNPAGE_INSTFILES
!insertmacro MUI_UNPAGE_FINISH
```

Script defines some extra stuff, like uninstaller, installation components and so on. Check out the whole script:

```text
; Copyright (c) 2012, BuildmLearn Contributors listed at http://buildmlearn.org/people/
; All rights reserved.
;
; Redistribution and use in source and binary forms, with or without
; modification, are permitted provided that the following conditions are met:
;
; * Redistributions of source code must retain the above copyright notice, this
;   list of conditions and the following disclaimer.
;
; * Redistributions in binary form must reproduce the above copyright notice,
;   this list of conditions and the following disclaimer in the documentation
;   and/or other materials provided with the distribution.
;
; * Neither the name of the BuildmLearn nor the names of its
;   contributors may be used to endorse or promote products derived from
;   this software without specific prior written permission.
;
; THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
; AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
; IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
; DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
; FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
; DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
; SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
; CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
; OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
; OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.


;--------------------------------
; Do necessary inclusions.
!include ......resourcesnsisNSIS.definitions.nsh
!include MUI2.nsh

;--------------------------------
; Basic values definitions.
!define INST_DIR "@CPACK_TEMPORARY_DIRECTORY@"

; Name and file.
Name "${APP_NAME}"
OutFile "@CPACK_TOPLEVEL_DIRECTORY@/@CPACK_OUTPUT_FILE_NAME@"

; Set custom branding text.
BrandingText "${APP_NAME}"

; Set compression.
SetCompressor /SOLID /FINAL lzma

;Default installation folder.
InstallDir "@CPACK_NSIS_INSTALL_ROOT@${APP_NAME}"
InstallDirRegKey HKCU "Software${APP_NAME}" "Install Directory"

; Require administrator access.
RequestExecutionLevel admin

;--------------------------------
; Interface Settings

; Show "are you sure" dialog when cancelling installation.
!define MUI_ABORTWARNING

;--------------------------------
;Pages

; Pages for installator.
!insertmacro MUI_PAGE_WELCOME
!insertmacro MUI_PAGE_LICENSE "${LICENSE_FILE}"
!insertmacro MUI_PAGE_DIRECTORY
!insertmacro MUI_PAGE_COMPONENTS

;Start Menu Folder Page Configuration
!define MUI_STARTMENUPAGE_REGISTRY_ROOT "HKCU" 
!define MUI_STARTMENUPAGE_REGISTRY_KEY "Software${APP_NAME}" 
!define MUI_STARTMENUPAGE_REGISTRY_VALUENAME "Start Menu Folder"

!insertmacro MUI_PAGE_INSTFILES

; Offer user to launch the application right when it is installed.
!define MUI_FINISHPAGE_RUN "$INSTDIR${EXE_NAME}"

!insertmacro MUI_PAGE_FINISH

; Pages for uninstallator.
!insertmacro MUI_UNPAGE_CONFIRM
!insertmacro MUI_UNPAGE_INSTFILES
!insertmacro MUI_UNPAGE_FINISH

;--------------------------------
; Languages.

!insertmacro MUI_LANGUAGE "English"

;--------------------------------
;Reserve Files
  
; If you are using solid compression, files that are required before
; the actual installation should be stored first in the data block,
; because this will make your installer start faster.
!insertmacro MUI_RESERVEFILE_LANGDLL

;--------------------------------
; Custom functions.

; Executed when installer starts.
Function .onInit
  IntOp $0 ${SF_SELECTED} | ${SF_RO}
  SectionSetFlags ${Core} $0
FunctionEnd

; Conditional saving to registry.
Function ConditionalAddToRegisty
  Pop $0
  Pop $1
  StrCmp "$0" "" ConditionalAddToRegisty_EmptyString
    WriteRegStr SHCTX "SoftwareMicrosoftWindowsCurrentVersionUninstall${APP_NAME}" "$1" "$0"
  ConditionalAddToRegisty_EmptyString:
FunctionEnd

;--------------------------------
; Sections.

; Installer sections.
Section "!Core" Core
  ReadRegStr $R0 SHCTX "SoftwareMicrosoftWindowsCurrentVersionUninstall${APP_NAME}" "UninstallString"
  IfFileExists $R0 +1 NotInstalled
  MessageBox MB_OK|MB_ICONEXCLAMATION "${APP_NAME} is already installed. $n$nClick 'OK' to uninstall it then continue with current installation." IDOK Uninstall
  
Uninstall:  
  ExecWait '"$R0" /S'

NotInstalled:
  SetOutPath "$INSTDIR"
  
  ; Install core application files.
  @CPACK_NSIS_FULL_INSTALL@
  
  ; Store installation folder.
  WriteRegStr HKCU "Software${APP_NAME}" "Install Directory" $INSTDIR
  
  ; Create uninstaller.
  WriteUninstaller "$INSTDIRUninstall.exe"
  
  ; Create entry in Windows "Add/Remove programs" panel.
  Push "DisplayName"
  Push "${APP_NAME}"
  Call ConditionalAddToRegisty
  Push "DisplayVersion"
  Push "${APP_VERSION}"
  Call ConditionalAddToRegisty
  Push "Publisher"
  Push "@CPACK_PACKAGE_VENDOR@"
  Call ConditionalAddToRegisty
  Push "UninstallString"
  Push "$INSTDIRUninstall.exe"
  Call ConditionalAddToRegisty
  Push "NoRepair"
  Push "1"
  Call ConditionalAddToRegisty
  Push "NoModify"
  Push "1"
  Call ConditionalAddToRegisty
  Push "DisplayIcon"
  Push "$INSTDIR@CPACK_NSIS_INSTALLED_ICON_NAME@"
  Call ConditionalAddToRegisty
  Push "HelpLink"
  Push "@CPACK_NSIS_HELP_LINK@"
  Call ConditionalAddToRegisty
  Push "URLInfoAbout"
  Push "@CPACK_NSIS_URL_INFO_ABOUT@"
  Call ConditionalAddToRegisty
  Push "Contact"
  Push "@CPACK_NSIS_CONTACT@"
  Call ConditionalAddToRegisty
SectionEnd

Section "Desktop Icon" DesktopIcon
  CreateShortCut "$DESKTOP${APP_NAME}.lnk" "$INSTDIR${EXE_NAME}"
SectionEnd

Section "Quick Launch Icon" QuickLaunchIcon  
  CreateShortCut "$QUICKLAUNCH${APP_NAME}.lnk" "$INSTDIR${EXE_NAME}"
SectionEnd

Section "Start Menu Shortcuts" StartMenuShortcuts
  CreateDirectory "$SMPROGRAMS${APP_NAME}"
  CreateShortCut "$SMPROGRAMS${APP_NAME}${APP_NAME}.lnk" "$INSTDIR${EXE_NAME}"
  CreateShortCut "$SMPROGRAMS${APP_NAME}Uninstall.lnk" "$INSTDIRUninstall.exe"
SectionEnd

LangString DESC_Core ${LANG_ENGLISH} "Core installation files for ${APP_NAME}."
LangString DESC_DesktopIcon ${LANG_ENGLISH} "Desktop icon for ${APP_NAME}."
LangString DESC_QuickLaunchIcon ${LANG_ENGLISH} "Quick launch icon for ${APP_NAME}."
LangString DESC_StartMenuShortcuts ${LANG_ENGLISH} "Start Menu Shortcuts for ${APP_NAME}."

!insertmacro MUI_FUNCTION_DESCRIPTION_BEGIN
  !insertmacro MUI_DESCRIPTION_TEXT ${Core} $(DESC_Core)
  !insertmacro MUI_DESCRIPTION_TEXT ${DesktopIcon} $(DESC_DesktopIcon)
  !insertmacro MUI_DESCRIPTION_TEXT ${QuickLaunchIcon} $(DESC_QuickLaunchIcon)
  !insertmacro MUI_DESCRIPTION_TEXT ${StartMenuShortcuts} $(DESC_StartMenuShortcuts)
!insertmacro MUI_FUNCTION_DESCRIPTION_END

; Uninstaller section.
Section "Uninstall"
  ; Remove core application files & directories.
  @CPACK_NSIS_DELETE_FILES@
  @CPACK_NSIS_DELETE_DIRECTORIES@

  ; Remove uninstaller.
  Delete "$INSTDIRUninstall.exe"
  
  ; Remove entry from Windows "Add/Remove programs" panel.
  DeleteRegKey SHCTX "SoftwareMicrosoftWindowsCurrentVersionUninstall${APP_NAME}"
  
  ; Remove rest of installed files.
  ; Custom files are left intact.
  RMDir "$INSTDIR"
    
  Delete "$SMPROGRAMS${APP_NAME}${APP_NAME}.lnk"
  Delete "$SMPROGRAMS${APP_NAME}Uninstall.lnk"
  RMDir "$SMPROGRAMS${APP_NAME}"
  
  Delete "$DESKTOP${APP_NAME}.lnk"
  Delete "$QUICKLAUNCH${APP_NAME}.lnk"

  DeleteRegKey /ifempty HKCU "Software${APP_NAME}"
SectionEnd
```