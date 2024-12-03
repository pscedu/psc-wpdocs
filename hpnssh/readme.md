## Configuration Options

These options can be used on the command line in the typical '-o Option ' format or embedded in the ssh\_config or sshd\_config files.

## LibreSSL Support
Changes in LibreSSL version 3.5 and 3.6 prevent the use of the threaded AES CTR cipher. 
In those cases HPNSSH will fallback to the serial versionof the AES CTR cipher. A warning
is printed to stderr. This was resolved in LibreSSL 3.7.

## Automatic Port Fallback
Starting with version 17v3 the hpnssh client now uses TCP port 2222 to connect automatically as this is the default hpnsshd port. However, we understand that many users will be end up connecting standard SSH servers on port 22. To make the easier for users the client will fall back to port 22 in the event that there is no hpnssh server running on port 2222. The behaviour can be modifed as follows:
<dl>
<dt>-oFallback=[yes|no] </dt> 
<dd>Enable or disable port fallback. Default is yes.</dd>
<dt>-oFallbackPort=[N] </dt>
<dd>N is the port number that should be used for fall back. Default is 22.</dd>
<dt> Example Usage:</dt>
<dd>Stop hpnssh from using sshd on port 22 if hpnsshd isn't found on port 2222

>`hpnssh -oFallback=no`

Have hpnssh fall back to port 2022 if hpnsshd isn't on port 2222
>`hpnssh -oFallbackPort=2022`
</dd>
</dl>

## Metrics options

This features allows the client to request tcp networking information from the
TCP_INFO struct. This includes data on retransmits, round trip time, lost packets,
data transferred, and the like. The metrics are polled periodically through the
life of the connection. By default this is every 5 seconds but users can pick different
polling periods. The resulting data is stored in two distinct files; one for local
metrics and one for remote metrics. Remote metrics are only available if the remote
supports this feature. This feature will *not* diagnose a poorly performing connection
but may provide insight into what is happening during the connection.
<dl>
<dt>Metrics=[yes|no]</dt>
<dd>This option will enable metrics polling. Default: No.</dd>

<dt>MetricsInterval=[N]</dt>
<dd> N is the polling period in seconds. Default: 5 seconds.</dd>

<dt>MetricsPath=[/filepath/filename]</dt>

<dd>This is the path to the files where the remote and local data will be stored. Default: `./ssh\_stack\_metrics.[local|remote]`.
    Any other option chosen by the user will have a .local or .remote suffix appended to it.
  </dd>
    </dl>
<b>Example usage:</b>

> `hpnssh -oMetrics=yes -oMetricsInterval=1 -oMetricsPath=/tmp/scp-test-results`

## SCP resume options

This feature allows SCP to resume failed transfers. In the event of a failed transfer
issues the same scp command with the '-Z' option. For example - if you issued:
> `hpnscp myhugefile me@host:~`

and it dies halfway through the transfer issuing
> `hpnscp -Z myhugefile me@host:~`
will resume the transfer at the point where it left off.

This is implemented by having the source host send a hash (blake2b512) of the file to the
target host. The target host then computes it's own hash of the target file. If the hashes match
then the file is skipped as this indicates a successful transfer. However, if the hashes do not
match then the target sends the source its hash along with the size of the file. The source then
computes the hash of the file *up to* the size of the target file. If those hashes match then
the source only send the necessary bytes to complete the transfer. If the hashes do not match then
the entire file is resent. If the target file is larger then the source file then the entire
source file is sent and any existing target file is overwritten.
## None cipher options
To use the NONE option you must have the NoneEnabled switch set on the server and
you must have both NoneEnabled and NoneSwitch set to yes on the client. The NONE
feature works with ALL ssh subsystems (as far as we can tell) as long as a tty is not
spawned. If a user uses the -T switch to prevent a tty being created the NONE cipher will
be disabled.
<dl>
<dt>NoneEnabled=[yes|no]</dt>
<dd>Default is no. Set to "yes" to enable the switch to no encryption after authentication. This option must be set to "yes" on the server as well as the client.</dd>
<dt>NoneMacEnabled=[yes|no]</dt>
<dd>Default is no. Set to "yes" to disable the message authentication cipher after authentication. This must be set to "yes" on both the client and server.</dd>
<dt>NoneCipherSwitch=[yes|no]</dt>
<dd>Default is no. If the NoneEnabled option is set to yes then the connection will disable encryption after authentication. If NoneMacEnabled and NoneEnabled is set to yes then both the cipher and MAC will be disabled after authentication. 
    </dd>
</dl>
<b>Example usage:</b>

> `scp -oNoneEnabled=yes -oNoneCipherSwitch=yes hugefile me@remote:~`

will, after authentication disable encryption while transferring `hugefile` from the local host to the remote host. The remote host must have `NoneEnabled=yes` set the in the sshd\_config file.

> `scp -oNoneEnabled=yes -oNoneMacEnabled=yes -oNoneCipherSwitch=yes hugefile me@remote:~`

will, after authentication disable both encryption *and* the MAC while transferring `hugefile` from the local host to the remote host. All 3 options must be set to yes on the client to disabled the MAC. The remote host must have both `NoneEnabled=yes` and `NoneMacEnabled=yes` set the in the sshd\_config file. 


## MULTI-THREADED AES CIPHER:
The AES cipher in CTR mode has been multithreaded (MTR-AES-CTR). This will allow ssh installations
on hosts with multiple cores to use more than one processing core during encryption.
Tests have show significant throughput performance increases when using MTR-AES-CTR up
to and including a full gigabit per second on quad core systems. It should be possible to
achieve full line rate on dual core systems but OS and data management overhead makes this
more difficult to achieve. The cipher stream from MTR-AES-CTR is entirely compatible with single thread AES-CTR (ST-AES-CTR) implementations and should be 100% backward compatible. Optimal performance requires the MTR-AES-CTR mode be enabled on both ends of the connection.
The MTR-AES-CTR replaces ST-AES-CTR and is used in exactly the same way with the same
nomenclature.

Usage examples:

>                `ssh -caes128-ctr you@host.com`
>                `scp -oCipher=aes256-ctr file you@host.com:~/file`

## MULTI-THREADED ChaCha20-Poly1305 CIPHER:
The default cipher used by HPN-SSH is now a threaded implementation of the
ChaCha20-Poly1305 cipher. In tests this cipher results in an approximately 90% gain in
throughput performance in comparison to the serial implementation found in OpenSSH.
This cipher is fully compatible with all known existing implementations of ChaCha20-Poly1305.

Internally this cipher is referred to as chacha20-poly1305-mt@hpnssh.org (CC20-MT)
similar to how the OpenSSH implementation is known as chacha20-poly1305@openssh.com
(CC20-S (for serial)). No changes are required on the part of the user to make use of CC20-MT.
However, if users don't want to make use of this cipher they can explicitly load CC20-S using `-cchach20-poly1305@openssh.com` on the command line.

## HPN specific options
 
<dl>
<dt>TcpRcvBufPoll=[yes/no] client/server </dt> 
<dd>Enable or disable the polling of the TCP receive buffer through the life of the connection. You would want to make sure that this option is enabled for systems making use of autotuning kernels (linux 2.4.24, 2.6, MS Vista). Default is no.
</dd>

<dt>NoneEnabled=[yes/no] client/server </dt> 
<dd>Enable or disable the use of the None cipher. Care must always be used when enabling this as it will allow users to send data in the clear. However, it is important to note that authentication information remains encrypted even if this option is enabled. Default is no.
</dd>

<dt>NoneMACEnabled=[yes/no] client/server </dt> 
<dd>Enable or disable the use of the Message Authenication Cipher (MAC). Care must always be used when enabling this as it offers no protection against man in the middle attacks. We suggest it only be used when performance is more important than data integrity and on known secure networks. Default is no.
</dd>

<dt>NoneSwitch=[yes/no] client </dt> 
<dd>Switch the encryption cipher being used to the None cipher after authentication takes place. NoneEnabled must be enabled on both the client and server side of the connection. When the connection switches to the NONE cipher a warning is sent to STDERR. The connection attempt will fail with an error if a client requests a NoneSwitch from the server that does not explicitly have NoneEnabled set to yes. Note: The NONE cipher cannot be used in interactive (shell) sessions and it will fail silently.  Default is no.
</dd>

<dt>HPNDisabled=[yes/no] client/server </dt> 
<dd>In some situations, such as transfers on a local area network, the impact of the HPN code produces a net decrease in performance. In these cases it is helpful to disable the HPN functionality. Default is no.
</dd>

<dt>DisableMTAES=[yes/no] client/server</dt>
<dd>Switch the encryption cipher being used from the multithreaded MT-AES-CTR cipher
back to the stock single-threaded AES-CTR cipher. Useful on modern processors with
AES-NI instructions which make the stock single-threaded AES-CTR cipher faster than
the multithreaded MT-AES-CTR cipher. Set to no by default.
</dd>
</dl>

### Credits: 
This patch was conceived, designed, and led by Chris Rapier (rapier@psc.edu) The majority of the actual coding for versions up to HPN12v1 was performed by Michael Stevens (mstevens@andrew.cmu.edu). The MT-AES-CTR cipher was implemented by Ben Bennet (ben@psc.edu) and improved by Mike Tasota (tasota@gmail.com) an NSF REU grant recipient for 2013. Mitchell Dorrell (mwd@psc.edu) has provided invaluable assistance on improving the performance of ChaCha20 and Poly1305 along with other critical work. Allan Jude provided the code for the NoneMac and buffer normalization. This work was financed, in part, by Cisco System, Inc., the National Library of Medicine, and the National Science Foundation.

### Sponsors: 
Thanks to Niklas Hambuchen for being the first sponsor of HPN-SSH via github's sponsor program! Join our sponsors at <a href="https://github.com/sponsors/rapier1">our github</a>, view our <a href="https://www.psc.edu/hpn-ssh-home/support/">funding page</a>, or contact at hpnssh@psc.edu. 


