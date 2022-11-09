## Configuration Options

These options can be used on the command line in the typical '-o Option ' format or embedded in the ssh\_config or sshd\_config files.

## LibreSSL Support:
Changes in LibreSSL version 3.5 and 3.6 prevent the use of the threaded AES CTR cipher. 
In those cases HPNSSH will fallback to the serial versionof the AES CTR cipher. A warning
is printed to stderr. 

##Automatic Port Fallback
Starting with version 17v3 the hpnssh client now uses TCP port 2222 to connect automatically as this is the default hpnsshd port. However, we understand that many users will be end up connecting standard SSH servers on port 22. To make the easier for users the client will fall back to port 22 in the event that there is no hpnssh server running on port 2222. The behaviour can be modifed as follows:
<dl>
<dt>-oFallback=[yes|no] </dt> 
<dd>Enable or disable port fallback. Default is yes.</dd>
<dt>-oFallbackPort=[N] </dt>
<dd>N is the port number that should be used for fall back. Default is 22.</dd>

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
    Any other option chosen by the user will have a .local or .remote suffix appended to it.</dd>
<dt>Example usage</dt>
>ssh -oMetrics=yes -oMetricsInterval=1 -oMetricsPath=/tmp/scp-test-results
</dl>

## SCP resume options

This feature allows SCP to resume failed transfers. In the event of a failed transfer
issues the same scp command with the '-Z' option. For example - if you issued:
>'scp myhugefile me@host:~'

and it dies halfway through the transfer issuing
>'scp -Z myhugefile me@host:~'
will resume the transfer at the point where it left off.

SCP however, will use the first scp in the user's path. This might not support the resume
function and the attempt will fail. In those cases the user can explicitly define the path to the resume enabled scp with the '-z' option. This would be the path on the *remote* host. For example:

>'scp -Z -z /opt/hpnssh/usr/bin/scp myhugefile me@host:~'

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
<dd>Default is no. If the NoneEnabled option is set to yes then the connection will disabled encryption after authentication. If NoneMacEnabled and NoneEnabled is set to yes then both the cipher and MAC will be disabled after authentication. 
<dt>Example usage</dt>
<dd> >scp -oNoneEnabled=yes -oNoneCipherSwitch=yes hugefile me@remote:~

Will, after authentication disable encryption while transferring `hugefile` from the local host to the remote host. The remote host must have `NoneEnabled=yes` set the in the sshd\_config file.
>scp -oNoneEnabled=yes -oNoneMacEnabled=yes -oNoneCipherSwitch=yes hugefile me@remote:~

Will, after authentication disable both encryption *and* the MAC while transferring `hugefile` from the local host to the remote host. All 3 options must be set to yes on the client to disabled the MAC. The remote host must have both `NoneEnabled=yes` and `NoneMacEnabled=yes` set the in the sshd\_config file. 
</dd>
</dl>

## HPN specific options
 
<dl>
<dt>TcpRcvBuf=[int]KB client </dt> 
<dd>Set the TCP socket receive buffer to int Kilobytes. It can be set up to the maximum socket size allowed by the system. This is useful in situations where the TCP receive window is set low but the maximum buffer size is set higher (as is typical). This works on a per TCP connection basis. You can also use this to artificially limit the transfer rate of the connection. In these cases the throughput will be no more than n/RTT. The minimum buffer size is 1KB . Default is the current system wide TCP receive buffer size.
</dd>

<dt>TcpRcvBufPoll=[yes/no] client/server </dt> 
<dd>Enable or disable the polling of the TCP receive buffer through the life of the connection. You would want to make sure that this option is enabled for systems making use of autotuning kernels (linux 2.4.24, 2.6, MS Vista). Default is no.
</dd>

<dt>NoneEnabled=[yes/no] client/server </dt> 
<dd>Enable or disable the use of the None cipher. Care must always be used when enabling this as it will allow users to send data in the clear. However, it is important to note that authentication information remains encrypted even if this option is enabled. Default is no.
</dd>

<dt>NoneSwitch=[yes/no] client </dt> 
<dd>Switch the encryption cipher being used to the None cipher after authentication takes place. NoneEnabled must be enabled on both the client and server side of the connection. When the connection switches to the NONE cipher a warning is sent to STDERR. The connection attempt will fail with an error if a client requests a NoneSwitch from the server that does not explicitly have NoneEnabled set to yes. Note: The NONE cipher cannot be used in interactive (shell) sessions and it will fail silently.  Default is no.
</dd>

<dt>HPNDisabled=[yes/no] client/server </dt> 
<dd>In some situations, such as transfers on a local area network, the impact of the HPN code produces a net decrease in performance. In these cases it is helpful to disable the HPN functionality. Default is no.
</dd>

<dt>HPNBufferSize=[int]KB client/server </dt> 
<dd>This is the default buffer size the HPN functionality uses when interacting with nonHPN SSH installations. Conceptually this is similar to the TcpRcvBuf option as applied to the internal SSH flow control. This value can range from 1KB to 14MB (1-14336). Use of oversized or undersized buffers can cause performance problems depending on the length of the network path. The default size of this buffer is 2MB.
</dd>
</dl>