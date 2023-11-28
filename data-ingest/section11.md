
## Section 11 - Test

Data uploads go through several statuses during the ingest process.

**Everything below this should be orange.**

  
<div style="background-color:orange;">
  
### Ingestion Statuses 
These statuses display in the ingestion portal as a data set is processed.

| Status | Explanation / Comments |
|:---------------------|:----------------------------------------------------|
| **New** | Data upload registered; Globus upload directory created.  Data provider has uploaded data. After HIVE (or CODCC) validation, status changes to <em>Submitted </em>when the data provider hits the submit button on the upload. <strong>Prerequisite: </strong>Local validation of data by provider prior to data upload.|
| **Processing** |The data upload is being processed and is not editable. A transient state (between other states) while automated processes act on the upload.|
| **Valid** | Every data upload is reorganized into data sets. This is a semi-automated process. If the data upload is valid, data curation can kick off this process.|
| **Submitted** | Data upload submitted for validation and processing by the HIVE or CODCC. The data upload can be automatically or manually ingested. Status changes to _Processing_ when data curation presses the “Validate” button. |
| **Reorganized** | Data curation hits the “Reorganize” button to kick off automated processing that generates the data sets. The status of the upload changes to _Reorganized_ when this completes.|
| **Invalid** | The data upload did not pass HIVE (or CODCC) validation or a failure occurred during processing. Someone from the HIVE (or CODCC) will contact the data submitter to address this status. |
| **Error** | An (unspecified) error occurred during HIVE or CODCC processing. |
</details>


<p><b>NOTE:</b> Multiple sets of data can be uploaded and the same data set <i>could be</i> uploaded more than once. <span style="background-color:green;">Each data upload</span> is assigned a UUID (universally unique identifier), but not a version number, until it has completed the entire data ingest, validation, and approval process and is published.</p>

 
Additional pipelines will be added over time, as needed. Pipelines are available for download use by others, including TMCs. <br> **NOTE:** Generally, TMCs are not involved with pipelines, but may be contacted if an error occurs.


</div>
