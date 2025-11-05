# TCP Handshake

#### Find the MAC address:

<img width="532" height="322" alt="Screenshot 2025-11-05 090226" src="https://github.com/user-attachments/assets/d8aef0be-1c82-47f0-b388-3f75cee461fd" />

----------------------------------------------

#### Then have to send a SYN packet to get a resonse with info needed for the handshake:

<img width="930" height="205" alt="Screenshot 2025-11-05 090906" src="https://github.com/user-attachments/assets/7753c889-a1f7-4717-97f4-2904410b06fa" />

---------------------------------------------

#### Then craft the packet with the returned values. Need to update the "seq" to match the 10.0.0.2 "ack", and then update the ack to match the 10.0.0.2 seq+1:

<img width="922" height="72" alt="Screenshot 2025-11-05 091514" src="https://github.com/user-attachments/assets/fd059eb1-e089-4447-9c78-a87ce7c69a90" />
