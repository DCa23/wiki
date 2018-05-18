information about this fantastic wifi driver

# frequency jamming

TODO: enable option tx99 and try if it jams frequency

also known as TDNA (Time Division No Access) ;)

# debug


access to interesting stuff

    cd /sys/kernel/debug/ieee80211/phy0/ath9k

## dfs

[DFS](https://en.wikipedia.org/wiki/Channel_allocation_schemes#DFS) is a standard that affects some channels of 5 GHz

see if there is a radar

    cat dfs_stats

simulate a radar, as a way to test the wifi channel change

    echo 1 > defs_simulate_radar

## adjusting to customm bandwith

`chanbw` part allows you to subdivide the bandwidth of the channel to 5 MHz, this way, energy gets more concentrated. "this is how you can reach 100 km". TODO review how to use it (probably reading the linux kernel source code)

TODO: find how to use chan
