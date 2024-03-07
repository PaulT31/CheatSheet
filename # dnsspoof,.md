# dnsspoof, 

dnsspoof forges replies to arbitrary DNS address / pointer queries on the LAN. This is useful in bypassing hostname-based access controls, or in implementing a variety of man-in-the-middle attacks.

## How to use dnsspoof ?
You will have to specify the interface on which you want the tool listen to, and also a list of DNS records as an 'hosts' file.
'dnsspoof [-i interface] [-f hostsfile] [expression]'