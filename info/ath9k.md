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

example output of `dfs_stats` without radars detected

```
DFS support for macVersion = 0x80, macRev = 0x2: enabled
Pulse detector statistics:
    pulse events reported    :          0
    invalid pulse events     :          0
    DFS pulses detected      :          0
    Datalen discards         :          0
    RSSI discards            :          0
    BW info discards         :          0
    Primary channel pulses   :          0
    Secondary channel pulses :          0
    Dual channel pulses      :          0
Radar detector statistics (current DFS region: 0)
    Pulse events processed   :          0
    Radars detected          :          0
Global Pool statistics:
    Pool references          :          0
    Pulses allocated         :          0
    Pulses alloc error       :          0
    Pulses in use            :          0
    Seqs. allocated          :          0
    Seqs. alloc error        :          0
    Seqs. in use             :          0

```

simulate a radar, as a way to test the wifi channel change

    echo 1 > defs_simulate_radar

## adjusting to customm bandwith

`chanbw` part allows you to subdivide the bandwidth of the channel to 5 MHz, this way, energy gets more concentrated. "this is how you can reach 100 km". TODO review how to use it (probably reading the linux kernel source code)

TODO: find how to use chan
