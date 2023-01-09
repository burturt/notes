## Problem
- We get a packet capture and some note about it being a layer 2 recording, with the title of the challenge referring to wifi
- Open up in wireshark:
![Screen Shot 2023-01-07 at 8.31.53 PM.png](../_resources/Screen%20Shot%202023-01-07%20at%208.31.53%20PM.png)
- What we have here is 802.11, which is the protocol for wifi. Since there are no non-802.11 packets, this is probably encrypted.
- Running `aircrack-ng -w rockyou.txt BobertsonNet.pcap` quickly gives us the password
- In order to make wireshark decrypt the packets, we need both the password and the wifi SSID (the name that shows up in the wifi list when connecting)
- To find this, open wireshark preferences --> column, and add a new custom column with the field `wlan.ssid`:
- ![Screen Shot 2023-01-07 at 8.35.03 PM.png](../_resources/Screen%20Shot%202023-01-07%20at%208.35.03%20PM.png)
- Now that the column is there, scroll down until you see the column filled in with a value - that is the SSID!
- To make wireshark decrypt the data, open preferences --> protocol --> IEEE 802.11 (yes its a long list), check "enable decryption" if it isn't already, and click "edit" next to decryption keys
- Make a new `wpa-pwd` type key with the key of `password:SSID`. Save and exit
- In the packet capture, the few packets of actual data has now been decrypted! To figure out what kind of data there is, go to Statistics --> Protocol Hirarchy. We see lots of TCP data transmission, so filter only tcp data by using the filter `tcp`
- Woah, lots of `RST, ACK` packets! Was someone port scanning? Let's see if there were any successful tcp connections, by looking for the syn ack packet: `tcp and tcp.flags.syn == 1 and tcp.flags.ack == 1`
- Success! One single connection was made successfully, and right click --> follow --> TCP Stream yields the flag
