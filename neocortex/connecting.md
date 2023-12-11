## Connecting to the System

There are two systems to connect to, interconnected by the network file systems, $HOME and $PROJECT (read more in the [Data Management section](https://www.psc.edu/resources/neocortex/docs/data-management).

1. The Neocortex system (contains the Cerebras CS machines, driven by the HPE Superdome Flex system)
2. The Bridges-2 system (provides access to the GPU partition on Bridges-2)

There are also two different ways to connect to both systems:

1. Via a web interface using the Open OnDemand platform
2. Via a command line interface over ssh

Please read below for specific instructions and connect to each system as needed. The resource usage will be tracked for each project.

### Connecting to Neocortex (preferred)

<blockquote><strong>Info</strong>
<p>If you forget your Neocortex authentication credentials, please visit the [PSC password change utility webpage](https://apr.psc.edu/).</p>
</blockquote>

Neocortex users have been granted Neocortex resources for their projects. Please refer to the [Allocations section](https://www.psc.edu/resources/neocortex/docs/allocations) for more information. You can compile code, validate code, and run training jobs on the CS machines using this system.

#### How to connect to Neocortex over ssh

Connect to `neocortex.psc.edu` using your PSC credentials, where *psc_username* is your PSC username.

<pre> ssh <i>psc_username</i>@neocortex.psc.edu</pre>

#### How to connect to Neocortex using Open OnDemand

Navigate to the [Neocortex Open OnDemand webpage](https://ondemand.neocortex.psc.edu/) at https://ondemand.neocortex.psc.edu/ and login using your PSC credentials.

### Connecting to Bridges-2

<blockquote>
  <strong>Info</strong>
<p>If you forget your Neocortex authentication credentials, please visit the [PSC password change utility webpage](https://apr.psc.edu/).</p>
</blockquote>

Neocortex users are granted access to Bridges-2 resources for their projects. Please refer to the [Allocations section](https://www.psc.edu/resources/neocortex/docs/allocations) for more information.

You can compile and validate code using this resource, and you also have access to GPUs on this system. However, **you cannot run training jobs on the CS machines from Bridges-2, as those are only available on Neocortex**.

#### How to connect to Bridges-2 over ssh

Connect to `bridges-2.ps.edu` using your PSC credentials, where *psc_username* is your PSC username. 
<pre> ssh <i>psc_username</i>@bridges2.psc.edu</pre>

If you need to reset your password, please visit the [PSC Password Change Utility](https://apr.psc.edu) webpage.

#### How to connect to Bridges-2 using Open OnDemand

Navigate to the [Bridges-2 Open OnDemand webpage](https://ondemand.bridges2.psc.edu/) at https://ondemand.bridges2.psc.edu/ and login using your PSC credentials.
