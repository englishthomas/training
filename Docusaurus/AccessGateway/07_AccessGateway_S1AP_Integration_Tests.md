S1AP Integration Tests
======================

Current testing workflow for VM-only S1AP integration tests covering
gateway-only tests and general notes.

Our VM-only tests use 3 Vagrant-managed VMs hosted on the local device
(laptop):

-   *Magma VM*, i.e. magma-dev or gateway\
    > The *magma* VM acts as the ***gateway***
    > between *magma\_test* and *magma\_trfserver*

-   *magma\_test*, i.e. s1ap\_tester\
    > The *magma\_test* VM abstracts away the UE and eNodeB

-   *magma\_trfserver*, i.e. an Iperf server to generate uplink/downlink
    > traffic\
    > The *magma\_trfserver* emulates the Internet

Gateway-Only tests
------------------

### **Gateway VM Setup**

Spin up and provision the gateway VM, then make and start its services:

1.  From magma/lte/gateway on the host machine: vagrant up magma &&
    > vagrant ssh magma

2.  In the gateway VM: cd \$MAGMA\_ROOT/lte/gateway && make run

### **Test VM Setup**

Spin up and provision the s1ap tester\'s VM, make, then make in the
integ\_tests directory.

1.  From magma/lte/gateway on the host machine: vagrant up magma\_test
    > && vagrant ssh magma\_test

2.  In the *magma\_test* VM:

    i.  cd \$MAGMA\_ROOT/lte/gateway/python && make

    ii. cd integ\_tests && make

### **Run tests**

From \$MAGMA\_ROOT/lte/gateway/python/integ\_tests on
the *magma\_test* VM, run either individual tests or the full suite of
tests. A safe, non-flaky test to run
is s1aptests/test\_attach\_detach.py.

-   Individual test(s): make integ\_test TESTS=\<test(s)\_to\_run\>

-   All tests: make integ\_test

> **Note**: The traffic tests will fail as traffic server is not running
> in this setup. Look at the section below on running traffic tests.

### **Running uplink/downlink traffic tests**

1.  On the *magma* VM, run, disable-tcp-checksumming

2.  On the *magma\_test* VM, disable-tcp-checksumming

3.  Start the traffic server VM from the host, vagrant up
    > magma\_trfserver && vagrant ssh magma\_trfserver

4.  From *magma\_trfserver* VM, run disable-tcp-checksumming &&
    > trfgen-server

> Running make integ\_test in *magma\_test* VM should succeed now.

Testing Stateless Access Gateway
--------------------------------

The Access Gateway by default runs a set of stateful services, which
means that whenever the services are restarted, all previous state of
UEs and eNodeBs, and they need to reconnect and re-register.
Alternatively, we can switch the Access Gateway to be stateless, as
shown below, so that all UE state is preserved across service restarts.

Note that this is a feature in development, so some tests from the
integ\_test suite may not pass.

All the tests below assume you have completed the Gateway Setup and Test
VM Setup described above.

### **Testing one stateless service**

#### Stateless MME

This section describes how to test whether MME service is persisting
state to Redis.

On the Gateway VM:

1.  Disable Pipelined, Mobilityd, Sctpd and Sessiond from restarting
    > when MME restarts.

> cd /etc/systemd/system

2.  Comment out the line PartOf=magma\@mme.service from the following
    > files:

    a.  <magma@mobilityd.service>

    b.  <magma@pipelined.service>

    c.  <magma@sessiond.service>

    d.  sctpd.service

> sudo systemctl daemon-reload

3.  In /etc/magma/mme.yml, set use\_stateless to true

4.  Clean up all the state in redis: redis-cli -p 6380 FLUSHALL. This
    > might throw a \"Could not connect\" error if magma\@redis service
    > is not running. Start the redis service with \
    > \
    > sudo service magma\@redis start 

5.  Try again:\
    > \
    > cd \$MAGMA\_ROOT/lte/gateway; make restart

**On test VM:**

1.  Basic attach/detach test where MME is restarted mid-way:

> make integ\_test
> TESTS=s1aptests/test\_attach\_detach\_with\_mme\_restart.py

2.  Attach with uplink UDP traffic, where MME is restarted while UDP
    > traffic is flowing:

> make integ\_test
> TESTS=s1aptests/test\_attach\_ul\_udp\_data\_with\_mme\_restart.py
>
> Verify traffic server VM is running (as described in traffic tests
> above) and TCP checksum is disabled on all VMs.

#### Stateless Mobilityd

This section describes how to test whether Mobilityd service is
persisting state to Redis.

On gateway VM:

1.  Disable the MME from restarting when Mobilityd restarts and comment
    > out the line PartOf=magma\@mobilityd.service from the MME system
    > service file /etc/systemd/system/magma\@mme.service .

> sudo systemctl daemon-reload

2.  In /etc/magma/mobilityd.yml, set persist\_to\_redis to true

3.  Clean up all the state in redis: redis-cli -p 6380 FLUSHALL. This
    > might throw a \"Could not connect\" error if magma\@redis service
    > is not running. Start the redis service with:

> sudo service magma\@redis start and then try again

4.  cd \$MAGMA\_ROOT/lte/gateway; make restart

**On test VM:**

1.  cd \$MAGMA\_ROOT/lte/gateway/python && make

2.  cd integ\_tests && make

3.  Basic attach/detach test where Mobilityd is restarted mid-way:

> make integ\_test
> TESTS=s1aptests/test\_attach\_detach\_with\_mobilityd\_restart.py

4.  Test IP blocks are maintained across service restart\
    > \
    > make integ\_test
    > TESTS=s1aptests/test\_attach\_detach\_multiple\_ip\_blocks\_mobilityd\_restart.py

#### Stateless Pipelined

This section describes how to test whether Pipelined service is
persisting state to Redis.

On gateway VM:

1.  Disable MME from restarting when Pipelined restarts and comment out
    > the line PartOf=magma\@pipelined.service from the MME system
    > service file /etc/systemd/system/magma\@mme.service 

> sudo systemctl daemon-reload

2.  In /etc/magma/pipelined.yml, set clean\_restart to false

3.  Clean up all the state in redis: redis-cli -p 6380 FLUSHALL. This
    > might throw a \"Could not connect\" error if magma\@redis service
    > is not running. Start the redis service with:

> sudo service magma\@redis start and then try again

4.  cd \$MAGMA\_ROOT/lte/gateway; make restart

**On test VM:**

1.  cd \$MAGMA\_ROOT/lte/gateway/python && make

2.  cd integ\_tests && make

3.  UDP traffic test where Pipelined is restarted mid-way:

> make integ\_test
> TESTS=s1aptests/test\_attach\_ul\_udp\_data\_with\_pipelined\_restart.py

### ** Testing Stateless Gateway with all Services**

To test the Gateway with all services being stateless:

1.  On ***gateway VM***, follow steps 1 and 2 for each of Stateless MME,
    > Mobilityd and Pipelined as listed above

2.  On ***test VM***, run any of the test cases for individual service
    > restarts listed above. Further, you can test attach with uplink
    > UDP traffic, where multiple services are restarted while UDP
    > traffic is flowing:

> make integ\_test
> TESTS=s1aptests/test\_attach\_ul\_udp\_data\_with\_multiple\_service\_restart.py
>
> Verify the ***traffic server VM*** is running (as described in traffic
> tests above) and TCP checksum is disabled on all VMs.

Notes
-----

-   Restart the *magma* VM with vagrant reload magma on an assertion
    > error involving \
    > ENB\_S1\_SETUP\_RESP.  This is a known issue.

-   See [*[Bindings for Magma\'s REST
    > API]{.underline}*](https://fb.quip.com/4tmUAtlox4Oy) for notes on
    > the Python bindings for our REST API generated
    > by [[swagger-codegen]{.underline}](https://github.com/swagger-api/swagger-codegen).

-   It may be cleaner to set the host using the [[configuration
    > class]{.underline}](https://github.com/swagger-api/swagger-codegen/blob/master/samples/client/petstore/python/petstore_api/configuration.py).
    > This is also where we can set SSL options.
