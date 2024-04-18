---
title: "Sorting Demo"
date: 2024-04-17
author: Thomas
tags:
  - vdp
  - sort
  - algorithm
  - c
  
draft: false
---

To share my fascination for the numerous sorting algorithm videos on youtube, I took some sorting algorithm examples in C from https://www.geeksforgeeks.org/sorting and visualized them using our BGI compatible C graphics library (more about that later).

The algorithms shown are:

* Bubble Sort
* Cocktail shaker Sort 
* Gnome Sort
* Insertion Sort
* Comb Sort
* Heap Sort
* Shell Sort
* Selection Sort
* Quick Sort
* Merge Sort
* Radix Sort


The code examples from https://www.geeksforgeeks.org/sorting are only slightly adapted and could be compiled with cc65 almost instantly. The trick was only to find the places in the code where the interesting search array accesses happen.

The lines a drawn using the "BGI" line(x1,y1,x2,y2) function, which then uses the V9958's command engine to draw lines.
Sorts up to 255 values are drawn in graphics mode 7, with a resolution of 255x212 pixels, while everything up to 512 values is drawn in graphics mode 6, which has a resolution of 512x212 pixels, but only 16 colors (which really does not matter here..).

Despite being "hardware accelerated", the line drawing part is very very slow and causes considerable slowdown to the sorting. I mean, even sorting 64 values with bubble sort is not THAT slow..  

Still a fun program to write and to watch.

{{< youtube 3m6WrJgtlcg >}}
