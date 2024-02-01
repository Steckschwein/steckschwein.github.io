---
title: "ASMDOC - Documenting Assembly Code"
date: 2024-02-01
author: Thomas

draft: true
---

As the Steckschwein codebase (consisting of the library, bios, steckOS, programs) is growing, keeping track of function calls is becoming more and more of an issue. What procedures are there, what do they do, what kind of parameters do they expect, 
and what information do they return, and in which registers? We do not always want to browse through all of our code in order to find what we are looking for. 

Other (high level) languages have tools like javadoc, Doxygen, Sphinx, phpDocumentor, to name few. But for assembly language, the situations looks a lot different. Maybe
