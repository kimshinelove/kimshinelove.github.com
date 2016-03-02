---
layout: post
section-type: post
title: My Blog Markdown Style Check!
category: Category
tags: [ 'blog', 'markdown' ]
---

앞으로 블로그를 해보려면 글쓰기부터 쉬워야 하니 첫 포스팅은 간단하게 마크다운 문법이 어떤식으로 보이는지 확인해야겠다.

마크다운 문법은 [여기][99]서 참고로 사용했고 아래는 내 블로그에서 마크다운 스타일이 어떻게 보기 위해 출력!

[99]: http://daringfireball.net/projects/markdown/ "여기"

---

A First Level Header
====================

A Second Level Header
---------------------

---

Now is the time for all good men to come to
the aid of their country. This is just a
regular paragraph.

The quick brown fox jumped over the lazy
dog's back.

---

### Header 3

---

> This is a blockquote.
>
> This is the second paragraph in the blockquote.
>
> ## This is an H2 in a blockquote

---

Some of these words *are emphasized*.
Some of these words _are emphasized also_.

Use two asterisks for **strong emphasis**.
Or, if you prefer, __use two underscores instead__.

---

*   Candy.
*   Gum.
*   Booze.

---

+   Candy.
+   Gum.
+   Booze.

---

-   Candy.
-   Gum.
-   Booze.


*   A list item.
    With multiple paragraphs.
*   Another item in the list.

---

This is an [example link](http://example.com/).

This is an [example link](http://example.com/ "With a Title").

---

I get 10 times more traffic from [Google][1] than from
[Yahoo][2] or [MSN][3].

[1]: http://google.com/        "Google"
[2]: http://search.yahoo.com/  "Yahoo Search"
[3]: http://search.msn.com/    "MSN Search"

---

I start my morning with a cup of coffee and
[The New York Times][NY Times].

[ny times]: http://www.nytimes.com/

---

![alt text](/path/to/img.jpg "Title")

![alt text][id]

[id]: /path/to/img.jpg "Title"

---

I strongly recommend against using any `<blink>` tags.

I wish SmartyPants used named entities like `&mdash;`
instead of decimal-encoded entites like `&#8212;`.

---

<blockquote>
    <p>For example.</p>
</blockquote>

## REFERENCE
* http://daringfireball.net/projects/markdown/
* https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet