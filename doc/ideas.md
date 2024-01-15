# ideas 

https://github.com/can3p/kleiner is designe dto work with Github for CLI goalng programs.

Its create because it gives golang devs exactly what they need without all the setup.

What follows are ideas for extensions to this further:

## GUI

Currently, kleiner works for golang CLI for TUI and Server use cases, but what about GUI ?

Each OS has a custom way to do this and needs custom packaging and also has custom self updating approaches that are designed for Devs using Chocelaty, Brew, etc.

But we can built out own into the actual Bianry !!

Zenity ( https://github.com/ncruces/zenity ) can do this.
- write a package that conforms to the current kleiner but has a GUI
- It has enough.
- Onyl runs when the bianry is opened, and so no need for anything else to be on the Laptop.

## CI 

Makefiles are generic and powerful and devs use them for anything.

We can also use this for CI, so that you never have to write a Github workflwo again.

**make.yaml** is an example Github Workflow file. It just calls "ci-all" in the Makefile
**Makefile** is an example Makefile that runs

- You can run the same code on your laptop that runs in CI.
- kleiner can still run in both plcaes as you see fit.

## CD

What if you want to not be dependent on Github for CD, and want a nice Web GUI.

NATS Jetsream Object store ( https://docs.nats.io/using-nats/developer/develop_jetstream/object ) can do this.
- In CI ( or laptop ) the bianry ( and any bits it may need ) are zipped and sent to NATS.
- Exisitng Servers, CLi, GUI programs all get an event when a new zip exists in NATS.

## Web site and docs.

Devs always needs Docs, Product and Diagrams and Screen shots 

Hugo Plate ( https://github.com/zeon-studio/hugoplate) is a clenaest and most powerful Hugo project

# Architecture

The above ideas can be implemented as packages for each area.

