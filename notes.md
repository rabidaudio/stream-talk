var fs=require('fs'), lame=require('lame'), Speaker=require('speaker')

make sure slides open, full screen

2 terminals

sublime

Before I sart, I want to ive you some context. this is a talk I was going to give last week
at the Atlanta node.js meetup, but i couldn't due to some logistical issues, so Im going
to try for next month. So this is aimed at software developers from atlanta who work with
or are curious about Node.js. I'm going to follow their conventions, which might break some
of your suggestions on proper presentation techniques. for example, I'm going to stand behind
the podium the whole time so that I can have easy access to run demos

Streams
are a useful tool for development with node

Node recently came out with a new stream system, so I, going to talk about the old streams
and the new streams, along with some demos

take advantage of Javascript's asynchronous, event-driven architecture
using streams will make your code easier to follow, and a lot of networking tasks
require it. TCP for example. and there are some really cool modules that are available
now, including for the browser, that i will mention at the end


but if you have experience with events in javascript, streams should be pretty easy

!

The concept of pipes is ancient, from the early days of terminals. Duglas McIlroy
came up with the concept, and in 1973 Ken Thompson threw it into Unix.

What originally turned me into a linux convert was a terminal that was actually
useful, and pipes are one of the core features of this. Mac users have the bash
shell as well, so everything here applies there as well.

The design concept is that all of these components are modular, and act
independently without knowing how they will be used, and  can be combined
in different ways to accomplish very different tasks

that's a very node-like concept as well, where every module is as small and specicallized
as it can be, and creating programs is simply combining modules together with a small amount
of glue

!

examples

I'm sure none of you are strangers to the console, but just in case I'm going to talk about pipes
and give some examples

ls will give me a list of all the programs in my program directory, and grep will search through a
list of items for those that match. so these are two programs that act independantly that i'm
combining together

    ls /bin | grep users

    wget -qO- http://www.prism.gatech.edu/~cknight7/dictionary.txt.gz | gzip -c | grep -e '^a' | wc -l 

here is a bigger example. I have a compressed version of a dictionary out on my prism drive that is a 
huge list of enlish words. by putting streams together in the right way, I can for example figure out
how many words start with 'a'. this is a kind of rudementary program, but you can see how through
encapsulation and chaining you can do just about anything

!

node streams

so let's talk about nodes implementation of pipes.
its all about these streams of data
here are the different types of streams
the Transform one is under-documented and outside the scope of this talk, but you should check it
out if you are curious

Readable- e.g. files
fs.readFile and fs.readFileSync read the whole file into memory, then return it
even the asynchronous version does this

So fs.fileRead and fs.fileReadSync buffer the entire contents of the file into memory, and then pass that
back (to the return value or callback, depending on which you use). But fs.createReadStream reads a file
asynchronously in chunks. You can let it decide how large a chunk you want to use by putting a function
on data, or you can specify the amount of data you want by using readable.read(bytes), or omit the byte
count to read everything from the buffer. 

Readable
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

```javascript

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


pause is a suggested method, but not neccessarily implemented. this can be very confusing


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

Methods like `pause()`, `resume()`, or an event like `on('data')` or `on('end')`

It is possible to specify during creation of these objects that they take in and spit out
arbitrary objects

Lets rewrite our line counter to use new streams

```javascript
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

```javascript
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



sources & further reading
--------------------------

- [http://doc.cat-v.org/unix/pipes/](http://doc.cat-v.org/unix/pipes/)
- [http://en.wikipedia.org/wiki/Douglas_McIlroy](http://en.wikipedia.org/wiki/Douglas_McIlroy)
- [http://en.wikipedia.org/wiki/Pipeline_(Unix)#History](http://en.wikipedia.org/wiki/Pipeline_(Unix)#History)
- [http://blog.nodejs.org/2012/12/21/streams2/](http://blog.nodejs.org/2012/12/21/streams2/)
- [https://github.com/substack/stream-handbook](https://github.com/substack/stream-handbook)