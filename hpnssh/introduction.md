## In this page
- [Abstract/Introduction](#abstract)
- [Patches](#patches)

### Abstract

SCP and the underlying SSH2 protocol implementation in OpenSSH is network performance limited by statically defined internal flow control buffers. These buffers often end up acting as a bottleneck for network throughput of SCP, especially on long and high bandwidth network links. Modifying the ssh code to allow the buffers to be defined at run time eliminates this bottleneck. We have created a patch that will remove the bottlenecks in OpenSSH and is fully interoperable with other servers and clients. In addition HPN clients will be able to download faster from non HPN servers, and HPN servers will be able to receive uploads faster from non HPN clients. However, the host receiving the data must have a properly tuned TCP/IP stack. Please refer to [this tuning page](https://fasterdata.es.net/host-tuning/) for more information.

The amount of improvement any specific user will see is dependent on a number of issues. Transfer rates cannot exceed the capacity of the network nor the throughput of the I/O subsystem including the disk and memory speed. The improvement will also be highly influenced by the capacity of the processor to perform the encryption and decryption. Less computational expensive ciphers will often provide better throughput than more complex ciphers.

### Performance Gap

With many high bandwidth connections, there is a performance gap between what SSH is capable of and what the network link has the capacity to do. The difference between these two numbers is the performance gap, or the underutilized portion of your network connection. This gap, in most situations, is the direct cause of undersized receive buffers in the SSH congestion control mechanism. The graph below shows the optimal receive buffer versus the effective SSH channel receive buffer for various round trip times along a 100Mbps path

<figure>
<img class=" size-full wp-image-250" src="/wp-content/uploads/2020/11/bdp-v-ssh-win.jpg" alt="Graph of optimal receive buffer vs. the effective SSH channel receive buffer" width="506" height="389" />
<figcaption>The difference between the red and blue line is, essentially, wasted throughput potential along the path.</figcaption>
</figure>

<h3 id="scp">Normal vs. HPN SCP Performance</h3>

The effect of raising the SSH buffer sizes can be seen in the following chart. The standard SSH throughput, represented by the red columns, closely matches the expected throughput for this path if the receive buffer was limited to 64KB. By increasing the size of the SSH channel receive buffers throughput, represented by the blue columns, improved by as much as 1000%. The variation now seen is due to the complexity of the cipher and the limits of the hard drive.

<figure>
<img class=" size-full wp-image-251" src="/wp-content/uploads/2020/11/hpn-v-ssh-tput.jpg" alt="Chart showing the effect of increasing the SSH buffer size on throughput" width="500" height="340" />
<figcaption>Clearly, the HPN patches significantly boost throughput performance. This enhancement is entirely from tuning the SSH buffer sizes.</figcaption>
</figure>


### Patches

All patches should be applied to the [OpenSSH source files](https://www.openssh.com/) using the 'patch' utility from the command line. Building SSH from source is actually quite easy and the recommended method. Binary packages are available for CentOS, Fedora, and Ubuntu. 

### HPN-15
The 15th revision series of HPNSSH introduces a number of changes. First, the prior versions of the AES-CTR multithreading cipher did not make use of AES-NI. This is a set of on cpu hardware instructions that accelerate the computation of the AES cipher. As such, on some systems the multithreaded cipher was slower than the single threaded version. This has been changed to make use of OpenSSL's AES instructions which will automatically use AES-NI if it is available. 

This revision series also includes automatic resumption of failed transfers with SCP, gathering remote and local TCP stack metrics to aid in performance diagnostics, and performance improvements made through normalizing internal buffer sizes. Please see the [What are you working on?](https://www.psc.edu/hpn-ssh-home) section for more information about these improvements.

This series is available from GitHub at [https://github.com/rapier1/openssh-portable](https://github.com/rapier1/openssh-portable), as binary packages from [https://sourceforge.net/projects/hpnssh](https://sourceforge.net/projects/hpnssh), and for Ubuntu as a ppa on [Launchpad](https://launchpad.net/~rapier1). For the ppa use `sudo add-apt-package ppa:rapier1/hpnssh` to add it to your package list.  

At this time we will not be breaking down features into independent patch sets as we did with HPN14. This may change in the not so distant future. It's just a lot of extra work and without specific user interest we'd like to focus on other tasks. 

### HPN-14

This is the 1st revision of the 14th major version of the HPN patch set. The HPN13 and HPN12 patch sets are available from [https://sourceforge.net/projects/hpnssh](https://sourceforge.net/projects/hpnssh). There are two fundamental differences between the HPN13 and HPN14 patch set. The most significant of these is the inclusion of fully functional Multi-Threaded AES CTR (MT-AES-CTR) mode cipher. A [paper](www.psc.edu/wp-content/uploads/2020/12/a11-rapier.pdf) and [presentation](https://www.psc.edu/wp-content/uploads/2020/12/MG08-rapier-bennett.pdf) about this work are available. The previous version of MT-AES-CTR failed when the process forked to the background or, starting in OpenSSH 6.1, when the rlimit sandbox was used. In the former case the threads lost their context from the parent during the fork. In the latter, the rlimit prevented the creation of new threads/processes by setting NPROCS to 0. This issue was resolved by using the single process AES CTR cipher during the pre-authentication phase. After authentication takes place the pointer to the AES CTR cipher was replaced with a pointer to the MT-AES-CTR cipher. The application then forces a rekeying to take place which starts up the threads. No real change was made to the cipher itself - just how it was being called in SSH.

This cipher mode introduces multi-threading into the OpenSSH application in order to allow it to make full use of CPU resources available on multi-core systems. As the canonical distribution of OpenSSH is unable to make use of more than one core, high performance transfers can be bottlenecked by the cryptographic overhead. HPN12 dealt with this by the introduction of None Cipher Switching. However, this technique is limited to those users who are willing to allow their data to be transferred without encipherment. It also was, by design, limited to bulk data transfers which further restricts its value to some users. The MT-AES-CTR mode will allow users, on multicore platforms, to attain throughput rates comparable or equal to unencrypted data transfers. In both lab and real world tests throughput at full GigE line rates, with full encryption, were commonly seen.

<figure>
<img class=" size-full wp-image-254" src="/wp-content/uploads/2020/11/mt-aes-ctr-results.gif" alt="Image showing how MT AES-CTR breaks through the single core bottleneck" width="600" />
<figcaption>Obviously, the MT AES-CTR mode cipher breaks through the single core bottleneck.</figcaption>
</figure>

_Obviously, the MT AES-CTR mode cipher breaks through the single core bottleneck._

MT-AES-CTR produces a cipher-stream that is indistinguishable from the distributed Single Thread AES-CTR (ST-AES-CTR) mode cipher and is fully compatible with all other AES-CTR mode implementations. In other words, it's completely backward compatible and will function in heterogenous connections with no problem. However, it is important to note MT-AES-CTR does impose additional overhead and may impose a performance penalty on single core machines. Additionally, the MT-AES-CTR mode cipher replaces the default ST-AES-CTR mode cipher post authentication.

The second major difference between HPN13 and HPN14 is that the NONE cipher switching routines have been split off into their own patch. There are some circumstances in which users may have need of the NONE cipher without the additional overhead of the dynamic windowing (packet radio under an amateur license for example). It also helps keep the patches a little cleaner. Please note, it's not always trivial to layer the patches on top of each other. If you don't have much experience dealing with reject files produced by diff I suggest making use of either the kitchen sink patch or the dynwindow-noneswitch patch sets.

 

**How to apply the patches:**

1. Get the OpenSSH source code from OpenSSH.org
2. Untar OpenSSH source
3. cd into the OpenSSH source directory
4. If gzipped, type `zcat pathtopatch/patchfile | patch -p1`
Otherwise type `patch -p1 < pathtopatch/patchfile`
5. Type `configure && make`
6. Type `make install`

<hr>

***Problems with buffer_append_space in HPN-SSH.*** If you are experiencing disconnects due to a failure in buffer_append_space please let us know. We're currently tracking some problems with this and we're trying to gather more information to help resolve it. You may want to try using `-oHPNBufferSize=16384` to restrict the growth of the buffer. Let us know if that helps.
