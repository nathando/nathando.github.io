---
layout: post
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

So here are 2 alias that I commonly used to make the downloading easier:  

{% highlight ruby %}
alias ytdl="youtube-dl --extract-audio --audio-format mp3 --add-metadata --metadata-from-title \"(?P<title>.+?) (-|_|\|) (?P<artist>.+)\" -o \"%(title)s.mkv\""  
alias ytdl2="youtube-dl --extract-audio --audio-format mp3 --add-metadata --metadata-from-title \"(?P<artist>.+?) (-|_|\|) (?P<title>.+)\" -o \"%(title)s.mkv\""
{% endhighlight %}

They are almost identical with just a reverse in order of `artist` and `title` in the pattern.  

What the commands will be doing are:
- Extract audio and save the mp3 version of youtube video specify
- Save meta infos like `artist` and `title` according to partern

For `ytdl`, the pattern is: `[title] [separator] [artist]`, in which `[separator]` can be `-` or `_` or `|`.  
For example, in the case of following video:  
<iframe width="560" height="315" src="https://www.youtube.com/embed/aTgXgN9fOsk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

- The `title` will be: `(Charlie Puth) Attention`  
- The `artist` will be:  `Josephine Alexandra | Fingerstyle Guitar Cover`  
It is still not perfect yet but it is quite enough for me  

`ytdl2` is just a reverse in order of `ytdl`, should be used for cases like in this video:  
<iframe width="560" height="315" src="https://www.youtube.com/embed/tAKnT0g8MtA" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>  

## Play music with `cmus`
<hr>
**1. Installation**  
Again with magic of `Homebrew`, to install `cmus` on Mac is simply:  
`brew install cmus`
The first installation, I've accidentally downloaded and compiled the build on `https://cmus.github.io/#download`. But still `Homebrew` installation is much better.  

**2. Usage**  
`cmus` is a very useful player when you mostly work on Terminal. Its UI has 7 views (that you can switch around using number key 1-7 once cmus open):  
![](/images/2019Mar/cmus-views.png)

If you are used to use terminal, it won't be that hard to use `cmus`. But to make it easier to remember the list of commands, I recommend this [cheatsheet](https://www.cheatography.com/stephenjjohnson/cheat-sheets/cmus/pdf/)  
As of the 2 video above, here is what it will look like after we've added them in playlist:  
![](/images/2019Mar/cmus-playlist.png)  

One thing I definitely loved about `cmus` is that the navigation keys are `j`, `k` for up and down which is super familiar if you used `vim`. So again, everything is seamless.  

Hope you will find this useful if you are also late to the game :)  



