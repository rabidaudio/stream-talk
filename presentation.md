<style>
h2{
    font-size: 1.1em;
}
code {
    color: rgb(0, 174, 108);
    font-size: 0.8em;
}
a{
    color: darkcyan;
}
</style>

Streams in Node.js
==================

the old, the new, and the why
-----------------------------

Charles Julian Knight

`@charlesjuliank`

!

Intro
-----

Why you care:

- Elegant, powerful code
- Easy to follow
- Many modules use it

!

Concept: pipes
==============

> *"We should have some ways of coupling programs like
> garden hose -- screw in another segment when it becomes when
> it becomes necessary to massage data in another way.  
> This is the way of IO also."*

-- Douglas McIlroy, 1964

!

Some examples
=============

!

Node Streams
============

- Readable
- Writable
- Duplex

!

Readable
========

`fs.readFileSync`, `fs.readFile` vs `fs.createReadStream`

`http.IncomingMessage`

!

Writable
========

`fs.createWriteStream`

`http.ServerResponse`

!

Duplex
======

Both worlds!

`net.Socket`

!

old streams
===========

Readable
--------

- `on('data')`
- `on('end')`
- `read()`
- `pause()`
- `resume()`
- `unshift()`

!

Old streams
===========

Writable
--------

- `on('drain')`
- `on('finish')`
- `write()`
- `end()`

!

Let's try it!
-------------

!

Problems
========

- data comes immediately
- `pause()` doesn't pause
- handoff is difficult

!

Streams2
========

new event: 'readable'

`pause()`, `resume()`, `on('data')` revert to old functionaility

Allows for arbitrary Objects as well as Buffers and Strings

!

Refactor!
=========

!

Another example
===============

MP3 Player
----------

- [`speaker`](https://www.npmjs.org/package/speaker) - takes in a stream of PCM audio data and plays it through the speakers.
- [`lame`](https://www.npmjs.org/package/lame) - Encodes/decodes MP3's to/from raw PCM formats.

!

sources & further reading
--------------------------

- [http://doc.cat-v.org/unix/pipes/](http://doc.cat-v.org/unix/pipes/)
- [http://en.wikipedia.org/wiki/Douglas_McIlroy](http://en.wikipedia.org/wiki/Douglas_McIlroy)
- [http://en.wikipedia.org/wiki/Pipeline_(Unix)#History](http://en.wikipedia.org/wiki/Pipeline_(Unix)#History)
- [http://blog.nodejs.org/2012/12/21/streams2/](http://blog.nodejs.org/2012/12/21/streams2/)

thanks
------
  
[@substack](http://twitter.com/substack)  
[@dominictarr](http://twitter.com/dominictarr)  
[@tootallnate](http://twitter.com/tootallnate)
[MarkdownPresenter](https://github.com/jsakamoto/MarkdownPresenter)

contact
-------

`@charlesjuliank`  
[github.com/rabidaudio](https://github.com/rabidaudio)

slides & notes
--------------
[SpeakerDeck](https://speakerdeck.com/rabidaudio/node-streams)
[GitHub](http://github.com/rabidaudio/stream-talk)