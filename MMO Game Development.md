
[Google Translated from] (https://github.com/21moons/memo/blob/master/%E7%BD%91%E7%BB%9C%E6%B8%B8%E6%88%8F%E6%A0%B8%E5%BF%83%E6%8A%80%E6%9C%AF%E4%B8%8E%E5%AE%9E%E6%88%98.md)


# 0 [Quick Start] Online Game Programming

## 0.2 Introduction to Socket Programming-Handling Multiple Concurrent Connections, Pursuing Performance

### 0.2.6 Single-threaded, non-blocking, event-driven-use the select function for polling

`` `c
int sock = socket (PF_INET, SOCK_STREAM);
bind (sock, addr);
listen (sock);

allsock.add (sock);
while (1) {
    result = select (sock);
    if (result> 0) {
        int new_sock = accept (sock, & addr);
        allsock.add (new_sock);
    }

    foreach (sock = allsock) {
        result = select (sock);
        if (result> 0) {
            char buf [100];
            size_t size = read (new_sock, buf, 100);

            if (size == 0) {
                close (new_sock);
            } else {
                write (new_sock, buf, size);
            }
        }
    }
}
`` `

### 0.2.7 Features of online game input and output-single thread, event driven, non-blocking

### 0.2.9 Give full play to performance and improve development efficiency-from the implementation language to the underlying structure

Software performance and development efficiency are contradictory

C / C ++> java> Scripting language

<p align = "left"> <font size = 2> Table 0.1 Main programming languages ​​and throughput </ font> </ p>

| Language | Throughput | Features |
| ------ | ------ | ------ |
| C / C ++ | 100 | Static typing, native code |
| Java / C # | 1 ~ 10 | Static Type, VM, Bytecode |
| Ruby / Python | 0.1 ~ 1 | Dynamic typing |

For CPU-intensive applications, Java runs at a speed similar to C.
However, for IO-intensive applications, because the Java virtual machine makes system call every time, it will perform buffer overflow check and exception object processing, so the running speed is about 10 times slower than C.

Dynamically typed languages ​​are slower than Java, because each time some processing is performed, the method called by the object may change, so it must be checked before each call.

The Go language is a static, locally executed language. It has a garbage collection mechanism, and programmers can choose the typed strength in different parts of the code.

### 0.2.10 Give play to the performance of multi-core servers

On Linux, on average, each CPU core can perform 100,000 to 200,000 context switches within one second. (The context switch here refers to the switching of the operating system scheduling unit)

### 0.2.12 Simplified server implementation --libevent

libevent single thread + event driven + non-blocking call

The biggest feature of libevent is: its working method is not to create a large number of threads and wait for system calls such as read, but to "not create threads, register callback functions for each event that you want to notify in advance. Function call "

## 0.3 RPC Guide-The Simplest Communication Middleware

DSL (Domain Specific Language)
IDL (Interface Description Language)

Protocol Buffers
Thrift

### 0.4.4 The essence of game programming-"task system" without threads

Many objects in the game seem to be moving at the same time at the same time, but in the system, multiple threads are not used for parallel processing. Therefore, the game is generally calculated by frame, and one or several timed loop threads are used to process events. To express all the objects in the scene.

### 2.5.2 3 special responsibilities of online game service operation

* Development
* Operation and maintenance
* Sales

## 2.7 Major categories of technologies that support online games

<p align = "left"> <font size = 2> Table 2.2 Classification of physical structures and differences in their properties </ font> </ p>

| | C / S | P2P |
| ------ | ------ | ------ |
| Delay (required time, communication delay) | Large | Small |
| Server Equipment Cost | Big | Small |
| Bandwidth cost | Determined by game content | Determined by game content |
| Illegal behavior | Difficult | Easy |

MMO Massively Multiplayer Online
MO Multi-player Online

<p align = "left"> <font size = 2> Table 2.3 Classification and characteristics of logical structure </ font> </ p>

| | MO | MMO |
| ------ | ------ | ------ |
| Game core | A small number of players gather to compete | A large number of players socialize |
| Number of simultaneous players | Around 20 | 200 ~ 1000000 |
| Delay | 50 ms | 300 ms |
| Game Time (Cumulative) | Minutes | Years |
| RMT (Real Money Trading) | Rarely | Active |
| Platforms | Game consoles | PCs, mobile devices |

4 forms of online games-physical structure * logical structure
The physical structure has two kinds of C / S and P2P, the logical structure has two kinds of MO and MMO, a total of four combinations

<p align = "left"> <font size = 2> Table 2.4 4 forms and types of online games </ font> </ p>

| | MO | MMO |
| ------ | ------ | ------ |
| C / S | Casual Games | MMORPG Virtual World |
| P2P | ARPG (Action RPG), Battle Fighting Game, FPS, Racing Game, RTS, Shooting Game | N / A |

### 2.8.2 3 cores that support the main body of online games

* Data format of the game
* Communication method of the game
* The reaction speed of the game (delay)

The display updates the screen dozens of times per second. This screen update time is the shortest time interval for the player to change the game state.

<p align = "left"> <font size = 2> Table 2.5 Network delay and implementation method </ font> </ p>

| Name | Delay | Synchronous / Asynchronous | Verbose | Supplement |
| ------ | ------ | ------ |
| Synchronization method | Less than 25 milliseconds | Synchronization | Non-redundant | Under the premise of high-speed network, the synchronization method is better |
| Asynchronous mode | Less than 100 milliseconds | Asynchronous | Redundancy | In order to achieve high-speed games on a low-speed network, redundancy is necessary |
| Browser mode | Less than 300ms | Non-synchronized | Non-redundant | The degree of delay exceeds the range that can be solved with redundancy, so the game data and the game client (game browser) are completely separated |

The synchronization method means that the programs running on all game player devices are synchronized, and there will be a coordinator under the star topology to ensure that all players see the state within each unit time (this unit time is related to the frame rate) It is the same. Only after obtaining the information of all the players can the game continue. If someone's network is delayed, the synchronization rate will be slower, causing all players to be affected.

Asynchronous means that the progress of the game on each terminal is different, and compromise is made on the consistency of the game data. The data is not required to be completely consistent, even if the program on a certain terminal stops running, the programs on other terminals can also run as usual , But you will see that the role corresponding to the problem terminal is stuck. Because of this, in the asynchronous mode, you can use a more unstable transmission line and a line with a greater delay, and you can also support more simultaneous online connections.

The browser mode means that all game content runs in the background, and the foreground only plays a role of display, so that even if all programs except the server stop running, the program can continue to run without data mismatch.

* Redundancy of game processing-> Solve the relationship between master data and copy data
* Asynchronous game processing-> Solve the relationship between synchronous processing (lock) and asynchronous processing when data changes

### 3.1.2 Reasons for storing data in memory

### 3.1.3 Change every 16 milliseconds-the information processed and its size

### 3.1.4 Display of a large number of objects-CPU processing power

Games are processed frame by frame, assuming there are 60 frames per second, and the main frequency of the CPU is 1.79 MHz, then for each frame of the game screen, the CPU has about 30,000 clock cycles available (1790000/60 = 29666).

To move objects in the game world, the following processes must be performed: reading coordinates, reading speed, performing calculations, and saving results. Using the command set of the 6502 processor to process the above operations requires 2 ~ 8 cycles, assuming that the efficiency is more The high program requires 4 cycles, and there are 8 objects in the game scene, so the two data in two-dimensional coordinates X and Y are processed once, a total of `(4 + 4 + 4 + 4) * 2 * 8 = 256` cycles, relative to the CPU 30,000 cycles of processing power can be described as very abundant.

Then you need to determine whether your own bullet has hit the enemy. Suppose you want to perform collision detection on all 8 objects in flight, then you must conduct 8 * 8 = 64` detections. Collision detection cannot be based on whether the coordinates of the two objects are consistent. To make a judgment, it must be judged by a rectangle, so a second comparison is required. Therefore, the processing process is as follows: reading coordinates, second comparison, judgment result. This time I did not want to process it requires 10 cycles, and because I have to deal with X , The Y coordinate, so it has to be doubled, and ultimately requires `(10 + 10 + 10 + 10) * 2 * (8 * 8) = 5120` cycles, plus 256 cycles of movement processing, which is 5400 The cycle is not good, it seems quite ample.

Classic games on home game consoles usually have around 20 characters. If collision detection is performed on 20 objects, according to the above calculation method, it needs to be `(10 + 10 + 10 + 10) * 2 * (20 * 20) = 32000` cycles, which exceeds the upper limit of 30,000 cycles. In addition, other operations and sound processing have not been considered here, so some optimization measures must be taken to reduce the cycle consumed, for example, the "self bullets will not occur between Collision "," The enemy bullet and the own bullet will not collide "and other game contents are taken into account.

When the CPU computing power is insufficient, the refresh rate of the game screen will decrease, causing the movement of objects in the game to become slow, resulting in a sharp decline in the game experience.

* Assuming that RDBMS is used in a home game console, then SQL statements must be passed, but statements such as SELECT * from FlyingObjects, just judge whether the syntax is correct and consume hundreds of CPU cycles, obviously unrealistic. Game programming must be at The determination and storage of the coordinates are completed within the frame. For this reason, it must be achieved only by combining some of the most primitive commands that the CPU has. It is quite unreasonable to just read the data to spend hundreds of cycles. Therefore, at home games In the machine, basically do not consider using RDBMS this way.

### 3.1.5 Unable to predict the player's operation-the game state is ever-changing (difficult to optimize)

### 3.1.6 The game data must be placed on the machine where the CPU is located

Comprehensive 3.1.4, 3.1.5, 3.1.6, the data during the game needs to be constantly changing at a very fast rate, so these data must be managed in memory. The key to managing in memory is to pay attention to the CPU frequency, also It is to access the data within a delay of a few nanoseconds to hundreds of nanoseconds, and only 1 nanosecond will advance by a distance of 30 centimeters.

In other words, game data needs to be stored within tens of centimeters from the CPU, but in practice, players are usually scattered everywhere, so it is necessary to save data redundantly between farther and closer This guarantees data consistency, which is very difficult.

## 3.2 Elements Unique to Online Games

### 3.2.1 Communication delay

### 3.2.2 Bandwidth--the standard of transmission volume

### 3.2.3 Server--cost, estimate of the number of servers

### 3.2.4 Security-the weakness of online games

### 3.2.5 Auxiliary system

## 3.3 Detailed physical architecture

### 3.3.1 Basic network topology

In order to reduce the communication delay as much as possible, the actual use is star, and full mesh structure (full mesh).

### 3.3.2 General category of physical architecture

### 3.3.3 C / S architecture--pure server type, reflective type

The reflection type only exchanges data packets, and does not check the contents.

### 3.3.4 P2P architecture

The P2P architecture has two modes, synchronous and asynchronous, and the synchronous mode has two types: full mesh structure and star structure.

### 3.3.5 C / S + P2P hybrid architecture

### 3.3.6 ad-hoc mode

## 3.4 Detailed logical architecture

### 3.4.1 What is MO, MMO?

* The difference between MO (Multiplayer Online) and MMO (Massively Multiplayer Online)
MO has fewer online users (2-100) at the same time, and the game time is relatively short.Yes, when you start the game, the state of the game will be reset, and the form of the game data is one-time.
The number of MMOs online at the same time reaches hundreds or thousands, the game data cannot be reset, and it is permanent.

### 3.4.2 MO architecture, MOG

The MO architecture is often used in games such as FPS and RTS.It is suitable for games with a small number of online people and high real-time performance.

* MO architecture
  * Synchronization method / full mesh structure
  * Synchronization method / star structure
  * Asynchronous mode / full mesh structure
  * Asynchronous mode / star structure

### 3.4.3 Synchronization mode--the game can continue only after the information of all players is obtained

Synchronous mode / full mesh structure, all terminals participating in the game have master data, these terminals transmit all input information of control devices to each other, and the game is always in a waiting state before obtaining input data from all terminals.
Synchronous mode / star structure, configure a root server that comprehensively manages game data, all participating terminals send all input information of players to the server, once the game state has progressed, the server will return those changed state data to All clients, before the server returns information, all clients do not render anything, just simply wait.

### 3.4.4 Synchronization mode / full mesh structure implementation-all terminals have master data

Only "input information of the control device" is sent between the terminals, and the game process data is digital data, so if the initial state and state changes can be sent without omission, the state of all players can always be consistent.

Example: Online Go Game

Three problems with the synchronization method / full mesh structure:
1. After the number of people increases, the integrity of sending and receiving information can easily collapse, especially when the network is unreliable
2. The slowest terminal will prolong the overall transmission time
3. Cannot join the game midway

### 3.4.5 Synchronization Mode / Star Structure--Temporarily concentrate the input information on the server

In this structure, all members in the network are not completely equal. The central terminal in the star structure is called a "server", and the other terminals are called "clients." The client will control the input information such as the direction keys on the device. Send to the server, the server sends the received input information to all clients at the same time after the reception is completed.

In this way, the requirements for data transmission links are reduced, and new problems are also introduced:
* Slow response (direct connection becomes forwarding).

The inevitable major problem of the synchronization method. Joining the game halfway will cause all players to temporarily interrupt the game, because the new client needs to obtain status data. The solution is to preferentially match players with similar physical positions (increasing the status synchronization speed) or shorten the game round. (No need to join the game halfway).

### 3.4.6 Asynchronous mode-accept inconsistency of game status on each terminal

### 3.4.7 The three basic elements of the game world--self, opponent, environment--asynchronous implementation guidelines

### 3.4.8 Self and opponents-the degree of abstraction of data between the game and the player

### 3.4.10 Own and the environment-fighting games and mutual exclusion control of available items

### 3.4.11 Realization of Mutual Exclusion Control-Adopt a mechanism similar to synchronous mode to achieve asynchronous mode

The distributed consensus algorithm is not suitable for this scenario, because this algorithm requires multiple round trips to transfer information, and online games that use the asynchronous method need to ensure data consistency in the extremely short period of tens of milliseconds.

* Item copy problem

### 3.4.12 Environments whose status changes automatically--static and dynamic environments

### 3.4.13 Relationship between opponent and environment
Relative to "self and opponent", "self and environment" is less important, can save

## 3.5 Detailed logical architecture--MMO architecture

The MMO architecture is "sharing a long-standing game process among a large number of players". For this kind of game, players invest a lot of time, so the game system must have high reliability. In the MMO architecture, the management of the game process The data center is responsible for all storage and saving, and the data center continuously sends the game results to the player terminal.

* Features of MMO game:
1. Player data needs to be persistent
2. Player data will accumulate over time
3. Support a large number of players online at the same time

* Browser mode, difference between synchronous mode and asynchronous mode
The difference between the browser mode and the synchronous mode lies in "transmitted content". In the synchronous mode, only the information input by yourself and other players is sent and received, while in the browser mode, the browser sends the operation information of the local player to the server Send the results of the game world to the browser.
In addition, in the synchronous and asynchronous modes, all the terminals participating in the game share all the data in the game process, while in the browser mode, only the server manages the game data, and the game logic is all implemented on the server. ) Only shows the results of the current player operation in the game world.

* The implementation policy of the MMO architecture-browser mode (the browser here does not refer to a web browser), pure C / S mode
1. In order to support a large number of players online at the same time, it is necessary to abandon the synchronous / asynchronous mode in the MM architecture (transmit state changes while the game is in progress to all players)
2. The huge amount of operations triggered by a large number of players online can easily cause the server to paralyze. Therefore, in order to support more players online at the same time, the server must be lightened. The client uses a browser to achieve, only contains and renders, sound And operation-related processing, only logical processing and player data persistence are placed on the server. The amount of data communicated between the client and the server should be as small as possible, and can tolerate large communication delays and unstable connections. Even if it does not start On the client side, the game will continue to run on the server.
3. Because all the logic is processed sequentially on the server, there will be no inconsistency between players, but in the case of too long communication delay, the screen will become incoherent

The interaction between the client and the server can be simplified as: the client sends the player operation-> the server performs the processing-> the processing result is displayed on the client

`` `
Column-Try to improve the display interval of web games
Assuming that the network delay is 200 milliseconds and the actual movement requires 500 milliseconds, the sequence of events in a web game is:
* 0 ms: the player starts to move (move to the coordinate XY)
* 0 milliseconds: operation information starts to be sent to the server
* 200 ms: the message reaches the server
* 205 milliseconds: the server process is over, and the result (movement start) is sent to the client
* 405 milliseconds: "Mobile start" message arrives at the client and starts rendering
* 905 milliseconds: the end of mobile screen rendering

In this way, the player will start to move after about 400 milliseconds after the operation. From our perspective, this effect is too bad. Just change the order of events to the following form to improve the operation time. Experience.
* 0 ms: the player starts to move (move to the coordinate XY)
* 0 milliseconds: operation information starts to be sent to the server
* 0 milliseconds: the client does not wait for the result information sent by the server, and immediately starts rendering the mobile screen
* 200 ms: the message reaches the server
* 205 milliseconds: the server process is over, and the result (movement start) is sent to the client
* 405 milliseconds: "Move Start" message arrives at the client, if the move is successful, ignore the message
* 500 milliseconds: the end of mobile screen rendering

This shortens the 905 milliseconds required to complete the movement to 500 milliseconds, so that players can feel that their characters are moving smoothly.
The problem here is that if the server sends back a "moving failure" message, you need to immediately return to the original location, otherwise the game state will be inconsistent, causing problems with subsequent operations.
`` `

# 4 [Realization] C / S MMO game development-long-running game server

## 4.1 Basic process of online game development

### 4.1.1 Project documentation / deliverables

Ensure the team work content is consistent

| Category | Name | Purpose | Content |
| ------ | ------ | ------ | ------ |
| Planning Documents | Summary Design Documents | Used for investors to judge the project feasibility and for prototype development | Including game content, basic rules, game main elements, platforms and business models |
Planning documents | Detailed design documents (specifications) | Used to guide developers in designing | This document contains the same information as the game guide and game description. Based on this, you can decide how to write code and how Start design work. |
| Planning Documents | Development Elements / Workload List | Used to estimate development costs and formulate a high-accuracy plan | List all the functions that must be implemented, determine the status without omissions, and quantify the workload |
| Design Documents | System Basic Structure Diagram | Server Design Summary | In order to design the process relationship diagram and resources, visualize the required design guidelines |
| Design Documents | Process Relationship Diagrams | Improve design accuracy, prepare servers, build systems | List information about all processes on the server and client, including the number of processes, relevance, network connection status, connection order, access mode, Expansion method |
| Design Documents | A summary of the physical structure of the server | Used to inform the system operation and maintenance company of how to configure the server, and for detailed design | Graphically explain what physical relationship must be configured for the server with certain characteristics |
| Design Documents | Bandwidth / Equipment Resource Estimation Documents | Costs of servers, transmission lines, storage devices, etc. needed for patina | From a technical level, estimate what types of servers are required, how many units are needed, and what availability needs Degree |
| Design Documents | Protocol Definition Documents | Used to define the transmission content, and improve the efficiency of the parallel development of the server and the client | Recorded the content of the data package that needs to be transmitted between the client and the server |
| Design Documents | Database Design Drawing | Used to realize the database | In order to avoid the sudden occurrence of database performance problems after operation, the database query statement needs to be planned |
| Design Documents | System Operation and Maintenance Design Documents | Used to actually build a physical server system | What kind of performance the server needs, when should it be purchased, how many units should be purchased, how to configure, connect, set up |
| Business-related documents | Contracts | Used to prevent transaction disputes between investors, developers, and operators | |
| Business-related documents | Business plan | Used to judge the feasibility of the investment plan | Estimate the initial development based on past experience, and even the trend within 2-3 years after the start of operation |
| Business-related documents | Development schedule | Used to grasp the overall development progress | Development plan formulation |
| Business-related documents | Development system diagram | Used to confirm whether the development process is normal | Explain your own development system to people outside the company |

### 4.1.2 Development process and document preparation process

### 4.1.3 Documentation / deliverables of technical staff

## 4.2 C / S MMO game development trends and countermeasures

C / S MMO architecture features:
1. Support the processing of large amounts of data
2. Set the information strictly confidential to the player
3. Strictly maintain the changes of game data and block illegal operations
4. Simple change of setting information
5. Easy to integrate with other service systems

C / S MMO architecture limitations:
1. a large delay
2. Game server bandwidth load is high
3. Game server maintenance costs are high
4. The game cannot be played during server maintenance

## 4.4 Development of basic system structure diagram

Enter:
1. Confirm the expected number of simultaneous connections
2. Identify expected system bottlenecks

! [Possible bottleneck of C / S MMO system] (https://raw.githubusercontent.com/21moons/memo/master/res/img/core technology of online games and actual combat / MMO system.

### 4.4.4 Solve the bottleneck of the game server / database

1. Space segmentation method, used to solve the server bottleneck.
2. Instance method (game copy instance), the part with particularly high load, where the users are concentrated is isolated, and these parts are allocated to a dedicated server for processing to solve the server bottleneck.
3. Parallel world approach, the game database is divided into multiple, used to solve the database bottleneck.

### 4.4.13 Estimate the number of simultaneous online based on the processing cost of the game logic

Estimate formula:
Simultaneous online * number of enemies facing each player * number of enemy actions per second * CPU time required for each action * safety factor = 1 second

### 4.4.14 Estimate the number of simultaneous online based on the processing load of the game database

Estimate formula:
Simultaneous online number * Average data storage frequency per connection * Number of queries required for 1 storage * Safety factor = query frequency supported by the database server

### 4.4.16 The basic structure of the server, formulate the basic structure diagram of the system

! [System basic structure diagram] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / system basic structure diagram.png)

## 4.5 Formulation of Process Relationship Diagram

| Name | Acronyms in this book | Purpose |
| ------ | ------ | ------ |
| Client | cli | A dedicated program used by users directly to access the game server |
| Game server | gmsv | Game logic processing, including execution of enemy actions, terrain judgment, events, character upgrades, cheating detection |
| Login server | loginsv | Responsible for overall service usage management, load control and session key issuance |
| Database server | dbsv | Unified connection to DBMS such as MySQL and Oracle |
| Reverse proxy server | proxy | Accepts simultaneous connections from multiple clients, responsible for the management of the TCP session itself, data decompression and decryption, reducing the load on the game server |
| Message server | msgsv | Support online chat, instant messaging, guild and other social activities |
World server | worldsv | When using space division, all gmsv belonging to the world are connected to this server process, responsible for the processing of the common needs of each world |
| Universal database server | commondbsv | In the case of using parallel worlds, persistence of information that is common to all worlds. In addition to being the backend of msgsv, it is also used for real-time statistical processing and ranking rankings |
| Charge authentication server | authsv | Gateway to the settlement company |
| Log server | logsv | Collect logs from the entire server via TCP |
| DBMS | DBMS | Database Process |
| Settlement company server | Settlement sv | Server managed by the settlement company |

* gmsv, loginsv, msgsv, proxy These processes near the front of the service are called "front-end", otherwise they are called "back-end".
* Realize the line necessary to represent the connection of the server, the dotted line means that the line does not matter even if it is cut off.
* The part shown by the black quadrilateral means that you must make it yourself without using middleware, and the gray quadrilateral indicates the use of ready-made programs such as MySQL
* All gmsv are connected to dbsv, authsv, worldsv
* backup refers to backing up the database through simple copy
* gmsv can be increased to more than 3, the target is 20 ~ 30
* One gmsv corresponds to one proxy, more than two can also be
* msgsv and loginsv each is enough
* authsv usually does not require multiple

! [Process relationship using space copy method] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / process relationship using space copy method.png)

! [Simultaneous use of space copy method and process relationship in the case of parallel world] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Online game core technology and actual combat / simultaneous use of space copy method and Process relationship in the case of parallel world.png)

## 4.6 Bandwidth / server resource estimation

When estimating, the server is divided into "CPU-centric server" and "storage-centric server", their characteristics are as follows:
* CPU-centric server: faster CPU, more cores, general memory, small storage capacity, low fault tolerance, one-time
* Storage-centric servers: average CPU, average core, high memory, large storage capacity, high fault tolerance, long service life

The bandwidth estimate is based on the average amount of data required for each client to function properly

## 4.6 Development of protocol definition document

Basically, all inter-process communication uses TCP-based RPC. As long as you can define "in what order and how to call the communication between processes", you can complete the definition of the protocol. The protocol is stateless and simple operation Collection.

Excellent API call timing:
1. It is optimal not to call the API
2. Only call the API with no return value, unidirectional sequence diagram 
! [Unidirectional Timing Diagram] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network Game Core Technology and Actual Combat / Unidirectional Timing Diagram.png)
3. Triangular timing diagram that is called only once and then gets the return value
! [Triangle Timing Diagram] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network Game Core Technology and Actual Combat / Triangle Timing Diagram.png)
4. Jagged timing diagram
! [Zigzag Timing Diagram] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network Game Core Technology and Combat / Zigzag Timing Diagram.png)

! [Sequence diagram of authentication operation 1] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / sequence diagram of authentication operation 1.png)

! [Sequence diagram of authentication operation 2] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / sequence diagram of authentication operation 2.png)

! [Character creation timing diagram] (https://raw.githubusercontent.com/21moons/memo/master/res/img/network game core technology and actual combat / character creation timing diagram.png)

! [Timing diagram for logging in to gmsv] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / timing diagram for logging in to gmsv.png)

! [Timing diagram for logging in msgsv] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / timing diagram for logging in msgsv.png)

! [Timing chart for logging out from gmsv] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / Timing chart for logging out from gmsv.png)

! [Character movement in gmsv] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / character movement in gmsv.png)

! [Commercial operation of characters in gmsv] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / commercial operation of characters in gmsv.png)

! [msgsv add to the friends list, delete friends] (https://raw.githubusercontent.com/21moons/memo/master/res/img/network game core technology and actual combat / msgsv add to the friends list to delete friends.png )

! [Send message to online friends] (https://raw.githubusercontent.com/21moons/memo/master/res/img/core technology and actual combat of online games / send message to online friends.png)

## 4.11 Database design

## 4.12 Server / Client Software + Middleware

## 4.13 Basic principles in program development

* Data structure priority principle
* The principle of maintaining playability
* Backend server postponement development principle
* Continuous measurement principle (continuous measurement of system performance data during development)

The data classification in video games includes "basically unchanged parts" and "frequently changed parts". In general, objects in the game are represented by a list + Rtree.

Rtree is used for indexing of two-dimensional and multi-dimensional data. It is a multi-level balanced tree, which is an extension of Btree in multi-dimensional space. The data stored in Rtree is not the original data, but the minimum boundary rectangle of these data (Minimal -Bounding-Box, MBR).

[Introduction to RTree] (http://xiaobaoqiu.github.io/blog/2014/06/17/r-tree/)

# 5 [Practice] P2P MO game development

Frequent changes in the state of such games will generate a lot of data interaction. If implemented in C / S mode, the cost requirements for the server and bandwidth are too high, so it is not feasible from a commercial point of view. Therefore, even if you use game plug-ins to cheat The risk of P2P is also reasonable from an economic perspective.
"Synchronized shared memory is a unique method often used in P2P MO game development.

### 5.1.2 RPC and shared memory

Comparison of RPC development method and shared memory development method

| | RPC Development Mode | Shared Memory Development Mode |
| ------ | ------ | ------ |
| Traffic | Minimal | There will be redundant communication |
| CPU load | Minimum | A small amount of extra computation |
| Maximum simultaneous connections | Thousands | Dozens to 100 |
| Development difficulty | Complex | Simple but large amount of code |
| Communication delay | No difference | No difference |
| Game Type | Low Immediateness | High Immediateness |
| Game data scale | Large amount of data (more than millions of objects) | Small amount of data (hundreds to thousands of objects) |
| Communication | One-to-many | One-to-all |
| Development timing of data synchronization | Development is needed at the beginning | Can be expanded after the stand-alone version |

### 5.1.3 Features of P2P MO Games--Comparison and Difficulties with C / S MMO Games

* Does not require a lot of data interaction
* Configuration information is visible to players
* The security of game data changes cannot be strictly guaranteed (cheat is possible)
* There are many NAT network environments where P2P connection is not possible
* Can't simply update the game
* Inconvenient to combine social network and other network services
* There are many cases where players are offline

### 5.1.4 Advantages of P2P MO games
* Less delay
* Low bandwidth load of game server (even no load)
* Low cost of game server hardware (even zero cost)
* Game can be played normally during server maintenance

### 5.3.1 P2P MO game system basic structure diagram

P2P MO type games do not have a dedicated server, all players only communicate with the client of the common game

! [Possible bottleneck of P2P MMO architecture] (https://raw.githubusercontent.com/21moons/memo/master/res/img/Network game core technology and actual combat / P2PMO system possible bottleneck.png)

! [P2P MMO system basic composition] (https://raw.githubusercontent.com/21moons/memo/master/res/img/network game core technology and actual combat / P2PMO system basic composition.png)

### 5.3.2 Process diagram

C / S MMO games include client programs, game servers, login servers, authentication servers and database servers, but for P2P MO games, there is only one game client, so you do n’t need to make a system relationship diagram data of.

* Star topology or mesh topology
The difference between the star topology and the mesh topology is the number of link hops required to share client data. The star topology is 2 (requires forwarding through nodes), and the mesh topology is 1 (direct connection). Although the topology topology has a small delay, there may be a problem of no communication between nodes. Therefore, in the case of not pursuing speed, the star topology is selected. The forwarding nodes in the star topology may introduce a single point of failure, and elect other The node is the new forwarding node.

* Synchronous, asynchronous or browser-based
Because the game is based on Internet communication, considering the complexity and instability of the network, excluding the synchronization method, and because the game does not need to support a large number of users, it was decided to implement it in an asynchronous manner

### 5.3.2 Implementation of "Shared Memory Mode"-Start coding

As the name implies, shared memory is to share the same memory data to different processes. The specific data that needs to be shared includes the coordinates, types, and movement directions of the animal body.

Points to note about shared memory:
1. the state of competition
2. Lock processing

### 5.5.8 How to code for shared memory development--Comparison of shared memory development and RPC development

RPC development method--The RPC development method adopted by the C / S MMO game updates the game progress data in the following order:
* Players perform character movement operations on the client (program).
* The client sends an RPC request for operation to the server.
* After the server receives the request of the move (5, 5) function, it first checks whether the parameters (5, 5) are correct, and if it is correct, updates the position coordinates of the character to (5, 5) in the game progress data.
* After the server successfully updates the data, synchronize the new value to other clients.

When using the RPC development method, the "change request" is transmitted on the network, not the "result of change".

Shared memory development method--P2P MO game
The shared memory development method used in P2P MO games requires that the client and server share the same game data, and under this premise, the game data is processed in the following order.
* Players perform character movement operations on the client (program).
* The client detects whether the mobile operation is correct, and if there are no problems, it updates the game progress data managed by the machine.
* The client transmits the updated value to other clients.
* Other clients have no recommendation to receive updated data.

When using the shared memory method, the "change result" is transmitted on the network, not the "change request", so only the corresponding value needs to be replaced unconditionally.

The shared memory is easier to modify slightly, which is conducive to pursuing the advantages of higher quality. The disadvantage is that even if only a few values ​​have changed, communication is required, which may generate some extra traffic.

### 5.6.5 Relay server

Solve the problem that P2P MO game players cannot connect

# 6 Auxiliary system for online games--necessary mechanism for perfecting game services

# 7 Support the infrastructure of online game operation-architecture, load testing and operation

## 7.2 Infrastructure skills that developers need to know

### 7.2.3 Load curve

### 7.2.7 Server monitoring, life and death monitoring

### 7.2.8 Log output / management

### 7.4 Load Test

# 8 The development system of online games-the challenge of team management