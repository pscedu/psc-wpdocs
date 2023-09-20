## What is HPN-SSH?

HPN-SSH is a series of modifications to OpenSSH, the predominant implementation of the ssh protocol. It was originally developed to address performance issues when using ssh on high speed long distance networks (also known as Long Fat Networks: LFNs). By taking advantage of automatically optimized receive buffers HPN-SSH could improve performance dramatically on these paths. Later advances include; disabling encryption after authentication to transport non-sensitive bulk data, modifying the AES-CTR cipher to use multiple CPU cores, more detailed connection logging, and peak throughput values in the scp progress bar. More information can be found on HPN-SSH page on the PSC website.

## Our Latest Release
The latest release of HPN-SSH is 18.1.0 built against OpenSSH 9.4. This version includes a much improved ChaCha20-Poly1305 cipher, which is the default for both HPN-SSH and OpenSSH. The implemtation of the cipher is parallized to move the hard work of computing the keystream to independent threads. It also uses an optimized version of the Poly1305 methods found in OpenSSL. In comparison to OpenSSH 9.4 this version is 59% faster. 

Get the latest source code release from [https://github.com/rapier1/hpn-ssh ](). Packages for Ubuntu and Fedora will be availble from the PPAs listed below. 

## Developing the next version of HPN-SSH

The developers of HPN-SSH at the Pittsburgh Supercomputing Center (PSC) have recently received a grant from the National Science Foundation to develop and incorporate new features and optimizations. This grant will provide direct support to developers at PSC for two years. The goal of this grant (NSF Award#: 2004012) is to provide HPN-SSH with the level of performance required in modern high performance computing.

## What are you working on?

We’ve identified six different areas where we would like to focus our efforts. This is not meant to be an exhaustive list but is more of a starting point for our deliverables. Depending on community input this list may change to develop advances of highest interest. The six initial proposed areas of work are:

**Automatically resume failed transfers:** There is nothing quite as frustrating as having scp or sftp fail in the middle of a large transfer. Currently ssh does not have a mechanism to allow for failed transfers to restart from the point of failure. HPN-SSH is proposing to develop a mechanism to reliably resume failed transfers. We expect to do this by computing a hash of the partial file and compare it to a corresponding byte range of the original file. If these match then HPN-SSH will append the missing information to the partial file. If they do not match then the entire file will be transferred.

This is available as of HPN-SSH 15v4 for OpenSSH 8.7.

**Incorporate AES-NI into the AES-CTR cipher:** The AES-NI instruction set is a set of on die instructions that use hardware acceleration to increase the performance of common AES functions. The result is that on CPUs that support AES-NI the default AES-CTR cipher is faster than HPN-SSH’s multithreaded cipher. We will work on incorporating AES-NI into the multithreaded cipher. We expect that this will allow for faster transfers when ssh is CPU bound.

This is available as of HPN-SSH 15v4 for OpenSSH 8.7.

**Parallelization of CHACHA20 cipher:** CHACHA20 is a fast secure cipher that is the current default for OpenSSH. Initial investigation indicates that CHACHA20 can be transformed into a multithreaded cipher. This will allow the workload to be distributed across more CPU cores and should allow for faster transfers. We believe this will be important in situations where multiple users are simultaneously transferring files to the same host.

This is available as of HPN-SSH 18.1.0 for OpenSSH 9.4. 

**Inline Network Telemetry:** Sometimes figuring out why a ssh connection is underperforming is a difficult task. To help with diagnostics HPN-SSH will deploy network telemetry. In this diagnostic mode both the client and server will periodically query network statistics (such as retransmits, out of order packets, time spent buffer limited, and so forth) and store this data for analysis. This data may also be periodically displayed to the user. Initially this will be limited to unix variants that have the TCP_INFO struct (Linux and *BSD).

This is available as of HPN-SSH 15v5 for OpenSSH 8.8.

**Pipelining HMAC generation:** The Hash-based Message Authentication Code (HMAC) is a one way cryptographic hash used by ssh to ensure that a datagram has not been modified en-route between the hosts. This ensures that the data has not been subjected to a man in the middle attack. In OpenSSH this is a step in a very linear process. No other work can be conducted (such as encrypting other data) while the HMAC is being computed. In many cases this can act a bottleneck on throughput. HPN-SSH is proposing to pipeline this process in order to mitigate this bottleneck as much as possible.

Update: The primary method of computing the MAC now happens after encryption which prevents pipelining for working effectively. However, we've been able to significantly improve the performance of the UMAC used for the AES counter cipher. In our tests this resulted in a 20% performance improvement. However, this requires HPN-SSH being bult against OpenSSL 3.0. We've also improved the performance of the Poly1305 cipher used by the default cipher of Chacha20 by using assembly, as oppessed to C, to generate the MAC. While the assembler isn't supported on every platform it will fall back to using the C code if it isn't. 

**Packaging and Distribution:** HPN-SSH was, for a very long time, only available as a series of patches. Later it became a GitHub repo. This turned out to be a non-optimal method of distributing HPN-SSH to the public. With this in mind we will be working to provide precompiled packages for a variety of operating systems and Linux distributions and the creation of canonical package repositories (such as PPAs). We will also be reaching out to distribution maintainers to make HPN-SSH an option for all of their users.

Our first online repo is now available for Ubuntu. Add it with:

>`sudo add-apt-repository ppa:rapier1/hpnssh`

Fedora RPMs can be added with:
>`sudo dnf copr enable rapier1/hpnssh`

## What can I do to help? 

### Join our HPN-SSH community mailing list 

Stay up-to-date on progress and improvements to HPN-SSH by [joining this list, intended for both developers and users](https://lists.psc.edu/mailman/listinfo/hpnssh-community).

### Make a donation

If you care about HPN-SSH there is no better way to show your support than making a donation to the Pittsburgh Supercomputing Center. I do not personally receive any money from these donations but your support ends up supporting our work. Any amount is worth while - even a dollar will show PSC and CMU your support for our work. Seriously, show your support in order to both keep HPN-SSH current and fund new improvements.

To support HPN-SSH, go to the PSC giving page at [https://www.psc.edu/giving/](https://www.psc.edu/giving/) and click the "Give online" button. In the next window, choose "Add a designation" and note that it is to support HPN-SSH. Thank you!