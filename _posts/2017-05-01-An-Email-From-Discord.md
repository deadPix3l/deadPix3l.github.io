---
color: 7289da
---
So, I decided to make a custom discord client (and that may or may not come out ever, its still only in planning, I have no idea if I can do it/ will maintain interest). 
At first I thought I might have to reverse engineer it. some GDB, some Strace, maybe even digging in the x86. 
I'll probably still do that. Maybe. But first, I guess open source research should be tried.
I did some googling on what kind of Crypto I need to use, how are messages stored, can they view the text conversations on the server or is it encrypted? (spoiler: they can, its not)
My googling turned up few vague results, mostly just forums of people speculating, a few posts from mods giving minor details. But I wanted more so I emailed discord, and expected no results. 
At most I plainly expected a "we can't release that information", but what I got was more than that. So here it is, uncut:

>This is obviously complex, but since you asked I will explain everything. I should mention that we take this seriously - and not in a lip service kinda way.
We have spent a serious amount of time and energy implementing what’s considered industry best practices for a chat service. 
So the details: communication between our client and gateway servers is encrypted using HTTPS or WSS which uses TLS. In the browser, 
voice communication is powered by the WebRTC code built into Chrome and Firefox which is encrypted using DTLS. 
Our desktop and mobile apps use xsalsa20-poly1305 as implemented in https://github.com/jedisct1/libsodium. 
Our gateway endpoints are proxied behind CloudFlare, which provides hardware level DDOS mitigation. 
Our voice servers are not behind CloudFlare’s DDOS mitigation hardware in order to minimize latency, 
but due to their transient nature users can easily migrate to a new active voice server from their server’s settings if an issue arises. 
Voice data is not stored in any way, it is read into memory and then relayed to connected users. 
Message transmission is very similar to Telegram’s cloud chats or Slack - all data is transported to our servers and back to clients using the above mentioned industry standard security practices. 
Also similarly to Slack, our chat messages are stored unencrypted on our servers so that we’ll be able to offer quick search & indexing. 
Our databases are currently stored on virtual machines hosted by Google’s GCE which is a reputable virtual host that provides good CPU and Bandwidth characteristics for a service like Discord.


So for all you tinfoil hats out there, lets recap:
- they SAY voice data isn't stored. you can trust them or not. Your call.

- they ADMIT that text chat is stored in the clear, and is indexed.
This could mean your message may be used for advertising, quality control, or any other purpose.
That's all just assumption, but whats clear: your messages are not private.

- those messages are stored on VMs hosted by google.
Which lets be honest, they know everything about you already. 
So probably for the best its google and not someone else. 
But again, where you put your trust, thats up to you.

- the voice data uses xsalsa20. I'm a crypto nerd, but shamefully, I'll admit I don't know much about salsa. 
I know its roughly related to ChaCha, but as for its security. No idea. 
If anyone has any interesting articles/proofs of cryptanalysis against salsa20 or how it stacks up against other standards like AES and RSA, I'd love to see them.


Anyways, That's all for now. Happy hacking.