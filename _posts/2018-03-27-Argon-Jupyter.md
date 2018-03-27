---
title: "Jupyter Notebook on UIowa's HPCs: An Example of Using Argon"
layout: post
date: 2018-03-27 17:00
headerImage: false
tag:
- uiowa
- hpc
- python
category: blog
hidden: false
author: zhiyzuo
description: use jupyter notebook on uiowa hpc argon
---

I have been working a lot with our university's [high performance computing](https://hpc.uiowa.edu/) resources lately. The new system, [Argon](https://wiki.uiowa.edu/display/hpcdocs/Argon+Cluster), is a very cool and exciting resource, each of whose node has 56 cores! It enables parallelization of data analysis, especially for those [embarrassingly parallel tasks](https://en.wikipedia.org/wiki/Embarrassingly_parallel). I myself needs a lot of cores for data preprocessing and preliminary analyses and Argon really helps a lot.

However, in the past semesters, I found it not as convenient if I need to submit jobs all the time. Interactive is what I wanted. Therefore, I wanted to run [Jupyter notebooks](http://jupyter.org/) on the computing nodes. On asking the HPC admins (and thanks to them), I got to know that [port forwarding](https://superuser.com/questions/284051/what-is-port-forwarding-and-what-is-it-used-for) can enable us access the notebook from the ___login node___. However, this is not enough for me because login node can hardly handle heavy computing. With a bit of explorations, I find that running a Jupyter notebook from computing nodes are indeed possible by ___doing port forwarding twice___!


### Interactive sessions
To get Jupyter notebook run on the compute nodes, we need to use [`qlogin`](https://wiki.uiowa.edu/display/hpcdocs/Qlogin+for+Interactive+Sessions) to start interactive sessions. A simple usage (that fits my own need) is 
```bash
$ qlogin -q QUEUE_NAME -pe smp CORE_NUMBER
```
where `QUEUE_NAME` is the queue to submit to; `CORE_NUMBER` is the number of cores requested. Without the second argument `-pe`, a full node will be used (i.e., 56 cores).

After `qlogin`, nothing will be changed except the host name, which will be the name of the compute node (e.g., `argon-mm-compute-4-06`). Once we are on the compute node, we can start the notebook server, ___without prompting the browser___, simply because no user interface is avaiable on HPC systems:
```bash
$ nohup jupyter notebook --no-browser --port 9898 > jupyter.log &
```
I like to use `nohup`, along with `> jupyter.log &` to make it run in the background and log any outputs into a specified file. One can specify any eligible port number. I like to stick with one choice so that I do not noeed to change it every time.

### Port forwarding
Once we get Jupyter notebook to run on the compute node, we first forward the port number to the login node. My way is to run the following:
```bash
$ nohup ssh -L 9898:localhost:9898 COMPUTE-NODE -N > port_forward.log &
```
where `9898` is the port number I choose to use for both the compute and login node, just to make it simple. One can actually use different port numbers; `COMPUTE-NODE` is the host name of the compute node. Again, I make it run in the background for convenience.

The final step is just to repeat the above step on your own computer. The only change is to replace `COMPUTE-NODE` by `LOGIN-NODE` host name. This can be found in the terminal. In Argon, there are two login nodes: `argon-login-1.hpc.uiowa.edu` or `argon-login-2.hpc.uiowa.edu`.

### Access the notebook on your local browser
Now, to access the notebooks, we need to find out the full URL on the compute node, where we start the notebook server. I usually use `less` to quickly look at the log and get the address. For example:
```bash
$ less jupyter.log
```
Then we can copy the line similar to the address below and open it in our own browser:
```log
Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:9898/?token=xxxxxxxxxxxxxxxxxxxxxxx
```
Finally we can enjoy both the interactive notebooks with the amazing computing resources of our HPC systems!

### Stop the port fowarding
To kill the processes, we can use the following to find out the `PID` of the port forwarding processes and kill it:
```bash
$ ps aux | grep ssh | grep HAWKID
$ kill -9 PID
```
where `HAWKID` is the hawk id so that we can filter out processes related to ourselves; `PID` is in the number in the second column. We can find out the `PID` for Jupyter notebook in a similar way.
