## Configuration Options

These options can be used on the command line in the typical '-o Option ' format or embedded in the ssh_config or sshd_confing files.

 
<dl>
<dt>  **TcpRcvBuf=[int]KB client** </dt> 
<dd>Set the TCP socket receive buffer to int Kilobytes. It can be set up to the maximum socket size allowed by the system. This is useful in situations where the TCP receive window is set low but the maximum buffer size is set higher (as is typical). This works on a per TCP connection basis. You can also use this to artifically limit the transfer rate of the connection. In these cases the throughput will be no more than n/RTT. The minimum buffer size is 1KB . Default is the current system wide TCP receive buffer size.
</dd>

<dt>  **TcpRcvBufPoll=[yes/no] client/server** </dt> 
<dd>Enable or disable the polling of the TCP receive buffer through the life of the connection. You would want to make sure that this option is enabled for systems making use of autotuning kernels (linux 2.4.24, 2.6, MS Vista). Default is no.
</dd>

<dt>  **NoneEnabled=[yes/no] client/server** </dt> 
<dd>Enable or disable the use of the None cipher. Care must always be used when enabling this as it will allow users to send data in the clear. However, it is important to note that authentication information remains encrypted even if this option is enabled. Default is no.
</dd>

<dt>  **NoneSwitch=[yes/no] client** </dt> 
<dd>Switch the encryption cipher being used to the None cipher after authentication takes place. NoneEnabled must be enabled on both the client and server side of the connection. When the connection switches to the NONE cipher a warning is sent to STDERR. The connection attempt will fail with an error if a client requests a NoneSwitch from the server that does not explicitl y have NoneEnabled set to yes. Note: The NONE cipher cannot be used in interactive (shell) sessions and it will fail silently.  Default is no.
</dd>

<dt>  **HPNDisabled=[yes/no] client/server** </dt> 
<dd>In some situations, such as transfers on a local area network, the impact of the HPN code produces a net decrease in performance. In these cases it is helpful to disable the HPN functionality. Default is no.
</dd>

<dt>  **HPNBufferSize=[int]KB client/server** </dt> 
<dd>This is the default buffer size the HPN functionality uses when interacting with nonHPN SSH installations. Conceptually this is similar to the TcpRcvBuf option as applied to the internal SSH flow control. This value can range from 1KB to 14MB (1-14336). Use of oversized or undersized buffers can cause performance problems depending on the length of the network path. The default size of this buffer is 2MB.
</dd>
</dl>