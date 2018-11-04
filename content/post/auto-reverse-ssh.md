+++
date = "2017-09-19T10:07:00"
draft = false
tags = ["SSH"]
title = "A Simple Bash Script to Reverse SSH Tunnel Automatically"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

While, this post will be very simple since it's more like a note for my personal use. If you'd like to know what is the SSH Tunnel, what is the SSH port forwarding, or how to reverse SSH tunnel, please Google it.

The following script will try to connect to another server automatically every 10 seconds. Remember to run it using `tmux` or `screen` so it can stay in the background when you are disconnected.

```
#!/bin/bash
while true;do
  RET=`ps ax | grep "ssh -f -N -R 19995:localhost:22" | grep -v "grep"`
  if [ "$RET" = "" ]; then
    echo "restart ssh server"
    ssh -f -N -R 19995:localhost:22 <username>@dslsrv1.rnet.missouri.edu
  fi
  sleep 10
done
```

Simply speaking, the above code will forward its own port 22 to port 19995 on dslsrv1. 
