## System Specifications
Neocortex features two Cerebras CS-2 systems and an HPE Superdome Flex HPC server robustly provisioned to drive the CS-2 systems simultaneously at maximum speed and support the complementary requirements of AI and HPDA workflows. Neocortex is federated with Bridges-2 to yield great benefits including:

* Access to the Bridges-2 filesystem for management of persistent data
* General-purpose computing for complementary data wrangling and preprocessing
* **High-bandwidth connectivity to other sites, campus, labs, and clouds ??**

The configuration of each specialized system is described below.
### CS-2
Each CS-2 features a Cerebras WSE2 (Wafer Scale Engine2), the largest chip ever built.
<table>
  <tbody>
    <tr>
      <td style="vertical-align:top;"><strong>AI Processor</strong></td>
      <td>Cerebras Wafer Scale Engine 2 (WSE2)
<ul><li>850,000 Sparse Linear Algebra Compute (SLAC) Cores</li>
<li>2.6 trillion transistors</li>
<li>46,225 mm2 40 GB SRAM on-chip memory</li>
<li>20 PB/s memory bandwidth</li>
<li>220 Pb/s interconnect bandwidth</li></ul></td>
    </tr>
    <tr>
    <td><strong>System I/O</strong></td>
    <td>1.2 Tb/s (12 × 100 GbE ports)</td>
    </tr>
  </tbody>
</table>


### HPE Superdome Flex
<table>
  <tbody>
    <tr>
      <td  style="vertical-align:top;"><strong>Processors</strong></td>
      <td>32 x Intel Xeon Platinum 8280L, 28 cores, 56 threads each, 2.70-4.0 GHz, 38.5 MB cache  <br />
        <a href="https://ark.intel.com/content/www/us/en/ark/products/192472/intel-xeon-platinum-8280l-processor-38-5m-cache-2-70-ghz.html" target="_blank">more info</a>
      </td>
    </tr>
    <tr>
    <td><strong>Memory</strong></td>
    <td>24 TiB RAM, aggregate memory bandwidth of 4.5 TB/s</td>
    </tr>
    <tr>
    <td  style="vertical-align:top;"><strong>Local Disk</strong></td>
    <td>32 x 6.4 TB NVMe SSDs
<ul><li>204.6 TB aggregate</li>
<li>150 GB/s read bandwidth</li></ul></td>
</tr>
<tr>
  <td  style="vertical-align:top;"><strong>Network to CS-2 systems</strong></td>
  <td>24 x 100 GbE interfaces
<ul><li>1.2 Tb/s (150 GB/s) to each Cerebras CS-2 system</li>
<li>2.4 Tb/s aggregate</li></ul></td>
</tr>
<tr>
  <td  style="vertical-align:top;"><strong>Interconnect to Bridges-2</strong></td>
  <td>16 Mellanox HDR-100 InfiniBand adapters
<ul><li>1.6 Tb/s aggregate</li></ul></td>
</tr>
<tr>
  <td><strong>OS</strong></td>
  <td>Red Hat Enterprise Linux</td>
</tr>
  </tbody>
</table>
