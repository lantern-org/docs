# Self-Hosted Guide

I'll try to make this painless.



## General system overview

### ELI5

There are three parts to the overall system:
1. the data **forager**
2. the data **preserver**
3. the data **eater**

The **forager** is (presumably) _you_.
You have a cell-phone with an internet connection and a GPS signal.
You send your real-time location to the **preserver**.

The **preserver** is like a refrigerator.
It takes incoming food (GPS location data), and keeps it nice and cool and safe.
Until other people, the **eater**s, come along for a snack.

The **eater** _consumes_ the food (GPS data).

This sounds nice and easy, but we need to actually implement these ideas.

### technical implementation

Our "forager" goes on their bike ride with an **android app** running in the background of their cell phone.
The app collects GPS data, packages it up, and sends it to the "preserver".
The preserver is a **server** -- it's a bare-metal, or virtual, computer running the program that collects the forager's data.
The preserver also needs to let outsiders, the "eaters", actually view these data.
So the server plays a dual role.
Ideally, this dual role would be split between two computers that handle separate connections (TODO, for future).
One server should be just fine for a single forager and a few eaters.
The "eaters" are regular people that go to a **website** and view where the forager is currently located.

Cool.
Now we have the three parts we have to set up -- an **app**, a **server**, and a **website**.
Unfortunately, there's more.
Each of these parts requires extra steps and services to get working.

### dependencies

The **app** requires a cell-phone with a data connection and a GPS signal.
Estimated data usage:
```txt
32 bytes / packet
10 packet / second
60 second / minute
60 minute / hour
1/1024 kilobyte / byte
= 1125 kb/hr
~ 1 mb/hr
```
I'd imagine most data plans can handle this, but maybe I'm just privileged.

Now, the GPS on your phone cannot take 10 recordings per second.
Your GPS watch, however, may be able to (TODO -- link ref to Garmin).
In the future, the app may be able to connect to your watch/computer and broadcast that data.

The app also requires you to configure a server connection.
This simply means you'll need to enter a URL (or IP-address) to which the app will send data.
This URL (or IP-address) should be pointing to the **server** that will preserve our data.
Speaking of.

The **server** requires a computer or cloud-hosted virtual computer.
It also requires the underlying computer to be connected to the internet.
It also should have a URL pointing to the IP-address of the computer's internet connection.
It also should have a network proxy inbetween the internet and the actual code (TODO -- server-side handle this).
That's a lot of stuff.

The **website** needs to be hosted _somewhere_.
It can be hosted on the same server, or a different server.
It has to have a URL pointing to it -- this URL can be the same as the server's URL (if you give it one).
For the website to work, you need to place our javascript script somewhere in your webpage.
We'll have a super-easy HTML code snippet you can copy-paste right onto your site:
```txt
<!-- it'll roughly look like this -->
<div id="Lantern"></div>
<script src="https://raw.githubusercontent.com/lantern-org/frontend-api/main/api.js"></script>
```

So here are our dependencies -- data plan, GPS connection, URL, internet-connected computer.

### finale

Pain list (increasing pain order):
1. phone -- you probably have this already; needs data and GPS
1. app -- you install the app on your phone, and type in a URL
1. URL -- $10/mo, may have to set up DNS entry
1. server
   1. exposing to the internet
   1. website
   1. data consumer code

## Setting it up

I'm planning to make this as easy as possible.

On that end, we suggest using DigitalOcean to host your server.
A simple Droplet (cloud-hosted computer) is $5/mo, and will work well enough.
To make it as easy as possible, we will provide:
- an image to base your Droplet containing:
   - NGINX proxy
   - server-side code that ingests and publishes data
   - simple configuration with how-to
   - pre-installed website
- instructions to buy a domain and configure it to point to your Droplet

then all you'd have to do is download the app and enter your URL (or IP address)

steps follow.

### obtaining a server


