---
layout: post
title: Generic Bubble Sort algorithm implementation in C & inline Assembly
author: Martin Rotter
date: 2016-07-19 08:00:00 +0200
category: IT & Programming
language: en
---

Code is pretty much self-explanatory.
<!--more-->

```c
#include <stdio.h>
#include <time.h>

#define IS_LESS		-1
#define IS_EQUAL	0
#define IS_MORE		1

// C.
void swap_c(void *e1, void *e2, size_t size);
void bubble_sort_c(void *base, size_t num, size_t size, int (*comparator) (const void *, const void *));

// Assembler.
void swap_a(void *e1, void *e2, size_t siz);
void bubble_sort(void *base, size_t num, size_t siz, int (*comparator) (const void *, const void *));

int comparator(const void * elem1, const void * elem2);

int comparator(const void *elem1, const void * elem2) {
  int *val1 = (int*) elem1;
  int *val2 = (int*) elem2;
  if (*val1 < *val2) {
    return IS_LESS;
  }
  else if (*val2 < *val1) {
    return IS_MORE;
  }
  else {
    return IS_EQUAL;
  }
}

void swap_a(void *e1, void *e2, size_t siz) {
  _asm {
    mov ebx, e1;
    mov ecx, e2;
    mov edi, siz;
    dec edi;
    mov edx, 0;

opakuj: cmp edi, 0;
    jl konec;

    mov dl, byte ptr [ebx];
    mov al, byte ptr [ecx];
    mov byte ptr [ebx], al;
    mov byte ptr [ecx], dl;

    inc ebx;
    inc ecx;
    dec edi;
    jmp opakuj;

konec: 
  };
}

void swap_c(void *e1, void *e2, size_t siz) {
  char *x1 = (char*) e1;
  char *x2 = (char*) e2;

  char temp;

  int i;
  int size = (int) siz;
  for (i = 0; i < size; i++) {
    temp = x1[i];
    x1[i] = x2[i];
    x2[i] = temp;
  }
}

// Seřazuje vstupní pole hodnot vzestupně.
void bubble_sort_c(void *base, size_t num, size_t size, int (*comparator) (const void *, const void *)) {
  size_t i;
  size_t j;

  // Char is one byte long, it is good for us.
  char *b1= (char*) base;

  for (i = 0; i < num; i++) {
    for (j = 0; j < num - i - 1; j++) {
      char *left = b1 + (j*size);
      char *right = b1 + ((j+1)*size);
      int result = comparator(left, right);
      if (result > IS_EQUAL) {
        swap_c(left, right, size);
      }
    }
  }
}

void bubble_sort(void *base, size_t num, size_t siz, int (*comparator) (const void *, const void *)) {
  _asm {
    mov ebx, -1;
    mov edx, base;

vnejsi: mov ecx, 0;
    inc ebx;
    cmp ebx, num;
    jae konec1;
    jmp vnitr;

vnitr:	mov edi, num;
    dec edi;
    sub edi, ebx;

    cmp ecx, edi;
    jae vnejsi;		

    mov edi, siz;
    imul edi, ecx;	
    add edi, base;

    mov esi, siz;
    imul esi, ecx;
    add esi, siz;
    add esi, base;

    push ecx;
    push esi;
    push edi;
    call comparator;
    add esp, 8;
    pop ecx;

    cmp eax, 0;				
    jg prohod;
    jmp pokrac;

prohod:	
    push ecx;
    push siz;
    push esi;
    push edi;
    call swap_a;
    add esp, 12;
    pop ecx;

pokrac:
    inc ecx;
    jmp vnitr;

konec1:
  };
}

int main(int argc, char *argv[]) {
  int i;
  int pole[4000];

  clock_t cas;

  for (i = 0; i < 4000; i++) {
    pole[i] = 4000 - i;
  }
  cas = clock();
  bubble_sort_c(pole, 4000, sizeof(int), comparator);
  cas = clock() - cas;
  printf("nnSort took %f seconds.n", (double) cas/CLOCKS_PER_SEC);

  for (i = 0; i < 400; i++) {
    printf("%d ", pole[i]);
  }

  for (i = 0; i < 4000; i++) {
    pole[i] = 4000 - i;
  }

  cas = clock();
  bubble_sort(pole, 4000, sizeof(int), comparator);
  cas = clock() - cas;
  printf("nnSort took %f seconds.n", (double) cas/CLOCKS_PER_SEC);

  for (i = 0; i < 400; i++) {
    printf("%d ", pole[i]);
  }

  printf("nn");

  return 0;
}
```