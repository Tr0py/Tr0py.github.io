---
title: 'VIM Tips: One Click to Cancel Searching Highlight'
date: 2022-06-15
tags:
  - notes
  - English
  - Vim
---

## Problem 

When you do a search in vim, all the matching strings would be highlighted, even
**after** the search.  This could be annoying.

To remove the highlighting, you can simply use `:noh`.

However, typing `ESC` + `:noh` everytime still bothers.  

## Solution

You could map `:noh` to a simple key: `Enter`.

Add this line to your vimrc 

```
:map <silent> <leader><cr> :noh<cr>
```

Then try a search, and press `Enter` to cancel the highlights.




(This is a trick I stealed from YF.)
