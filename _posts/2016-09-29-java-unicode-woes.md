---
layout: post
title: "Java8 Unicode Woes"
date: 2016-09-29 22:15:00
categories: intro 
featured_image: /images/cover.jpg
---

## A First Look Into Encoding
Based both on work I've been doing lately at HubSpot out in Cambridge, 
and a friends mission to write a proposal for a train caboose emoji, I found myself 
learning more about what makes unicode tick. I've spent some time dealing with the 
Supplementary Multilingual Plane (SMP) and how these ```multi-chunk``` 
characters are interpreted in Java.

The SMP houses some infrequently used characters (ancient Greek symbols, Mahjong
tiles, playing cards, etc...), and has been off of our radar when handling
strings provided by our customers until recently.

//TODO: Talk about SMP Emoji 

## Surrogate Pairs
Turns out, in order to encode characters in the SMP, more than 8 bytes are used.
These characters are split into pairs of 8 byte chunks, that together can
produce a unicode 'meaning' of sorts

//TODO: Bytes example of (women+man => Family)

## Practically Speaking...
But, how does all of this really apply when we're writing our code? A good
example of where this could come up to bite us is when a client side String
cannot be completely validated on the front-end of an application. 

Say a website crawler is attempting to fetch pages at a given domain, process
them in some manner, and store a titled web archive for a page or page set. We
may want to trim off the title of a page at 200 characters to fit in a DB
column, where the ower of this domain can make the title of their pages as long
as they want. 

If we were to truncate the title of a given web page to 200 characters using
```String.concat```, or ```StringUtils.left``` and our String was say, 199 a's followed
by our friend, left tilted magnifying glass Emoji, we would have trouble. Since
these surrogate pairs count as multiple characters in an encoded String as far
as Java is concerned, the character value will be chopped in half! The first
bytes of the Emoji don't mean a thing without the other half, and would cause a
confused customer in this case...

Sure, it's simple to write a util using ```String.isLowSurrogate``` to check for
these pairs and truncate them one character earlier. The thing is, I havent
found a single utility to do this for me! Not in StringUtils, Guava, or our
internal utils. I would be interested to see if there is a util for String
truncation in java that takes surrogate pairs into account. Drop me a line if
you come across anything!

## Not _Safe_ to Ignore Anymore
The world is only going to be more saturated with cry-laughing, heart-eyed Emoji
and whatever else is stuck in this plane. If you're storing client-side input in
a format that accepts SMP, surrogate pair Strings, 😍😍😍 make sure to keep an eye out
😍😍😍
