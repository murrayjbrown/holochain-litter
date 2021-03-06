# Kennel

[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)

**Overview**
*Kennel* is a work in progress, derived from the Holochain project's [clutter](https://github.com/holochain/clutter) sample application which exists to demonstrate how easy it is to build applications on holochain.

A group of puppy dogs is called a *kennel.* Maybe it's time for a fully distributed shoutcast network of cute puppies, rather than a certain bluebird — or cats; I'm not particularly fond of cats. ;-)

**Purpose:**

This project is a personal learning vehicle for exploring the [Holochain](https://github.com/Holochain/holochain-proto) distributed computing platform, Holochain distributed applications (dApps).

**Objectives**:

- Evolve this dApp into something more transactional, such as for puppy adoption, perhaps with (qualified) auctions; or breeding with pedigree tracking.

**Code Status:** Pre-alpha. Not for production use. This application has not been audited for any security validation.

## Installation & Usage

Prerequiste: [Install holochain](https://github.com/metacurrency/holochain/#installation) on your machine and make sure you do the step to set the $GOPATH.

If you want to run Kennel locally, you will need `nodejs` installed, with `npm` or `yarn`:

```shell
git clone https://github.com/murrayjbrown/holochain-kennel.git kennel
cd ui-src
npm install # (or yarn install)
npm run build # (or yarn build)
cd ..
```

Now if you want to run the app, you can run:

```shell
hcdev web # if you want to just run it temporarily and test it out, with scratch data
```

or

```shell
hcd web # if you want to really start to use the app
```

If you want to just see Kennel in action, the best way to try out Kennel on your own is to run 2 instances of Kennel and your own Bootstrap server.  So clone the latest release from [Kennel Release](https://github.com/murrayjbrown/holochain-kennel.git) and make 2 copies of the contents into folders called kennel1 and kennel2.  Both folders will have a dna folder and a ui folder in each.

Firstly run the bootstrap server which will let each instance of Kennel know about its peers.  The ```bs```  command is part of the Holochain install.  If it doesn't work you probably need to set the $GO_PATH variable. (Soon we won't need this step)

```shell
  bs
```

You will get a response like

```shell
2018/01/11 11:24:03 app version: 0.0.2; Holochain bootstrap server
2018/01/11 11:24:03 starting up on port 3142
```

Now start up Kennel in each folder.

```shell
  cd kennel1
  hcdev -no-nat-upnp -port=6001 -agentID=lucy -mdns=true -bootstrapServer=localhost:3142 web 3141

  cd ..
  cd kennel2
  hcdev -no-nat-upnp -port=6002 -agentID=phil -mdns=true -bootstrapServer=localhost:3142 web 4141
```

You will see a response like:

```shell
Copying chain to: /Users/mjbrown/.holochaindev
Serving holochain with DNA hash:QmVbbeDAHVxC9cTvx6UhNEeTCK99SRKfxKDz3s4mR6TnsS on port:3141
```

Now open a browser at
[http://localhost:3142/QmVbbeDAHVxC9cTvx6UhNEeTCK99SRKfxKDz3s4mR6TnsS]( http://localhost:3142/QmVbbeDAHVxC9cTvx6UhNEeTCK99SRKfxKDz3s4mR6TnsS) (substituting in the DNA hash from the response above if different) and look at the Bootstrap server.  You will see 2 records like this

```shell
doQmVbbeDAHVxC9cTvx6UhNEeTCK99SRKfxKDz3s4mR6TnsS
  [{"Req":{"Version":1,"NodeID":"QmTAjDmQHobs2oQZp4UrbSzkShUGVKcsQUdakHeQ4YYxRX","NodeAddr":"/ip4/0.0.0.0/tcp/6003"},"Remote":"[::1]:63187","LastSeen":"2018-01-11T12:32:15.659887156+11:00"},{"Req":{"Version":1,"NodeID":"QmWQVaqEayZJWnvxLtsKr1iyeTDp3s7m7TTE36HhAUTiTK","NodeAddr":"/ip4/0.0.0.0/tcp/6002"},"Remote":"[::1]:63153","LastSeen":"2018-01-11T12:28:40.85765899+11:00"}]
```

Now open a browser to [http://localhost:3141](http://localhost:3141) and you will see Kennel.  Open another tab to [http://localhost:4141](http://localhost:4141) and you now have 2 instances of Kennel that you can chat between.  Add a handle in each and then woof and follow each instance and you will see the woofs!!

### Docker Usage

You can do all this much easier with Docker. Clone the latest release from [Kennel Release](https://github.com/murrayjbrown/holochain-kennel.git) and cd into the folder. Then run

```shell
  cd ui-src
  npm install # (or yarn install)
  npm run build # (or yarn build)
  cd ..
  TARGETDIR=$(pwd) docker-compose up
```

This will build the source into a React app and install it in Holochain. Then you can open browsers to

```shell
  http://localhost:3142 - Bootstrap
  http://localhost:3141 - Kennel
  http://localhost:4141 - Kennel
  http://localhost:5141 - Kennel
```

and try out Kennel.

### Tests

To run all the stand alone DNA tests:

```shell
hcdev test
```

## Scenarios

### Scenario - Collision Of Handles - Sequence Diagram

```shell
  hcdev -no-nat-upnp -mdns=true scenario collisionOfHandles
```

![Collison of Handles](collisionOfHandles-sequence.svg)

#### followAndShare

```shell
hcdev -mdns=true scenario followAndShare
```

This test spins up two nodes `jane` and `joe` and tests that following and reading posts works. To watch the network traffic and details try:

```shell
hcdev -debug -mdns=true scenario followAndShare
```

#### scaling

This test is designed to be run on separate machines and spins up many clones on each and confirms that they all talk to each other.

## UI automation

In kennel folder

```shell
  hcdev -execpath=$HOME/.holochaindev1 -no-nat-upnp -port=6001 -agentID=agent3141 -mdns=true web 3141
  hcdev -execpath=$HOME/.holochaindev2 -no-nat-upnp -port=6002 -agentID=agent4141 -mdns=true web 4141
  hcdev -execpath=$HOME/.holochaindev3 -no-nat-upnp -port=6003 -agentID=agent5141 -mdns=true web 5141
```

if running all in one terminal you will need to kill the processes between restarts.

```shell
  kill -kill `lsof -t -i tcp:3141` & kill -kill `lsof -t -i tcp:4141` & kill -kill `lsof -t -i tcp:5141`
```

## What the Automated build does

When a branch is pushed to Github Travis runs a build.  The build does the following:

1. Installs docker-compose
2. Runs docker-compose up -d which spins up a bootstrap server and 3 instances of kennel
3. Install the cypress dependencies
4. Runs the Cypress e2e tests.
5. If on master a new release is published to github releases. (coming soon)

## License

[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)

Copyright (C) 2017, The MetaCurrency Project (Eric Harris-Braun, Arthur Brock, et. al.)

This program is free software: you can redistribute it and/or modify it under the terms of the license provided in the LICENSE file (GPLv3).  This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
