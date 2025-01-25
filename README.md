# Environment Setup: Using QUIC with ns-3

## Table of Contents
1. [Overview](#overview)
2. [Execution Environment](#execution-environment)
3. [Environment Setup and Verification](#environment-setup-and-verification)
   - [Download and Extract ns-3 Source](#download-and-extract-ns-3-source)
   - [Download and Extract the QUIC Module](#download-and-extract-the-quic-module)
   - [Move Files and Rename wscript](#move-files-and-rename-wscript)
   - [Modify wscript](#modify-wscript)
   - [Build and Move Necessary Files](#build-and-move-necessary-files)
   - [Move the Header Files](#move-the-header-files)
   - [Create quic-module.h in build/ns3](#create-quic-moduleh-in-buildns3)
4. [Run quic-tester.cc](#run-quic-testercc)
5. [Output](#output)
6. [Create and run your own simulation code](#create-and-run-your-own-simulation-code)


## Overview

ns-3 is a discrete-event network simulator for simulating network protocols and internet communications. It is widely used by researchers and developers to experiment and evaluate network behaviors in a controlled environment.

Additionally, ns-3 is open-source and provided under the GNU GPLv2 license.

This repository shares the steps to set up the environment for ns-3 with QUIC support.

The test code, `quic-tester.cc`, which was pre-configured for QUIC in ns-3, has been verified to work successfully.

Below are all the steps for setting up the environment and verifying the functionality.

## Execution Environment

- OS: Ubuntu 20.04.6 LTS
- gcc version: 9.4.0 (Ubuntu 9.4.0-1ubuntu1~20.04.2)

## Environment Setup and Verification

### Download and Extract ns-3 Source

Run the following command to download and extract the ns-3 source code:

```bash
wget https://www.nsnam.org/releases/ns-allinone-3.32.tar.bz2
tar -xvf ns-allinone-3.32.tar.bz2
```

### Download and Extract the QUIC Module
Run the following command to download and extract the QUIC module. Afterward, move the extracted module into the appropriate directory inside the ns-3 project.

```bash
wget https://github.com/signetlabdei/quic/archive/refs/tags/v1.0.tar.gz
tar -xvf v1.0.tar.gz

mv quic-1.0 ./ns-allinone-3.32/ns-3.32/src/
mv ns-allinone-3.32/ns-3.32/src/quic-1.0 ns-allinone-3.32/ns-3.32/src/quic
```

### Move Files and Rename wscript
Navigate to the ns-3 directory and move the files to the correct locations. Also, rename the wscript file to wscript.py, as it is initially a JavaScript file.
```bash
cd ns-allinone-3.32/
cd ns-3.32/

mv src/quic/quic-applications/helper/* src/quic/helper/
mv src/quic/quic-applications/model/* src/quic/model/

mv src/quic/wscript src/quic/wscript.py
```

Now, change the name of wscript.py back to wscript (without the .py extension) without using the mv command, as it will revert the file to JavaScript.

### Modify wscript

Additionally, add the following lines to wscript in two sections:

1. Add the following to the module.source section:
```bash
'model/quic-client.cc',
'model/quic-server.cc',
'model/quic-echo-client.cc',
'model/quic-echo-server.cc',
'helper/quic-echo-helper.cc',
'helper/quic-client-server-helper.cc'
```

2. Add the following to the headers.source section:
```bash
'model/quic-client.h',
'model/quic-server.h',
'model/quic-echo-client.h',
'model/quic-echo-server.h',
'helper/quic-echo-helper.h',
'helper/quic-client-server-helper.h'
```

After modification, your wscript file should look as follows:
```bash
# -*- Mode: python; py-indent-offset: 4; indent-tabs-mode: nil; coding: utf-8; -*-

def build(bld):
    module = bld.create_ns3_module('quic', ['internet', 'applications', 'flow-monitor', 'point-to-point'])
    module.source = [
        'model/quic-congestion-ops.cc',
        'model/quic-socket.cc',
        'model/quic-socket-base.cc',
        'model/quic-socket-factory.cc',
        'model/quic-l4-protocol.cc',
        'model/quic-socket-rx-buffer.cc',
        'model/quic-socket-tx-buffer.cc',
        'model/quic-socket-tx-scheduler.cc',
        'model/quic-socket-tx-pfifo-scheduler.cc',
        'model/quic-socket-tx-edf-scheduler.cc',
        'model/quic-stream.cc',
        'model/quic-stream-base.cc',
        'model/quic-l5-protocol.cc',
        'model/quic-stream-tx-buffer.cc',
        'model/quic-stream-rx-buffer.cc',
        'model/quic-header.cc',
        'model/quic-subheader.cc',
        'model/quic-transport-parameters.cc',
        'model/quic-bbr.cc',
        'helper/quic-helper.cc',
        'model/quic-client.cc',
        'model/quic-server.cc',
        'model/quic-echo-client.cc',
        'model/quic-echo-server.cc',
        'helper/quic-echo-helper.cc',
        'helper/quic-client-server-helper.cc'
    ]

    module_test = bld.create_ns3_module_test_library('quic')
    module_test.source = [
        'test/quic-rx-buffer-test.cc',
        'test/quic-tx-buffer-test.cc',
        'test/quic-header-test.cc',
    ]

    headers = bld(features='ns3header')
    headers.module = 'quic'
    headers.source = [
        'model/quic-congestion-ops.h',
        'model/quic-socket.h',
        'model/quic-socket-base.h',
        'model/quic-socket-factory.h',
        'model/quic-l4-protocol.h',
        'model/quic-socket-rx-buffer.h',
        'model/quic-socket-tx-buffer.h',
        'model/quic-socket-tx-scheduler.h',
        'model/quic-socket-tx-pfifo-scheduler.h',
        'model/quic-socket-tx-edf-scheduler.h',
        'model/quic-stream.h',
        'model/quic-stream-base.h',
        'model/quic-l5-protocol.h',
        'model/quic-stream-tx-buffer.h',
        'model/quic-stream-rx-buffer.h',
        'model/quic-header.h',
        'model/quic-subheader.h',
        'model/quic-transport-parameters.h',
        'model/quic-bbr.h',
        'helper/quic-helper.h',
        'model/windowed-filter.h',
        'model/quic-client.h',
        'model/quic-server.h',
        'model/quic-echo-client.h',
        'model/quic-echo-server.h',
        'helper/quic-echo-helper.h',
        'helper/quic-client-server-helper.h'
    ]

    if bld.env.ENABLE_EXAMPLES:
        bld.recurse('examples')
```

### Build and Move Necessary Files
Once you have moved the required files, run the build:

```bash
cp src/applications/model/seq-ts-header.h  src/quic/model/
cp src/applications/model/packet-loss-counter.h  src/quic/model/

./waf configure --build-profile=debug --enable-examples --enable-tests
./waf build
```


### Move the Header Files
After building, manually move the QUIC module to the build/ns3 directory:

```bash
sudo cp src/quic/helper/*.h build/ns3/
sudo cp src/quic/model/*.h build/ns3/
```

### Create quic-module.h in build/ns3
Navigate to the build/ns3 directory and create the quic-module.h file:

```bash
cd build/ns3
vi quic-module.h
```
The content of quic-module.h should be as follows:
```bash
#ifdef NS3_MODULE_COMPILATION
# error "Do not include ns3 module aggregator headers from other modules; these are meant only for end user scripts."
#endif

#ifndef NS3_MODULE_QUIC

// Module headers:
#include "quic-bbr.h"
#include "quic-client-server-helper.h"
#include "quic-client.h"
#include "quic-congestion-ops.h"
#include "quic-echo-client.h"
#include "quic-echo-helper.h"
#include "quic-echo-server.h"
#include "quic-header.h"
#include "quic-helper.h"
#include "quic-l4-protocol.h"
#include "quic-l5-protocol.h"
#include "quic-server.h"
#include "quic-socket-base.h"
#include "quic-socket-factory.h"
#include "quic-socket-rx-buffer.h"
#include "quic-socket-tx-buffer.h"
#include "quic-socket-tx-edf-scheduler.h"
#include "quic-socket-tx-pfifo-scheduler.h"
#include "quic-socket-tx-scheduler.h"
#include "quic-socket.h"
#include "quic-stream-base.h"
#include "quic-stream-rx-buffer.h"
#include "quic-stream-tx-buffer.h"
#include "quic-stream.h"
#include "quic-subheader.h"
#include "quic-transport-parameters.h"
#include "windowed-filter.h"
#endif
```

### Run quic-tester.cc
Now, navigate back to the terminal and run the quic-tester.cc test code:

```bash
cd ../..
cp src/quic/examples/quic-tester.cc scratch/quic-tester.cc
./waf --run scratch/quic-tester
```


### Output
The following output should appear after running the test:
```bash

$ ./waf --run scratch/quic-tester
Waf: Entering directory `/home/asaken_watanabe/workspace/ns-allinone-3.32/ns-3.32/build'
Waf: Leaving directory `/home/asaken_watanabe/workspace/ns-allinone-3.32/ns-3.32/build'
Build commands will be stored in build/compile_commands.json
'build' finished successfully (0.460s)


#################### SIMULATION SET-UP ####################


+0.000000000s -1 QuicEchoServerApplication:QuicEchoServer(0xaaaad5036e40)
+0.000000000s -1 QuicEchoServerApplication:SetStreamId(0xaaaad5036e40, 2)
+0.000000000s -1 QuicEchoClientApplication:QuicEchoClient(0xaaaad50385c0)
+0.000000000s -1 QuicEchoClientApplication:SetDataSize(0xaaaad50385c0, 100)
+0.000000000s -1 QuicEchoClientApplication:SetStreamId(0xaaaad50385c0, 2)
+0.000000000s -1 QuicEchoClientApplication:SetFill(0xaaaad50385c0, "Hello World")

...

```
Execution results are omitted due to length.

### Create and run your own simulation code

To create and run your own simulation code, create a file with \<any file name\>.cc directly under the scratch folder. 

You can execute it with the following command.

```bash
./waf --run scratch/<any file name>
```
