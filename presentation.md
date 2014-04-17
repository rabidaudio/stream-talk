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
    font-size: 0.8em;
}
</style>

Streams in Node.js
==================

the old, the new, and the why  
-----------------------------  

Charles Julian Knight  
[`@charlesjuliank`](http://twitter.com/charlesjuliank)

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

- stream.Readable
- stream.Writable
- stream.Duplex
- stream.Transform

!

Readable
========

Examples
--------

- `fs.createReadStream`
- `http.IncomingMessage`

Implement
---------

```javascript
util.inherits(myStream, stream.Readable);
myStream.prototype.read = function() ...
```
!

Writable
========

Examples
--------

- `fs.createWriteStream`
- `http.ServerResponse`

Implement
---------

```javascript
util.inherits(myStream, stream.Writable);
myStream.prototype.write = function() ...
```

!

Duplex
======

Both worlds!

Example
-------

`net.Socket`

Implement
---------

```javascript
util.inherits(myStream, stream.Duplex);
myStream.prototype.read = function() ...
myStream.prototype.write = function() ...
```

!

Old streams
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

- Implemented in `v0.10`
- new event: 'readable'
- `pause()`, `resume()`, `on('data')` revert to old functionality
- Allows for arbitrary Objects as well as Buffers and Strings

!

Refactor!
=========

!

And now for pipes
===============

MP3 Player
----------

- [`speaker`](https://www.npmjs.org/package/speaker) - takes in a stream of PCM audio data and plays it through the speakers.
- [`lame`](https://www.npmjs.org/package/lame) - Encodes/decodes MP3's to/from raw PCM formats.

!

contact
-------

[charles@rabidaudio.com](mailto:charles@rabidaudio.com)

thanks
------
  
[`@substack`](http://twitter.com/substack)  
[`@dominictarr`](http://twitter.com/dominictarr)  
[`@tootallnate`](http://twitter.com/tootallnate)  
[MarkdownPresenter](https://github.com/jsakamoto/MarkdownPresenter)

slides & notes
--------------
[SpeakerDeck](https://speakerdeck.com/rabidaudio/node-streams)  
[GitHub](http://github.com/rabidaudio/stream-talk)