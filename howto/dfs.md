[DFS](https://en.wikipedia.org/wiki/Channel_allocation_schemes#DFS) for 802.11s works when you set up encryption in the network (because this is the way where wpa-supplicant starts, and because DFS works through wpa-supplicant)

wpa-supplicant takes 1 minute scanning to get a free channel before it takes up

~~right now you cannot use mesh and access point in the same radio~~ Daniel fixed it (or is in its way)

"noscan" option in `/etc/config/wireless` allows you to use channels that are being used in the "secondary part" of a 40 MHz channel

Good practice: in tall places expect DFS, in short places don't expect DFS

[next commands play with dfs and wifi driver](../info/ath9k.md)
