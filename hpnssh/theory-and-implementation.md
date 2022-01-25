## Abstract {#abstract}

SCP and the underlying SSH protocol is network performance limited by statically defined internal flow control buffers. These buffers often end up acting as a brake on the network throughput of SCP especially on long and wide paths. Modifying the SSH code to allow the flow control buffers to be defined at run time eliminates this bottleneck.

 

## Problem {#problem}

High Bandwidth and High Latency links are becoming more prevalent in corporate and academic institutions. Applications that use windowing thus need to ensure that the window size is at least equal to the Bandwidth Delay Product, or BDP, are to obtain maximum utilization of the link. The BDP is the product of the narrowest portion of the network path and the round trip delay time and represents the total data carrying capactity of the path. For TCP it is already possible to tune the tcp window size manually or use an autotuning mechanism, such as the Web100 linux kernel patch to ensure maximum throughput with TCP. However, when applications above the TCP layer implement windowing, the limitation on throughput then becomes the less of either TCP or the application. In OpenSSH the limitation appears in the static window sizes that appear in channels.h as defined values.

## Solution {#solution}

Modifying the static size to be a larger value would only serve to waste space in the event that it is larger than the underlying protocol’s window size. Asking the user to specify the size also presents the problem of requiring users to be knowledgable in network performance tuning. Adjusting the size of window to be large enough so that it is no longer the limitation on throughput, but not much larger than it needs to be in order to obtain the desired performance would be the ideal solution.

There were only two changes needed to adjust the SSH window based on the TCP window. One was to enable the buffer code to allocate larger sizes. This was done using a variable that replaced the constant that was the maximum size allowed by the buffer code, and a function to modify the variable’s default value to something larger. The second change was to get the TCP window size from getsockopt and adjust the window size to match, but only if the new size was larger than the old one. The returned value from getsockopt is also doubled because OpenSSH only sends a WINDOW_ADJUST message when the window is half full in order to save on the number of WINDOW_ADJUST messages sent with a cost of doubling the buffer size.

## Tests {#tests}

The following hosts were used in the performance tests. kirana was running a 2.6 linux kernel with the Web100 patch. tg-login was runing a 2.6 kernel without autotuning, but a tcp window size of 10,000,000 bytes. The link BDP of a 1Gbps with a 0.04 second delay is 40,000,000 bits or 5,000,000 bytes. The 300MB file was copied from /dev/shm on one machine to /dev/null on the other.

### Hosts:

- **kirana.psc.edu**
   - Dual PIII 1.0Ghz (Coppermine)
   - 1Gig RAM
   - GigaBit Ethernet
- **tg-login.ncsa.teragrid.org**
   - Quad Itanium2 1.3Ghz
   - 8Gig Ram
   - GigaBit Ethernet


### Traceroute log:
<table class="table-striped">
<tbody>
<tr>
<td>1</td>
<td>bar-kirana-ge-0-2-0-0.psc.net</td>
<td>(192.88.115.169)</td>
<td>0.292 ms</td>
<td>9.452 ms</td>
<td>0.204 ms</td>
</tr>
<tr>
<td>2</td>
<td>beast-bar-g4-0-1.psc.net</td>
<td>(192.88.115.18)</td>
<td>0.129 ms</td>
<td>0.099 ms</td>
<td>0.094 ms</td>
</tr>
<tr>
<td>3</td>
<td>abilene-psc.abilene.ucaid.edu</td>
<td>(192.88.115.124)</td>
<td>9.801 ms</td>
<td>9.792 ms</td>
<td>9.805 ms</td>
</tr>
<tr>
<td>4</td>
<td>nycmng-washng.abilene.ucaid.edu</td>
<td>(198.32.8.84)</td>
<td>14.042 ms</td>
<td>14.036 ms</td>
<td>14.138 ms</td>
</tr>
<tr>
<td>5</td>
<td>chinng-nycmng.abilene.ucaid.edu</td>
<td>(198.32.8.82)</td>
<td>34.341 ms</td>
<td>41.711 ms</td>
<td>34.326 ms</td>
</tr>
<tr>
<td>6</td>
<td>mren-chin-ge.abilene.ucaid.edu</td>
<td>(198.32.11.98)</td>
<td>34.421 ms</td>
<td>34.466 ms</td>
<td>34.417 ms</td>
</tr>
<tr>
<td>7</td>
<td>sbr0-lsd6509.gw.ncsa.edu</td>
<td>(198.17.196.1)</td>
<td>36.957 ms</td>
<td>36.949 ms</td>
<td>36.920ms</td>
</tr>
<tr>
<td>8</td>
<td>acb-2-vlan101.gw.ncsa.edu</td>
<td>(141.142.0.6)</td>
<td>37.010 ms</td>
<td>36.957 ms</td>
<td>36.943ms</td>
</tr>
<tr>
<td>9</td>
<td>core-10-acb-2.gw.ncsa.edu</td>
<td>(141.142.0.133)</td>
<td>37.091 ms</td>
<td>36.965 ms</td>
<td>36.958 ms</td>
</tr>
<tr>
<td>10</td>
<td>hg-core-core-10.gw.ncsa.edu</td>
<td>(141.142.0.138)</td>
<td>38.300 ms</td>
<td>38.866 ms</td>
<td>38.312 ms</td>
</tr>
<tr>
<td>11</td>
<td>hg-1-hg-core.ncsa.teragrid.org</td>
<td>(141.142.47.34)</td>
<td>38.739 ms</td>
<td>39.187 ms</td>
<td>38.340 ms</td>
</tr>
<tr>
<td>12</td>
<td>tg-login1.ncsa.teragrid.org</td>
<td>(141.142.48.5)</td>
<td>36.996 ms</td>
<td>36.959 ms</td>
<td>36.950 ms</td>
</tr>
</tbody>
</table>
&nbsp;
<h2 id="scp">Unmodified SCP Performance</h2>
<table class="table-striped">
<tbody>
<tr>
<td>3des-cbc</td>
<td>1.3MB/s</td>
</tr>
<tr>
<td>arcfour</td>
<td>1.9MB/s</td>
</tr>
<tr>
<td>aes192-cbc</td>
<td>1.8MB/s</td>
</tr>
<tr>
<td>aes256-cbc</td>
<td>1.8MB/s</td>
</tr>
<tr>
<td>aes128-ctr</td>
<td>1.9MB/s</td>
</tr>
<tr>
<td>aes192-ctr</td>
<td>1.8MB/s</td>
</tr>
<tr>
<td>aes256-ctr</td>
<td>1.8MB/s</td>
</tr>
<tr>
<td>blowfish-cbc</td>
<td>1.9MB/s</td>
</tr>
<tr>
<td>cast128-cbc</td>
<td>1.7MB/s</td>
</tr>
<tr>
<td>rijndael-cbc@lysator.liu.se</td>
<td>1.8MB/s</td>
</tr>
</tbody>
</table>
 

## Unmodified SCP Performance
<table class="table-striped">
<tbody>
<tr>
<td>3des-cbc</td>
<td>1.3MB/s</td>
</tr>
<tr>
<td>arcfour</td>
<td>1.9MB/s</td>
</tr>
<tr>
<td>aes192-cbc</td>
<td>1.8MB/s</td>
</tr>
<tr>
<td>aes256-cbc</td>
<td>1.8MB/s</td>
</tr>
<tr>
<td>aes128-ctr</td>
<td>1.9MB/s</td>
</tr>
<tr>
<td>aes192-ctr</td>
<td>1.8MB/s</td>
</tr>
<tr>
<td>aes256-ctr</td>
<td>1.8MB/s</td>
</tr>
<tr>
<td>blowfish-cbc</td>
<td>1.9MB/s</td>
</tr>
<tr>
<td>cast128-cbc</td>
<td>1.7MB/s</td>
</tr>
<tr>
<td>rijndael-cbc@lysator.liu.se</td>
<td>1.8MB/s</td>
</tr>
</tbody>
</table>
 

## Modified SCP Performance
<img class=" size-full wp-image-261" src="/wp-content/uploads/2012/05/scp-mod.png" style="width: 100%;" alt="Graph of Modified SCP" width="640" height="480" />
<table class="table-striped">
<tbody>
<tr>
<td>3des-cbc</td>
<td>2.8MB/s</td>
</tr>
<tr>
<td>arcfour</td>
<td>24.4MB/s</td>
</tr>
<tr>
<td>aes192-cbc</td>
<td>13.3MB/s</td>
</tr>
<tr>
<td>aes256-cbc</td>
<td>11.7MB/s</td>
</tr>
<tr>
<td>aes128-ctr</td>
<td>12.7MB/s</td>
</tr>
<tr>
<td>aes192-ctr</td>
<td>11.7MB/s</td>
</tr>
<tr>
<td>aes256-ctr</td>
<td>11.3MB/s</td>
</tr>
<tr>
<td>blowfish-cbc</td>
<td>16.3MB/s</td>
</tr>
<tr>
<td>cast128-cbc</td>
<td>7.9MB/s</td>
</tr>
<tr>
<td>rijndael-cbc@lysator.liu.se</td>
<td>12.2MB/s</td>
</tr>
</tbody>
</table>
 

## Analysis {#analysis}

The tests showed that throughput was increased dramatically, and the limitation was no longer the TCP or SSH window size, but the ability of the host to encrypt at a rate fast enough to send out over the Gigabit Ethernet. This is clearly demonstrated by the vast performance difference between 3des-cbc, the slowest cipher, and arcfour, the fastest cipher.

## Security implications {#security}

There are no implications that we know of with the following caveat: The use of the none cipher in the experimental hpn+none patch is experimental and you must use it at your own risk. Its use via the -z switch in scp will transfer your bulk data in the clear even though your authentication is encrypted. This should, natually, be seen as riskier than transfering data via an encryption cipher. Also, while we did our best to make sure that you can only use the none cipher to transfer bulk data via scp it may be possible to run an interactive session with the none cipher (see the note of 15 January 2005). We're investigating this but we think this situation to be unlikely. If you have issues with this, use the approved non-experimental hpn patch.