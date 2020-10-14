Sierra Creative Interpreter (SCI) Resource Viewer
=================================================

I recently found myself with some .P56 files I wanted to view, but couldn't find anything to get the job done on my Mac. I hopped on to my Windows machine and found a few tools, but it seemed my files were using an obscure specification they didn't support. Finally, I happened upon the unassumingly named "SCI Viewer" at [SCIprogramming.com](http://sciprogramming.com/scitools.php?id=2) and lo and behold, I was able to view my files. And even better, there were CLI tools included with it, too!

I looked around for source code, [but sadly it seems it was never released](http://sciprogramming.com/community/index.php?topic=272.0). No one even says what the author's name was, they're just ["The creator of SCI Viewer"](http://sciprogramming.com/community/index.php?topic=423.0). The [SCI Tools](http://sci.sierrahelp.com/Tools/SCITools.html) page, which seems to be broken down by tool author, doesn't credit anyone. Their [GeoCities page](http://web.archive.org/web/20080413035345/http://www.geocities.com/sciviewer/) is in the Wayback Machine, but sadly, they only posted their e-mail address as an image in order to avoid spam, and it didn't make it in the archive. So despite my best efforts, all I have to convert my files are these Windows binaries. How can I run those on my Mac?

Luckily, [there's a Docker image for that](https://hub.docker.com/r/scottyhardy/docker-wine/). These seem to function just fine with Wine, so I've built a Dockerfile to download them and make them easy to run. I also tried to extract as much documentation as I could for these utilities, which has all been made available in the [help](help) folder. All of my needs were met by the [Graphics.exe](help/Graphics.txt) utility, but all 12 utilities are available in this image.

Examples
--------

#### Build

```
docker build -t sci-viewer .
```

#### Bash shell
Wine can take some time when it is run the first time, so it can be more efficient to perform batch operations inside the container.
```
docker run -it --rm -v "$(pwd)/:/home/wineuser/output" sci-viewer bash
```

#### Convert P56 file
Note that Windows programs expect backslashes, which must be escaped... with a backslash.
```
docker run -it --rm -v "$(pwd)/:/home/wineuser/output" sci-viewer wine graphics -o output output\\PIC.P56
```
