## What is HPN-SSH?

HPN-SSH is a series of modifications to OpenSSH, the predominant implementation of the ssh protocol. It was originally developed to address performance issues when using ssh on high speed long distance networks (also known as Long Fat Networks: LFNs). By taking advantage of automatically optimized receive buffers HPN-SSH could improve performance dramatically on these paths. Later advances include; disabling encryption after authentication to transport non-sensitive bulk data, modifying the AES-CTR cipher to use multiple CPU cores, more detailed connection logging, and peak throughput values in the scp progress bar.

## Our Latest Release
The latest release of HPN-SSH is 18.4.2 built against OpenSSH 9.7. This version includes a much improved ChaCha20-Poly1305 cipher, which is the default for both HPN-SSH and OpenSSH. The implemtation of the cipher is parallized to move the hard work of computing the keystream to independent threads. It also uses an optimized version of the Poly1305 methods found in OpenSSL. In comparison to OpenSSH 9.4 this version is 59% faster. 

Get the latest source code release from [https://github.com/rapier1/hpn-ssh ](). Packages for Ubuntu and Fedora will be availble from the PPAs listed below. 

## Developing the next version of HPN-SSH

Development is a never ending process and, at times, requires the infusion of resources. Fortunately, the developers of HPN-SSH at the Pittsburgh Supercomputing Center (PSC) received a grant from the National Science Foundation to develop and incorporate new features and optimizations in 2020. This grant provided direct support to developers at PSC for four years. The goal of this grant (NSF Award#: 2004012) was to provide HPN-SSH with the level of performance required in modern high performance computing.

We're always seeking new sources of funding and other resources and we hope to have more grant funding in the future. If you or your organization can help please contact us or look at how to donate below. 

## What have we accomplished?

During our award we identified multiple areas where we wanted to focused our efforts. The following is not meant to be an exhaustive list, but highlights the major accomplishments made under this NSF award.

**Automatically resume failed transfers:** There is nothing quite as frustrating as having scp or sftp fail in the middle of a large transfer. Currently OpenSSH does not have a mechanism to allow for failed transfers to restart from the point of failure. HPN-SSH developed a mechanism to reliably resume failed transfers. We do this by computing a hash of the partial file and compare it to a corresponding byte range of the original file. If these match then HPN-SSH will append the missing information to the partial file. If they do not match then the entire file will be transferred. This is currently only available under hpnscp using the -Z switch

This is available as of HPN-SSH 15v4 for OpenSSH 8.7.

**Incorporate AES-NI into the AES-CTR cipher:** The AES-NI instruction set is a set of on die instructions that use hardware acceleration to increase the performance of common AES functions. The result is that on CPUs that support AES-NI the default AES-CTR cipher is faster than HPN-SSH’s multithreaded cipher. We have been able to incorporate AES-NI into the multithreaded cipher significnatly imrproving throughput with this cipher. Additional modification greatly reduced memory utilization and reduced the number of cores used to maintain the cipher stream

This is available as of HPN-SSH 15v4 for OpenSSH 8.7.

**Parallelization of CHACHA20 cipher:** CHACHA20 is a fast secure cipher that is the current default for OpenSSH. Our initial investigation indicated that CHACHA20 can be transformed into a multithreaded cipher. This allows the workload to be distributed across more CPU cores resulting in faster transfers.  This is the default cipher used by HPN-SSH. 

This is available as of HPN-SSH 18.1.0 for OpenSSH 9.4. 

**Inline Network Telemetry:** Sometimes figuring out why a ssh connection is underperforming is a difficult task. To help with diagnostics HPN-SSH deployed inline network telemetry. In this diagnostic mode both the client and server will periodically query network statistics (such as retransmits, out of order packets, time spent buffer limited, and so forth) and store this data for analysis. This functionality is limited to Unix variants that have the TCP_INFO struct (Linux and *BSD). You can enable inline network telemetry with the -oMetrics=yes option on the command line. 

This is available as of HPN-SSH 15v5 for OpenSSH 8.8.

**Packaging and Distribution:** HPN-SSH was, for a very long time, only available as a series of patches. Later it became a GitHub repo. This turned out to be a non-optimal method of distributing HPN-SSH to the public. With this in mind we will be working to provide precompiled packages for a variety of operating systems and Linux distributions and the creation of canonical package repositories (such as PPAs). We will also be reaching out to distribution maintainers to make HPN-SSH an option for all of their users.

Ubuntu repos are availavble from Launchpad. Add it with:

>`sudo add-apt-repository ppa:rapier1/hpnssh`

Fedora RPMs can be added via copr with:
>`sudo dnf copr enable rapier1/hpnssh`

## What's coming the future?

We recently were awarded a grant by the National Science Foundation to explore the creation of a self-sustaining organization to support the growth and development of HPN-SSH over the long term. After all, we've been doing this for 20 years and we'd like to be here for at least another 20. As part of this process we've been inerviewing a lot people - users, administrators, executives, and so forth to get an idea how we can continue to help with your data movement needs. We'll be planning out a development roadmap over the next few months and letting people know what we will be focusing on. Some of our potential work may include a GUI, integration of QUIC, more advanced server side logging, passkeys, and more. This is in addition to our continued work on maximizing the throughput performance and realiablity of HPN-SSH. 

## What can I do to help? 

### Join our HPN-SSH community mailing list 

Stay up-to-date on progress and improvements to HPN-SSH by [joining this list, intended for both developers and users](https://lists.psc.edu/mailman/listinfo/hpnssh-community).

You can also join our comversations on the github discussion page at https://github.com/rapier1/hpn-ssh/discussions

### Volunteer to be a package maintainer

We are looking for people that would be willing to take up the task of maintaining packages for various distributions. While you do not have to coordinate with us we do suggest that you let us know. We're happy to help with the process in any way that we can and we'll include instructions on how to get that package here.

### Make a suggestion, file a bug report, provide a fix, or submit some code

We're always look for new ideas and new people to help with HPN-SSH. So contribute code, make a suggestions, or provide fixes on your schedule. All pull requests will be reviewed and, if suitable, incorporated. Likewise, if you are having a problem feel free to reach out. We really do depend on bug reports from our users. We can't fix every issue you might be facing but we'll certainly do our best to help. 

### Make a donation

If you care about HPN-SSH there is no better way to show your support than making a donation to the Pittsburgh Supercomputing Center. I do not personally receive any money from these donations but your support ends up supporting our work. Any amount is worth while - even a dollar will show PSC and CMU your support for our work. Seriously, show your support in order to both keep HPN-SSH current and fund new improvements.

To support HPN-SSH, go to the PSC giving page at [https://www.psc.edu/giving/](https://www.psc.edu/giving/) and click the "Give online" button. In the next window, choose "Add a designation" and note that it is to support HPN-SSH. Thank you!


-- updated September 6, 2024 by Chris Rapier