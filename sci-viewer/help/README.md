_Partially reproduced from http://web.archive.org/web/20080413062522/http://www.geocities.com/sciviewer/faq.htm._

CLI Tools
---------

#### General

There's a bunch of tools based on the same engine that allows batch "disassembling" of SCI resources. Each tool digs through the specified resource, prints it's findings to standard output in XML format and saves individual resource components (if any) as separate files. Run a tool without parameters to see it's usage information. In most cases you need to provide only an appropriate resource name. It may be necessary to specify additional parameters for better results (i.e., a palette for view resource that does not have an embedded one; a patch for music sound tracks; etc.).

Program | Works with...
--------|--------------
[exe.exe](Exe.txt)|Interpreter files (sciv.exe, sierra.exe...).
[resource.exe](Resource.txt)|Resource archives, CD-audio tracks.
[graphics.exe](Graphics.txt)|View, picture, cursor, palette and CLUT resources.
[code.exe](Code.txt)|Script and heap resources.
[text.exe](Text.txt)|Text resources.
[music.exe](Music.txt)|Sound and patch resources.
[vocab.exe](Vocab.txt)|Vocab resources.
[font.exe](Font.txt)|Font resources.
[audio.exe](Audio.txt)|Audio and CD-audio resources.
[sync.exe](Sync.txt)|Sync resources.
[message.exe](Message.txt)|Message resources.
[video.exe](Video.txt)|Sequence, robot, VMD and duck resources.

#### Sample script

The script below is an example of batch resource processing. It's definetely not perfect but you may use it as a start. Save it to a .cmd file, copy game files into a single directory on your hard drive and run the script. It will create a tree under "Resources" directory and extract resource _components_ there (individual _resources_ will be extracted in the current directory). Make sure that you set your PATH environment variable to include the directory with CLI tools.

```batchfile
SET TARGETDIR=.\\Resources

REM Create directory tree

mkdir %TARGETDIR% > NUL
mkdir %TARGETDIR%\\View > NUL
mkdir %TARGETDIR%\\Pic > NUL
mkdir %TARGETDIR%\\Cursor > NUL
mkdir %TARGETDIR%\\Palette > NUL
mkdir %TARGETDIR%\\CLUT > NUL
mkdir %TARGETDIR%\\Script > NUL
mkdir %TARGETDIR%\\Text > NUL
mkdir %TARGETDIR%\\Vocab > NUL
mkdir %TARGETDIR%\\Sound > NUL
mkdir %TARGETDIR%\\Font > NUL
mkdir %TARGETDIR%\\Audio > NUL
mkdir %TARGETDIR%\\Sync > NUL
mkdir %TARGETDIR%\\Message > NUL
mkdir %TARGETDIR%\\Video > NUL


REM Unpack resource archives into the current directory

for %%i in (\*.chk resource.0?? resource.alt altres.??? resource.msg ressci.???) do resource.exe %%i >%%~nxi.xml
for %%i in (audio???.??? resource.aud resource.sfx) do resource.exe %%i >%%~nxi.xml


REM Process unpacked resources

for %%i in (view.??? \*.v16 \*.v56) do graphics.exe -o %TARGETDIR%\\View %%i >%TARGETDIR%\\View\\%%~nxi.xml
for %%i in (pic.??? \*.p16 \*.p56) do graphics.exe -o %TARGETDIR%\\Pic -v %%i >%TARGETDIR%\\Pic\\%%~nxi.xml
for %%i in (cursor.??? \*.cur) do graphics.exe -o %TARGETDIR%\\Cursor %%i >%TARGETDIR%\\Cursor\\%%~nxi.xml
for %%i in (\*.pal) do graphics.exe %%i >%TARGETDIR%\\Palette\\%%~nxi.xml
for %%i in (\*.clu) do graphics.exe %%i >%TARGETDIR%\\CLUT\\%%~nxi.xml

for %%i in (font.??? \*.fon) do font.exe -o %TARGETDIR%\\Font %%i >%TARGETDIR%\\Font\\%%~nxi.xml

for %%i in (script.??? \*.scr \*.hep \*.csc) do code.exe -o %TARGETDIR%\\Script %%i >%TARGETDIR%\\Script\\%%~nxi.xml

for %%i in (vocab.??? \*.voc) do vocab.exe %%i >%TARGETDIR%\\Vocab\\%%~nxi.xml

for %%i in (text.??? \*.tex) do text.exe %%i >%TARGETDIR%\\Text\\%%~nxi.xml

for %%i in (\*.msg) do message.exe %%i >%TARGETDIR%\\Message\\%%~nxi.xml

for %%i in (sound.??? \*.snd patch.??? \*.pat) do music.exe -o %TARGETDIR%\\Sound %%i >%TARGETDIR%\\Sound\\%%~nxi.xml

for %%i in (\*.aud \*.cda @???????.??? a???????.???) do audio.exe -o %TARGETDIR%\\Audio %%i >%TARGETDIR%\\Audio\\%%~nxi.xml

for %%i in (\*.syn #???????.??? s???????.???) do sync.exe %%i >%TARGETDIR%\\Sync\\%%~nxi.xml

for %%i in (\*.seq \*.rbt \*.vmd \*.duk) do video.exe -o %TARGETDIR%\\Video %%i >%TARGETDIR%\\Video\\%%~nxi.xml
```
