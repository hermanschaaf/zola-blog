---
title: Moving my blog from Hugo to Zola
description: Zola is the static site generator I almost built myself, but thankfully didn't need to
slug: /hugo-to-zola
date: 2024-12-15
draft: false
---
After a brief 6 year hiatus, I felt it was time I started posting to my blog again. Six years is a long time in software,
so when I ran `hugo serve` with the latest version of Hugo, all I got was a blank page. No errors, no logs, just a blank page.
Hmm.

Hugo, in case you don't know, is now a very popular static site generator, written in Go. But back when I started using it,
it was a relatively young upstart with a lot of promise. It comes with some nice-looking templates, live-reload features and
a very active community. But right now I was getting a blank page, with no hints where to go next. So, naturally, I went to 
Google.

After some Googling and browsing through the Hugo changelogs and issues (there were not supposed to be any breaking changes 
between the version I had last used and the latest), I still didn't know why I was getting a blank page. Well, I thought,
 maybe 6 years ago this would have taken ages to solve, but this is 2024, we have AI assistants now, right!? This is a great chance 
 to try out [Aider](https://github.com/Aider-AI/aider)! Aider is an AI coding assistant that runs in your terminal
 and can use all your code as context. I installed it, and asked it to fix the blank page I was facing. Amazingly, within 
 a few iterations, Aider was actually able to update the code in a way that at least resulted in more specific errors. A few more iterations and 
 a bit of Googling later, my blog was working again. Hooray!

Except that the styles were all wrong. The theme I was using also wasn't compatible with this version of Hugo anymore,
so it needed to be updated. I spent about 30 minutes trying to do so, without success, and it was at this point, 2 hours
in, that I was reminded of Hugo's terrible templating system (which, to be fair to Hugo, is based on Go's terrible templating 
system). Aider wasn't able to come to my aid here. So then a thought occurred: "Hey, writing a static site generator can't be that 
hard, can it? What if I made my own, with some Jinja-like templating? That would be nice."

All memories of actually wanting to write a blog post were long gone by this point, so I cracked open my Go IDE and started coding.
After about 30 minutes I had something going with [Pongo](https://github.com/flosch/pongo2), a template engine
for Go with Django-like syntax. It had been a few years since I used a non-standard template engine for Go, and it
seemed like Pongo didn't really support template inheritance, one of the most important features of Django templates. This
is when it became clear that this was going to be a bigger project. And maybe Go isn't the best fit for this... I've been
wanting to start a project in Rust for a while now, maybe I should do that?

I was just about to run `cargo new`, but then thought, maybe, just maybe, someone else had the same idea before me. And indeed,
when looking for static site generators written in Rust, I found a few candidates. The most promising of those turned out to be
[Zola](https://www.getzola.org/). And that, kids, is how I met your ~mother~ static site renderer written in Rust.

It seemed like the creator of Zola shared my dislike of Hugo's template engine, which instantly made me like it more:

> This tool and its template engine tera were born from an intense dislike of the (insane) Golang template engine and therefore of Hugo that I was using before for 6+ sites.

Zola comes with most of the same features as Hugo: single binary, live-reload, syntax highlighting, Sass compilation,
and more, but with a Django-like template syntax (that actually supports template inheritance).
I couldn't believe it. Another dream crushed before it even began. I pressed Ctrl+C on the incomplete `cargo new` command in my terminal, and typed `zola init blog` instead.

Zola's documentation is okay, and I'm not about to bash a free and open source project for not having great documentation, but
I did have some trouble initially figuring out the lay of the land, and the documentation didn't really help there. What helped
most was finding some example projects, helpfully listed in [EXAMPLES.md](https://github.com/getzola/zola/blob/master/EXAMPLES.md).
After looking through a few of those (and their source code), I got the basic idea. You have a `content` directory, which
has subdirectories containing markdown files. In each subdirectory, you can place an `_index.md` file to control some options
and (optionally) render a page at the root of the directory. For example, I put my blog posts in a `posts` directory like this:

```txt
├── posts
│   ├── 2014-04-14-the-popularity-of-go.md
│   ├── 2014-04-16-why-is-go-popular-in-china.md
│   ├── 2014-05-03-efficient-string-concatenation-in-go.md
│   ├── 2014-05-19-running-the-go-race-detector-with-cover.md
│   ├── 2014-12-13-solving-regex-crosswords-using-go.md
│   ├── 2015-01-26-open-source-go-projects-to-learn-from.md
│   ├── 2015-04-03-building-a-flashcard-app-with-react-native.md
│   ├── 2017-10-01-bit-manipulation-in-go-1-9.md
│   ├── 2018-07-09-rules-for-choosing-nonfiction-books.md
│   └── _index.md
```

And inside `_index.md`, I have this:

```txt
+++
title = "Herman Schaaf"
description = "Herman Schaaf's personal blog and website"
page_template = "post.html"
redirect_to = "/"
[extra]
+++
```

Something that tripped me up a bit is that there are two similar settings available: `template` and `page_template`. It turns
out that `template` is for controlling the template that renders the root page, while `page_template` refers to the template
used by all the other markdown files in the directory.

To ensure consistency with my previous Hugo website and not have broken links, I added a `path` to the frontmatter of
all my markdown files, to ensure they are still available at the top-level, like this:

```txt
---
title: The Popularity of Go
description: Some observations from Google Trends
path: /the-popularity-of-go
slug: the-popularity-of-go
date: 2014-04-14
---
```

Overall, moving from Hugo to Zola was quite easy. I decided to take the opportunity to also write my own styling, rather
than use an off-the-shelf theme like I had done previously with Hugo. I had to make some small changes to the markdown
frontmatter and content for compatibility with Zola. For example, Zola didn't recognize `text` or `plaintext` as a valid
language for code snippets, but `txt` works fine. In the frontmatter, I had to use `description` rather than `subtitle`.
I also ran into an issue where the hot-reloading feature of Zola sometimes hid errors from me, which took me a while to figure
out. After this I learned to stop and restart `zola serve` if something wasn't acting like I expected, to make sure I'm getting 
all the errors. But that was basically it.

With [14k+ stars on GitHub](https://github.com/getzola/zola) at the time of writing, I clearly wasn't the first person to 
come across Zola. But the work put into it by a passionate community has clearly paid off, and I want to thank everyone 
who contributed to the project so far. I could have spent the next couple of ~months~ years developing this myself, but truth be told,
I'm glad I didn't have to. And, hey, look, in the end I actually did write that blog post.