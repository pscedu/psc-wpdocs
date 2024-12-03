***Q: What is HPN-SSH?***

A: HPN-SSH is a patch set designed to remove a networking bottleneck in the base OpenSSH code. Removing this bottleneck can improve performance drastically.

***Q: What is this bottleneck?***

A: SSH implements a multiplexed connection protocol so a single TCP/IP connection can host multiple SSH sessions at the same time. This means that SSH also has to implement a flow control mechanism in order to make sure that the network connection isn't overwhelmed. Much like TCP/IP, it uses a receive buffer to indicate how much data the sender should be sending at any one point. The developers of OpenSSH had initially set this buffer size to 64KiloBytes. This had been increased to 2MB several years ago but this is often too small for very high speed connections over long distances. HPN-SSH allows this buffer to grow well past 2MB allowing transfers at very high rates on long fat networks.

***Q: Will HPN-SSH help me?*** 

A: Maybe, it depends on a number of variables but the most important one is the speed of your network connection to the Internet. It also depends on the distance to the destination you are trying to reach. As a general rule of thumb, the farther away the destination and the faster your connection the greater the improvement will be. You can determine how much HPN-SSH will help by multiplying the bandwidth to the destination by the RTT (Round Trip Time). This is called the BDP (Bandwidth Delay Product) and is expressed as BDP = BW(B/s) * RTT(s) and gives you the number of bytes that can be in transit between any two hosts at one time. If this value is less than the previous mention receive buffer on the receiving host then the potential throughput of the connection will be near line rates. If the BDP is greater than the receive buffer the throughput will be limited in direct proportion to the difference between the BDP and the receive buffer. As a rule of thumb you will generally need around a 1Gb/s connection to the internet to see a benefit from HPN-SSH. 

This assumes that the processor, hard drive, memory, application, and other components of the system are fast enough to transfer data at line rates. You just won't be able to get GigE speeds out of a PC from 1987 regardless of how fast your network connection is.

***Q: I installed HPN-SSH but there is no improvement. Why not?***

A: There are many possible answers to this but its important to understand that HPN-SSH will not make every transfer faster. Transfers in a local area network will not be improved by HPN-SSH and in some case might even be slower (in these cases use the -o HPNDisabled=yes option). You also have to make sure that your computer's network stacks are properly tuned. This is especially critical on the the receiver side of the connection. Please see [EsNet's TCP Tuning page](https://fasterdata.es.net/host-tuning/) for details on how to do this. You might also be limited by firewalls, packet loss, network errors, and other problems that can affect network performance.

***Q: How do I use HPN-SSH?***

A: Generally it's a direct replacement for SSH and can be used in the exact same way. However, depending on your operating system you may need to enable some options. For people using Linux kernels that support receive side autotuning (some 2.4 kernels and most 2.6 kernels) and Microsoft Windows Vista you want to use -o TCPRcvBufPoll=yes on the receiver side. This works for both the server and the client. If you do not enable TCPRcvBufPoll your SSH receive buffer will be limited to the initial receive buffer window size (64K for Vista and 85K for Linux). Please read either the HPN-README file distributed as part of the patch or the online version of the HPN-README file for more information on available options.

***Q: What is the NONE Cipher Switch?***

A: The NONE cipher switch disables data encryption AFTER you have been authenticated or logged into the remote host. This can significantly reduce the load on the CPUs of both machines and may improve performance even more. It's important to remember that the initial authentication process is still fully encrypted. Additionally, while the data is no longer encrypted each packet is still digitally signed and protected against in transit manipulation of the information. Anytime the NONE cipher is used a warning will be printed to screen saying "WARNING: NONE CIPHER ENABLED". If you do not see that warning then the None cipher is not in use.

***Q: Is it dangerous to use the NONE Cipher Switch?***

A: That depends entirely on what you are trying to do. First off, you can't use the NONE Cipher Switch in an interactive session and is designed to be only used in the transfer of bulk data - like with scp. Second, you should be aware of what kind of data you are transferring. If you are copying financial or medical data then you would not want to use the NONE cipher. However, if you are copying non-sensitive data like MP3s, archives, images, and so forth it may make sense to use the NONE Cipher Switch. You will have to make that determination yourself. Lastly, since the authentication process is still encrypted hackers and eavesdroppers will not be able to steal your password.

***Q: I have '-oNoneSwitch=yes' on the command line. Why doesn't it work?***

A: You must use both '-oNoneSwitch=yes and '-oNoneEnabled=yes' on the client command line. Only using one or the other will not work. Additionally, the None cipher must be enabled on the server with NoneEnabled=yes in the sshd_config file or on the command line. Anytime the None cipher is used a warning will be printed to screen saying "WARNING: NONE CIPHER ENABLED". If you do not see that warning then the NONE cipher is not in use.

***Q: I want the speed but I can't use the NONE cipher. Do I have any options?***

A: Yes. As of HPN13v1 we've introduced a multi-threaded AES-CTR (MT-AES-CTR) patch that will allow SSH to make use of multiple cores. This can significantly improve throughput performance. In our test environments we commonly see near GigE line rate speeds - more than a 100% improvement over the default AES-CTR mode cipher.

***Q: How do I use MT-AES-CTR?***

A: On the command line use either -oCipher=aes[128|192|256]-ctr or -caes[128|192|256]-ctr.

***Q: Is MT-AES-CTR compatible with single threaded versions?***

A: Yes, the resulting cipher stream is indistinguishable from the single threaded version and will be understood by all compliant implementations of AES-CTR.

***Q: I'm having a lot of problems using HPN-SSH. Can you help me?***

A: We will certainly do our best but please try see PSC's TCP Tuning page first. If you are still having problems then contact us at hpn-ssh@psc.edu and include the following information:

- The version of the operating system on both sides of the connection
- The version of HPN-SSH you are using
- As much information as you can about the network path (bandwidth, RTT, loss, etc)
- The level of throughput performance you expect to see and what you are seeing.
- The receive buffer sizes of the hosts
- Any other information you feel is important

We can't help everyone but we will do our best.

***Q: If this HPN-SSH is so great why isn't it in the OpenSSH code base?***

A: The HPN-SSH patch has been made available to the OpenSSH development team. However, the team has other priorities and I'm perfectly able to maintain this implementation. Quite a number of organizations are using HPN-SSH though, including NASA, Sun Microsystems, HP, financial companies, research organizations, supercomputing centers, security concerns, and software developers.

***Q: I have a suggestion for HPN-SSH or this FAQ, a comment, want to send thanks, or ask some other question. Who should I contact?***

A: Please contact us at hpn-ssh@psc.edu with any suggestions, questions, comments, or criticisms. 