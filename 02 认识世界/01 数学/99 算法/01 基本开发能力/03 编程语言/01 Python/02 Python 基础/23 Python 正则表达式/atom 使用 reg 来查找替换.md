---
title: atom 使用 reg 来查找替换
toc: true
date: 2019-07-21
---
# atom 使用 reg 来查找替换


I want to search and replace this

```
`https://example.com/`{.uri}
```

to

```
[https://example.com/](https://example.com/)
```


If you Cmd-F and open the search pane, there is a ".*" button at the right side. Click it and now it's regex mode.

I find

```
(http.*)\{\.uri\}
```

and replace to

```
[$1]($1)
```

> When doing a regular expression search, the replacement syntax to refer back to search groups is `$1`, `$2`, … `$&`. Refer to JavaScript's guide to regular expressions to learn more about regular expression syntax you can use in Atom.



# 相关

- [Find and Replace](https://flight-manual.atom.io/using-atom/sections/find-and-replace/)
- [Search and Replace with RegEx components in Atom editor](https://stackoverflow.com/questions/22220444/search-and-replace-with-regex-components-in-atom-editor)
