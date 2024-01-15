# ideas 

https://github.com/can3p/kleiner is designe dto work with Github for CLI goalng programs.

Its create because it gives golang devs exactly what they need without all the setup.

What follows are ideas for extensions to this further:

## GUI

Currently, kleiner works for golang CLI for TUI and Server use cases, but what about GUI ?

Each OS has a custom way to do this and needs custom packaging and also has custom self updating approaches that are designed for Devs using Chocelaty ( https://chocolatey.org ) , Brew, etc. But we need an approahc for end users too.

We can easily fix this by building the self updating into the bianry. 

Zenity ( https://github.com/ncruces/zenity ) can do this.
- write a package that conforms to the current kleiner self update APi interface but has a GUI.
- Only runs when the binary is opened, and so no need for anything else to be on a users computer

## Signing

Because of softwware viruses and MITM atatches like Solar Winds, etc we need a way to ensure that the binary IS the binary from the source code.

For Mac you have to sign anyway. For Non GUI its just a Notary. Later can get fancy and scaffold al the App stroe crap Devs need.

For Windows lets see wht is the "standard way" to sign.

For Linux, we can use whatver we want because our packaging and CD is custom anyway.

## Packaging

Sure you have a binary, but real users want a DMG, etc for their Initial install experience.

https://github.com/gedw99/2fa has all this for Mac and Windows 
- runs on Laptop and Github CI using the Makefile approach: https://github.com/gedw99/2fa/blob/main/.github/workflows/build.yaml
- Has a Systray. We dont need this though.
- Has a GUI written using GIO. We could use this instead of Zenity, but i think Zenity is better as its Narive Dialogues and ligtht.
- Has packaging Icons. We need ths and unify to use it for the Web site and other things for consistent Branding of yoru software.


## Feature Flags

This is a large area but highly useful for developers because you can do continuous CI without worrying abotu how stable the code is. Just hide unstable code behind a feature flag and release it to a subset of users.

Segments of Users. The easiest if to have 3 formal versions of your software called **Canary, Beta and Stable** like what Google Browser does.

- Users then pick which one they want, and the feature flag files are hardcoded into each.
- Because we have complete binary isolation, users can install all 3 version side by side.
- Later when you have User signup and NATS Jetstream, you can control the Feature flgas at a more granular level.


## CI 

Makefiles are generic and powerful and devs use them for anything.

We can also use this for CI, so that you never have to write a Github workflwo again.

**make.yaml** is an example Github Workflow file. It just calls "ci-all" in the Makefile
**Makefile** is an example Makefile that runs

- You can run the same code on your laptop that runs in CI.
- kleiner can still run in both plcaes as you see fit.

## CD

What if you want to not be dependent on Github for CD

NATS Jetsream Object store ( https://docs.nats.io/using-nats/developer/develop_jetstream/object ) can do this.
- In CI ( or laptop ) the bianry ( and any bits it may need ) are zipped and sent to NATS.
- Exisitng Servers, CLi, GUI programs all get an event when a new zip exists in NATS.
- Can run embedded into your own Server if you want. https://github.com/maxpert/marmot does at https://github.com/maxpert/marmot/blob/master/stream/embedded_nats.go

Marmot has everythign we need actually:
- Snap shot of any data backed up: https://github.com/maxpert/marmot/tree/master/snapshot
- DB replciation to scale out your Server DB usong SQLite: https://github.com/maxpert/marmot/blob/master/pool/sqlite_driver_connector.go

So essentially what we really get is our CD and Server all rolled into one binary.
We just need to imporr it into kleiner server.

## Web site and docs.

What if want a Web GUI that can do your Dev Docs, Product Docs and Diagrams and Screen shots ?

Hugo Plate ( https://github.com/zeon-studio/hugoplate) is a clenaest and most powerful Hugo project

This can run as on Github pages or where the NATS Server lives.

## Auth and Users records

What if you want a way to know who your users are ?

For example when someone downlaods your software, it woudl be great to offer to capture their Email, so you can keep them informed of updates.

PocketBase as a Framework ( https://pocketbase.io/docs/use-as-framework ) is a single golang import that gets you a SQLITE DB with a Data GUI and AUTH. 
- No bloat
- Nothing to manage and can run on any Server anywhere. I use fly.io btw.
- It can send emails, so you can update your Users with information. 

Marmot is designed to work with Pocketbase. Marmot currently is designed to replicate the SQLite to many Instances.

Hugo is able to work with Pocketbase, so that you can also incorporate posting of data by usong HTMX.
- https://github.com/acaloiaro/hugo-htmx-go-template/blob/main/server.go is all that is needed.

## I18n

User want to be able to show GUI in the native users language. Its a huge pain at the moment.
- reuse this for CLI, GUI, Server. Devs need the same translations everywhere.
- Installer and updater is in the users own language.
- Hugo in users own language. Hugo has i18n support and we can easily add our auto translator.
- Bianry in users own language. 

These look generic enough for the file format:
- https://github.com/vorlif/spreak core
- https://github.com/vorlif/xspreak plugins

This will do local translation automatically:
- https://github.com/gilang-as/google-translate
- Must add caching so we dont hit the APi too much, and we are idempotent


# Architecture

The above ideas can be implemented as packages for each area.

https://github.com/bots-garden/simplism
- has WASM sso plugins can be run without anything else.

https://github.com/bots-garden/slingshot
- same but with NATS.


## zips

Cross OS way to package the binary with any other files as a zip.
zips: https://github.com/timotewb/go-tools-folder-compress
unzip: https://github.com/timotewb/go-tools-folder-uncompress

## WASM

Rather than bloat up the system we could use WASM and embed the WASM into the ZIP.

Wazero ( https://github.com/tetratelabs/wazero ) can act as the Plugin runner for our packages and then be embedded into the main binary.



## NATS Jetsream

99% of users can just use NATS Embedded into the Server binary. 

asnyc-api with code gen: https://github.com/lerenn/asyncapi-codegen will gen all NATS code for us.

Now all Clients and Servers have a way to call each other in a bi-directional way for anything without a NATS dependency in their code.

## Logging

NATS so all logs from all clients and servers are in the same plaace to make bugs easier to debug.

We can easily isolate them by user in real time thanks to NATS wildcards.

## Proxy

If we want we can add NATS as a Proxy with Caddy later so that all users are load balanced without anything else needed.

If we use the nats.go and nats. js libs then this load balancing is global, so that if a Data center does doens users will not notice a thing and when the data center comes back up it will self recover. Its basically BGP Anycast for free.

https://github.com/sandstorm/caddy-nats-bridge is one example but its HTTP based so not what i am looking for ...





