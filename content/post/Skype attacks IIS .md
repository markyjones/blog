+++
date = "2012-06-01T11:00:01+01:00"
draft = false
title = "Skype attacks IIS"

+++

We are currently developing an IIS hosted .NET application at work. Eventually the whole app will be hosted through HTTPS so for now we are doing a lot of the development this way too, thus our local development servers are configured for HTTPS over port 443.

One of the devs on the project was setting up their machine yesterday and kept getting the following error in IIS when starting their website: "The process cannot access the file because it is being used by another process. (Exception from HRESULT: 0x80070020)".

 ![Untitled](/img/skype attacks IIS/error.png)

We had to find which process was doing the blocking:

```c#
c:\> netstat -ao
```

Gives us a list of all active connections. Look in the local address column for something on port 443 e.g. "0.0.0.0:443" grab the PID from the rightmost column in my case 2384.

Gives us a list of all active connections. Look in the local address column for something on port 443 e.g. "0.0.0.0:443" grab the PID from the rightmost column in my case 2384.
Now grab the name of the culprit:

```c#
c:\> tasklist /fi "PID eq 2384"

```

```c#
Image Name                     PID Session Name        Session#    Mem Usage
========================= ======== ================ =========== ============
Skype.exe                     2384 Console                    1    104,180 K
```

So go KILL that process and now IIS should be happy again.
