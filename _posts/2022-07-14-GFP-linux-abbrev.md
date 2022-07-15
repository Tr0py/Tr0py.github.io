---
title: 'What does GFP mean in Linux?'
date: 2022-07-14
tags:
  - notes
---

Frequently I see `GFP` in Linux source code but I don't know what that means.

It means "Get Free Page", `__get_free_pages`.  As indicated, it is used in page allocation.
See [`include/linux/gfp.h`](https://elixir.bootlin.com/linux/v5.4.205/source/include/linux/gfp.h) 
for more details.

There's [a website](https://kernelnewbies.org/KernelGlossary) that summarizes many abbreviations in Linux.


### References

* [What is the meaning of GFP in kmalloc flags?](https://stackoverflow.com/questions/11223523/what-is-the-meaning-of-gfp-in-kmalloc-flags)
* [KernelNewbies: KernelGlossary](https://kernelnewbies.org/KernelGlossary)
* [`include/linux/gfp.h` in Linux 5.4](https://elixir.bootlin.com/linux/v5.4.205/source/include/linux/gfp.h)
