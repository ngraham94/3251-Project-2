# Assignment 2

## Building
Without running unit tests:

    ./gradlew assemble
    
With tests:

    ./gradlew build
    
## Running

Client:

    ./reldat-client.sh HOST:PORT WINDOW_SIZE

Server:

    ./reldat-server.sh PORT WINDOW_SIZE
    

## Design Documentation

### Header
Size in bits

SYN (1)        | ACK (1)          | FIN (1)
-------------- | ---------------- | ----------------
DATA_SIZE (32) | ---------------- | WINDOW_SIZE (32)
SEQ_NUM (32)   | ACK_NUM (32)     | CHECKSUM (64)


### Connection Opening / Termination
Opening: 3-way handshake similar to TCP (SYN, SYNACK, ACK)

Termination: One side sends a FIN packet to signal connection
termination. The receiver responds with a FINACK. If no
FINACK is received after a set timeout, the FIN sender
marks the connection closed.

### Duplicate Packets
Duplicate packet is detected when a packet has a matching
sequence number with a packet already in the buffer.
Duplicate packets are dropped.

### Corrupted/Lost Packets
Use checksum to detect corrupted packets. Drop corrupted
packet and do not send an ACK. Sender should resend the
packet on timeout. Lost packets are identified when an ACK
is not received within a timeout. Lost packets are resent.

### Reordered Packets
Packets can be accepted out of order as long as it falls
within the receive window. However, will only ACK highest
packet received in order (if [1, 2, 3, 4] are in window,
and 1, 2, 4 received, ACK 3).

### Bidirectional Data Transfer
A connection is negotiated with a 3-way handshake.
Both the client and server has send/receive buffer.
Sender uses a sequence number to identify the the packet,
and receiver ACKs based on next expected sequence number.
SEQ/ACK # are in bytes. If ACK not received after timeout,
resend all unacked packets in the window.

### Bytestream
Segment the stream into separate messages. A message
start with a 4-bit integer specifying the size of the
message.

### Important functions
These functions are all in the ReldatSocket class.

send(byte[])

Sends a byte array to the remote destination through
the established connection. This blocks until all the
data is sent or the connection is terminated.

receive(int)

Receives data from the remote socket through the
established connection. Blocks until the specified
length is received or the connection terminates.

accept()

Accepts a new connection from a client. Blocks until
a connection is received.

connect(SocketAddress)

Attempts to establish a connection with the specified
SocketAddress. This operation blocks until a connection
is established, or throws a ConnectException on timeout.


### FSMs

#### Client
![Client FSM](img/client-fsm.png)

#### Server
![Server FSM](img/server-fsm.png)

## Contributors
- Kelvin Chen <kelvin@gatech.edu>
- Nickolas Graham <ngraham7@gatech.edu>
