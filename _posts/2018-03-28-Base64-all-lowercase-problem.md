---
title: Help! my base64 lost its case!
color: b030c0
tags: walkthrough problem solution base64 data-recovery
---

This is a problem I encountered today, and the solution I came up with. This is
not a perfect solution, but it may help if you encounter this problem.

A chunk of data was sent from point A to point B. Along the way, I received a
copy of the data (so I guess that makes me Eve in this scenario). But there was
one small catch. The means through which it was intercepted is not case
sensitive. And the data was base64'd, so case was very important to the data.
So now I have a bunch of base64 which has been converted to all lowercase, and
it's up to me to reconstruct as much as I can of the original data. But how can
I go from:
```
cm9vddohoje2nzm2oja6otk5otk6nzo6ogpkywvtb246kjoxnje3ngoja6otk5otk6nzo6ogpiaw46k
joxnje3njowojk5otk5ojc6ojokcwexm6kjoxnje3njowojk5otk5ojc6ojokc3luyzoqoje2mtc2oj
a6oqotk5oto3ojo6cmdhbwvzoio6mtyxnzy6mdo5otk5oto3ojo6cm1hbg
...
```
to figuring out what it was originally?

There is some analysis we can make to make this more accurate, but also harder.
Or if the basic structure of the data is all we need rather than accuracy, we
can use a naive approach to make the code easier. I didn't need to know what it
was with perfect accuracy. I just need to know roughly what file format it is.
I chose the naive approach, but lets do the analysis anyways.

Of all the base64 characters, there are 12 characters, the numbers and the
special characters, which have no case. These characters we can be sure are
correct. Of the rest, the alpha characters, there are only 2 states, upper and
lowercase. The only bits we are unsure of are the ones that change between the
case. This can be simplified to `A ^ B`. This can be used to create a bitmap of
which bits we need to play with, versus which ones can be proven correct. This
sounds complicated, but it could probably be done in a few hours. If you make
it work, please email me. I'd love to see it.

So lets do the naive approach. Base64 can be split into 4 character chunks. Four
base64 characters makes up 3 bytes. We can figure these blocks out independent 
of the others (which means it can be done in parallel if you wish). Each block
has 4 characters, and each character can have 2 states. this means each block
can have `2**4` states, or less if there's numbers or symbols. This is easily
manageable. For each of these states, we can decode them as base64, producing 3
bytes. We can assign these scores based on how correct this set of bytes seem,
and take the highest scoring state as correct. If the original data is
unstructured, then scoring means nothing. We cannot figure it out.
BUT if the data is in English, or any other ASCII language (I'm sure this can
also apply to multi-byte languages like Chinese or Japanese, but I don't know how
I would go about that), or a structured format like JSON, CSV, conf files, or
source code, it can be scored. Because we are scoring 3 byte blocks, I chose to
assign blocks a score out of 300, but that's up to you. I decided to assign
higher score values in this order (but it all depends on your file: if you have
a clue what the file might be, use that to guide your scoring preferences):
```
letters (case insensitive): 100
numbers: 75
punctuation: 60
programming symbols ("\[]{}()$#/<>"): 50
whitespace: 45
other printable chars: 10
non printable binary chars: 0
```
These values can be tweaked as needed until results are satisfactory.

And doing this, I got results like this:
```
root:!:16736:0:99999:7::8jdaemon:*:16174j#k-+-+s; > 0#9sc{sc9j#+-+#s#:G0ys:*:
16176:0:99999:7:::$sync:*:16176:0:9-+-;z#;&tppW:";-assc;-+-;z#;&pF
```
It's pretty rough, but it looks like the /etc/passwd or the /etc/shadow file.
And thats good enough for me.
