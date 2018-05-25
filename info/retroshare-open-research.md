Notes about retroshare; documenting the experience

# Installation

Assuming debian

Use official download procedure

```
Debian 9

    #1 wget -qO - http://download.opensuse.org/repositories/network:retroshare/Debian_9.0/Release.key | sudo apt-key add -
    #2 sudo sh -c "echo 'deb http://download.opensuse.org/repositories/network:/retroshare/Debian_9.0/ /' >> /etc/apt/sources.list.d/retroshare.list"

And then install RetroShare with the following commands:

    #3 sudo apt-get update
    #4 sudo apt-get install retroshare
```

src http://retroshare.net/downloads.html (available other OS: windows, Mac OS, other gnu/linux distros, etc.)

If you want to install on your computer: `sudo apt-get install retroshare`

If you want to install on your server: `sudo apt-get install retroshare-nogui`

# Permissions to trusted nodes

Question: When you add a friend, this friend can see your social graph by default. Is there a way to limit this feature?

Answer: Yes. Go to preferences / permissions and disable the disc service on the people you don't want to show this feature

# Identity

Question: How to use multiple identities?

Answer:

- People tab: right click *chat with this person as*
- Chats tab: right click and *enter this room as*
- Mail tab: ?
- Files: you can share files with your trusted nodes
- Channels: you can specify identity when you submit a comment
