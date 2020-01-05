---
layout: post
lang: en
title: Music from command line
image: /images/2019Mar/post-music.jpg
tags: ["life", "music"]
---

Pretty sure I am very late to the game but only recently I've found out how to 
download and listen to music right from the `Terminal` (or `iTerm2`, whichever your terminal is).  

It's great, everything is seamless.
My combination is [cmus](https://cmus.github.io/) as player and [youtube-dl](https://github.com/rg3/youtube-dl).

## Download youtube music with `youtube-dl`  
<hr>
**1. Installation**  
On Mac, with `Homebrew`, it's just a command away:  
`brew install youtube-dl`  

or with `MacPorts`:  
`sudo port install youtube-dl`

**2. Add alias for common usage**  
`youtube-dl` is a very powerful tool to download video/audio from Youtube.  
Its syntax is, therefore, also pretty complicated.  

For my usage, I simply needed the mp3 versions of the music videos. 
Also some of the meta information like artist and title should be correct at least for easier classification later.  

So here are 3 aliases that I commonly used to make the downloading easier:  

{% highlight ruby %}
alias ytdl="youtube-dl --extract-audio --audio-format mp3 --add-metadata --metadata-from-title \"(?P<title>.+?) (-|_|\|) (?P<artist>.+)\" -o \"%(title)s.mkv\""  
alias ytdl-atal="youtube-dl --extract-audio --audio-format mp3 --add-metadata --metadata-from-title \"(?P<title>.+?) (-|_|\|) (?P<artist>.+) (-|_|\||\() (?P<album>.+)\" -o \"%(title)s.mkv\""
alias ytdl-ta="youtube-dl --extract-audio --audio-format mp3 --add-metadata --metadata-from-title \"(?P<artist>.+?) (-|_|\|) (?P<title>.+)\" -o \"%(title)s.mkv\""
{% endhighlight %}

You can put this to your `.bashrc`, and start using as follow:  
{% highlight ruby %}
`ytdl [youtube url]`  
{% endhighlight %}

What the commands will be doing are:
- Extract audio and save the mp3 version of youtube video specify
- Save meta infos like `artist` and `title` according to partern

**For `ytdl`, the pattern is:** `[title] [separator] [artist]`, in which `[separator]` can be `-` or `_` or `|`  
For example, in the case of following video:  
<iframe width="560" height="315" src="https://www.youtube.com/embed/aTgXgN9fOsk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

- The `title` will be: `(Charlie Puth) Attention`  
- The `artist` will be:  `Josephine Alexandra | Fingerstyle Guitar Cover`  

I used short forms of `artist` (`a`), `title` (`t`), `album` (`al`) as reminder how the pattern looks like

**For `ytdl-atal` (or `artist`, `title`, `album`), the pattern is:** `[title] [separator] [artist] [separator] [album]`, in which `[separator]` is same as above with extra `(` in case of last separator.  

With the same video above, if we use `ytdl-atal`  the result would be:   

- The `title` will be: `(Charlie Puth) Attention`  
- The `artist` will be:  `Josephine Alexandra`  
- The `album` will be:  `Fingerstyle Guitar Cover`  
It is still not perfect yet but it is quite enough for me  

`ytdl-ta` is just a reverse in order of `ytdl`, should be used for cases like in this video:  
<iframe width="560" height="315" src="https://www.youtube.com/embed/tAKnT0g8MtA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>  

Of course there will be more patterns of title you will see on youtube but those are the common ones.  

## Play music with `cmus`
<hr>
**1. Installation**  
Again with magic of `Homebrew`, to install `cmus` on Mac is simply:  
`brew install cmus`
The first installation, I've accidentally downloaded and compiled the build on `https://cmus.github.io/#download`. But still `Homebrew` installation is much better.  

**2. Usage**  
`cmus` is a very useful player when you mostly work on Terminal. Its UI has 7 views (that you can switch around using number key 1-7 once cmus open):  
{% highlight ruby %}
VIEWS
       There are 7 views in cmus.  Press keys 1-7 to change active view.

       Library view (1)
              Display all tracks in so-called library. Tracks are sorted
              artist/album tree.  Artist sorting is done alphabetically.
              Albums are sorted by year.

       Sorted library view (2)
              Displays same content as view 1, but as a simple list which is
              automatically sorted by user criteria.

       Playlist view (3)
              Displays editable playlist with optional sorting.

       Play Queue view (4)
              Displays queue of tracks which are played next. These tracks are
              played before anything else (i.e. the playlist or library).

       Browser (5)
              Directory browser.  In this view, music can be added to either
              the library, playlist or queue from the filesystem.

       Filters view (6)
              Lists user defined filters.

       Settings view (7)
              Lists keybindings, unbound commands and options.  Remove
              bindings with D or del, change bindings and variables with enter
              and toggle variables with space.
{% endhighlight %}


If you are used to use terminal, it won't be that hard to use `cmus`. But to make it easier to remember the list of commands, I recommend this [cheatsheet](https://www.cheatography.com/stephenjjohnson/cheat-sheets/cmus/pdf/)  
As of the 2 video above, here is what it will look like after we've added them in playlist:  
![](/images/2019Mar/cmus-playlist.png)  

One thing I definitely loved about `cmus` is that the navigation keys are `j`, `k` for up and down which is super familiar if you used `vim`. So again, everything is seamless. 
There are much more functionalities of cmus to explore and I am still in the learning process.  

Hope you will find this useful if you are also late to the game :)  



