# JCR Quick Link

JQL is a file format meant for debugging or quick linking for my JCR6 based builders.
It's basically nothing more but a text files pretending to be a JCR6 file, and when the JQL driver is loaded, JCR6 won't ever be the wiser.



Below is an example of the script I used to test the C++ version of the JQL loader
~~~
JQL

RAW:TestJQL.cpp>TestJQL.cpp
RAW:E:/dev_tools/BlitzMax/NG/Mac/docs/html/Modules/commands.txt>Commands for BlitzMax NG.txt

TEXT:Welcome.txt
Welcome to this silly test file that is part of a JQL file!
Does this all work properly?
@END@

FROM:E:\Projects\Applications\Slyvina\Tools\Scyndi\Test\Source\TestProperty.STB
STEAL:Translation.lua>Scrippie.lua
END
~~~
That file was also tested with NJCR giving this result
![image](https://user-images.githubusercontent.com/11202073/211562434-917d8d74-57e0-455c-bdfc-1d136814264a.png)

(NOTE: Now NJCR has been discontinued and been replaced by the Jalondi tool. Jalondi should by the way produce similar output).

Basically every line has one command. And it all just links things up.
One note. Although JQL parsers SHOULD be compatible with CRLF based text formats, it's better to use LF based text formats only.


Now every JQL file must start with "JQL". JCR6 has to quick scan all files to see what format it is so it can activate the correct driver. The JQL line takes care of that.

And here the command overview
### RAW
Import a "raw" file (that means from the underlying real file system) and make that a JCR entry
- Syntax: RAW:Filane>EntryName

### TEXT
Next lines will just be taken as actual entry data as were it a packed file until "@END@" is encountered.
- Syntax: TEXT:EntryName
- Note: @END@ does not have to be on a line on its own. You can also put it at the end of the last line, but making it have a line for itself is often the best way to go.

### NOTES
Will attach the text as a "Notes" to all upcoming raw files
- Syntax: NOTES:Notes

### AUTHOR
Will attach the text as "Author" to all upcoming faw files
- Syntax: AUTHOR:Author

### FROM
Will define from which other JCR6 file (or any other kind of file JCR6 can load through the present drivers) the STEAL command will "steal" from
- Syntax: FROM:JCR6File

### STEAL
"Steals" an entry from another JCR6 resource and makes it its own.
- Syntax: STEAL:EntryInOriginalFile>EntryInThisResource

- WARNING! There is NO protection in any way against fools trying to make this operation go cyclic, causing an infinite loop and a stack overflow in the process. Hopefully you are not such a fool!
- Note! Both STEAL and FROM were only implemented in version 23.01.10 for the JQL drivers. Please make sure the application you use to read JQL files was built from one of those.

### COMMENT
Adds a comment to the resource. The next lines will serve as a comment until "@END@" is encountered. Unlike "TEXT" it DOES need a line for its won here.
- Syntax: COMMENT:CommentName

### PATCH
Adds the content of another JCR6 resource and patches that in
- Syntax: PATCH:ResourceToPatch

- WARNING! There is NO protection in any way against fools trying to make this operation go cyclic, causing an infinite loop and a stack overflow in the process. Hopefully you are not such a fool!

### OPTIONAL
When given any non-existent raw file will be ignored (standard)
- Syntax: OPTIONAL

### REQUIRED
When given any non-existent raw file will cause an errror
- Syntax: REQUIRED

### PLATFORM
Denotes stuff must only be done on specific platforms. When the command "PLATFORM:Windows" is given, the next lines will only be processed when on Windows, until the next PLATFORM command is found. 
When "PLATFORM:Linux" is given, then the lines will only be processed on Linux.
When "PLATFORM:All" is given, then the used OS/Platform no longer matters.
As JQL is only meant for quick linkups it's by far more reliant on the underlying filesystem than a regular JCR6 file, but as file systems can differ a lot in base structures this can work very irritating when JQL is used cross-platform, hence the implementation of this command.
- Syntax: PLATFORM:PlatformName (Windows/MacOS/Linux/All)

### END
Ends the reading of JQL
- Syntax: END

Note: When missing and the end of the file is encountered, it will just act the same way as if the command were there. If you use this command and type anything after that, it will simply be ignored.




Now if your JQL file requires any comments that JQL should ignore, just give them a line from themselves and prefix them with "#"


