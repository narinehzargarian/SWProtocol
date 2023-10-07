# SWProtocol

**Sender**

The code is the implementation of Go-Back-N and uses a circular buffer to hold packets that are being transmitted and waits for acknowledgments (ACKs) from the receiver before transmitting additional packets.

The circular buffer is implemented as an array of packets of size `WND_SIZE`. The starting and ending indexes of the buffer are kept track of using the variables `s` and `e`, respectively. A flag `full` is set to 1 when the buffer is full. The variable `ack_expected` keeps track of the expected ACK number, and `eof` is a flag that is set to 1 when the end of the file has been reached. The variable `inWindow` is a flag that is set to 1 when the ACK number received is within the current window.

The code starts by sending the first packet, which is an ACK containing the payload, to the receiver. It then enters a loop that sends data packets up to the window size and waits for ACKs. The loop terminates when all packets have been sent and all ACKs have been received. If an ACK is received and it is an ACK that is expected, sender updates the circular buffer indexes, sets the `full` flag to 0. If a timeout occurs, the oldest unacknowledged packet is resent along with any subsequent unacknowledged packets.

**Receiver**

Upon receiving a packet, receiver matches the packet's sequence number with the expected sequence number. If the sequence numbers match, the payload is written to a file if th, an ACK packet is sent back to the client, and the next expected sequence number is updated.

If the received packet has a sequence number outside the receive window, an ACK packet with the expected sequence number is sent back to the client to request retransmission. The code handles the case where the upper bound of the receive window wraps around past the maximum sequence number.

If the received packet has a sequence number within the receive window but is not the expected packet, it is treated as a lost ACK and an ACK packet with the expected sequence number is sent back to the client.

