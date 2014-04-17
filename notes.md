
Streams 
are a useful tool for development with node
take advantage of Javascript's asynchronous, event-driven archetecture


The concept of pipes is ancient, from the early days of terminals. Duglas McIlroy
came up with the concept, and in 1973 Ken Thompson threw it into Unix.

What originally turned me into a linux convert was a terminal that was actually
useful, and pipes are one of the core features of this. Mac users have the bash
shell as well, so everything here applies there as well.


The design concept is that all of these components are modular, and act
independantly without knowing how they will be used, and  can be combined
in different ways to acomplish very different tasks



    ls /bin | grep user

    ls | wc -l

    wget -qO- http://www.prism.gatech.edu/~cknight7/dictionary.txt.gz | gzip -c | grep -e '^a' | wc -l 


Readable- e.g. files
fs.readFile and fs.readFileSync read the whole file into memory, then return it
even the asyncronous version does this

So fs.fileRead and fs.fileReadSync buffer the entire contents of the file into memory, and then pass that
back (to the return value or callback, depending on which you use). But fs.createReadStream reads a file
asynchronously in chunks. You can let it decide how large a chunk you want to use by putting a function
on data, or you can specify the amount of data you want by using readable.read(bytes), or omit the byte
count to read everything from the buffer. 


Readable-
events data and end are where the magic happens
you can use read() to read directly from the stream as well
pause and resume do what you think
unshift allows you to put data that has been read back into the buffer

Writeable-
uses write() method as it can, end() when done.
has events like finish (and others like error and pipe/unpipe that i've left out here)
drain event: it is possible that the buffer accepting data from writable will fill up
and write() will return false. if that happens, the drain event will be called when things
free up again

Duplex- methods from both 


Let's make that simple `wc -l` command in node

```linecount.js

#!/usr/bin/env node
var lines = 0;

process.stdin.on('data', function(data){
    var linebreaks = data.toString().match(/n/g;)
    if(linebreaks)
        lines+=linebreaks.length;
});

process.stdin.on('end', function(){
    process.stdout.write(lines+'\n');
});

```



The pause() method doesn't pause. It is advisory-only. In Node's implementation,
this makes things much simpler, but it's confusing to users, and doesn't do what it looks like it does.

'data' events come right away (whether you're ready or not). This makes it unreasonably difficult
to do common tasks like load a user's session before deciding how to handle their request.

There is no way to consume a specific number of bytes, and then leave the rest for some other part
of the program to deal with.

It's unreasonably difficult to implement streams and get all the intricacies of pause, resume,
write-buffering, and data events correct. The lack of shared classes mean that we all have to
solve the same problems repeatedly, making similar mistakes and similar bugs.


NEW STREAMS

Methods like `pause()`, `resume()`, or an event like `on('data')`

It is possible to specify during creation of these objects that they take in and spit out
arbitrary objects


The new way:

```linecount.js
#!/usr/bin/env node
var lines = 0;

process.stdin.on('readable', function(){
    var data = process.stdin.read();
    if(data===null){
        process.stdout.write(lines+'\n');
        return;
    }
    var linebreaks = data.toString().match(/n/g);
    if(linebreaks)
        lines+=linebreaks.length;
});

Another example

I've been working on a raspberrypi MP3 player which will eventually be embedded in my car. I orginally started
the project in Python, but trying to read button presses from the hardware and alter playback became such a headache
that I switched over to Node. There were no (good) audio playback libraries on NPM, but all the low-level requirements
were there.

    - [speaker](https://www.npmjs.org/package/speaker) - takes in a stream of PCM audio data and plays it through the speakers.
            Interfaces directly with OS sound libraries (C/C++ wrapper)
    - [lame](https://www.npmjs.org/package/lame) - Encodes/decodes MP3's to/from raw PCM formats. (C/C++ wrapper)

So getting audio to play was as simple as

```js
var fs = require('fs');
var Speaker = require('speaker');
var lame = require('lame');

var speaker = new Speaker({
    channels   : 2,
    bitDepth   : 16,
    sampleRate : 44100 
});
var decoder = new lame.Decoder();

var song = fs.createReadStream('wrek.mp3');
song.pipe(decoder).pipe(speaker);
```


look at event-stream
and substack's stream-handbook for awesome other streams you can do



sources
-------

http://doc.cat-v.org/unix/pipes/
http://en.wikipedia.org/wiki/Douglas_McIlroy
http://en.wikipedia.org/wiki/Pipeline_(Unix)#History
http://blog.nodejs.org/2012/12/21/streams2/

contributors
------------

@tootallnate
@substack
@dominictarr

https://github.com/jsakamoto/MarkdownPresenter