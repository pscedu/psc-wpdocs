## Section 10 - Data Processing
This section covers several topics including: Data Ingestion, HIVE / CODCC Validation, and Processing. You can also see the flow of data uploads and datasets through the system as well and more information about the various stages/statuses that occur during these processes.

Data uploads go through several statuses during the ingest process.

<details>
<summary>See a Data Ingest Flow Diagram</summary>
  https://raw.githubusercontent.com/pscedu/psc-wpdocs/dev/data-ingest/section10.md
#### Data Upload Flow Diagram 
![flow diagram](https://github.com/hubmapconsortium/data_submission_guide/blob/971828bc67887210ce9131bc56de1c898f7469e8/DataUploadFlow.png)

</details>
  
<details>
<summary>See details about Ingestion Statuses </summary>

### Ingestion Statuses 
These statuses display in the ingestion portal as a data set is processed.

**New** - Data upload registered, Globus upload directory created. Data provider has uploaded data. After HIVE (or CODCC) validation, status changes to <em>Submitted</em> when the data provider hits the submit button on the upload. <strong>Prerequisite:</strong> Local validation of data by provider prior to data upload.

**Processing** - The data upload is being processed and is not editable. A transient state (between other states) while automated processes act on the upload.

 **Valid** - Every data upload is reorganized into data sets. This is a semi-automated process. If the data upload is valid, data curation can kick off this process.

**Submitted** - Data upload submitted for validation and processing by the HIVE or CODCC. The data upload can be automatically or manually ingested. Status changes to _Processing_ when data curation presses the “Validate” button. 
 
**Reorganized** - Data curation hits the “Reorganize” button to kick off automated processing that generates the data sets. The status of the upload changes to _Reorganized_ when this completes.

**Invalid** - The data upload did not pass HIVE (or CODCC) validation or a failure occurred during processing. Someone from the HIVE (or CODCC) will contact the data submitter to address this status.

**Error** - An (unspecified) error occurred during HIVE or CODCC processing.

</details>
<br>
<b>Data Processing: Ingestion, HIVE / CODCC Validation, and Processing</b> <br>

_Prerequisites_

  - Upload all data files to the team’s Globus folder.
  - Each dataset has a corresponding directory and row in an associated metadata template file.
  - Each row in a template = one dataset
  - Multiple metadata template files can be included, but at least one is _required_. 

**NOTE:** Multiple sets of data can be uploaded and the same data set _could_ be uploaded more than once. Each data upload is assigned a UUID (universally unique identifier), but not a version number, until it has completed the entire data ingest, validation, and approval process and is published.

  1. **Email the <a href="mailto:help@hubmapconsortium.org">HuBMAP Helpdesk</a>** OR **<a href="mailto:help@sennetconsortium.org">SenNet Helpdesk</a>** once all the files associated with an upload have been uploaded. This lets them know that the upload is ready for ingestion (not an automated process).

 **IMPORTANT:** Include the _root path(s)_ of the specific data upload(s) in the email. 
 
  2. **The HIVE (or CODCC) extracts:** Each data upload, corresponding data, and registers them as individual datasets.
  3. **The HIVE (or CODCC) processes:** The data and metadata that you have uploaded for ingestion. If ingestion fails, or if additional information is needed, the data provider will be contacted using the ticketing system.

<blockquote>
<b>NOTE:</b> For Clinical assay data, the Pitt team will de-identify this data and submit the scrubbed data to the HIVE / CODCC. The provider of the data will need to review and approve the release of the de-identified data in the <a href="#publication">publish step</a>.
</blockquote>

  4. **Pipeline processing:** The HIVE / CODCC processes certain assays by standardized pipelines (where applicable).
     - **CODEX** - Cyclic immunofluorescence imaging: The pipeline uses Cytokit + SPRM
     - **ATAC-seq** - (Including sc-, sn-, and bulk variants): The pipeline uses SnapTools + SnapATAC
     - **RNA-seq** - (Including sc-,sn-,bulk): The pipeline uses Salmon + Scanpy + scVelo
     - **Other imaging data:** SPRM
    
Additional pipelines will be added over time, as needed. Pipelines are available for download use by others, including TMCs. 

**NOTE:** Generally, TMCs are not involved with pipelines, but may be contacted if an error occurs.

<details>
<summary>Details about Dataset Statuses </summary>

### Dataset Statuses 

These statuses apply to datasets created from a data upload. Once a data upload has been reorganized into datasets, each dataset passes through the system, ideally progressing from New to QA to Approved.

**New** - Data upload reorganized, broken into datasets, and status set to _New_. Data curation kicks off automated processes. If successful, status changes to _QA_. If these processes fail, the status changes to _Invalid_.

**QA** - Dataset is ready for pipeline processing OR for Provider approval (if no pipeline processing is needed). Status changes to _Processing_ when data curation presses the “Validate” button.

**Processing** - The data upload is being processed and is not editable. A transient state (between other states) while automated processes act on the upload.

**Approved** - Dataset approved by provider and any pipeline processing completed without errors. Dataset is ready for publication.

**Abandoned** - Dataset will not be further processed or published. There is no plan to advance this dataset. **NOTE:** At any point the data provider can elect to abandon the dataset.

**Invalid** - **ONLY PRIMARY DATASETS** - Information is missing or incorrect in the dataset. Someone from the HIVE (or CODCC) will contact the data provider to address this status. 

**Error** - **ONLY DERIVED DATASETS** - Error(s) occurred during pipeline processing. Someone from the HIVE (or CODCC) will contact the data provider to address this status.

</details>

<details>
<summary>See a Dataset Flow Diagram </summary>
  
#### Dataset Flow Diagram 

![flow diagram](https://github.com/hubmapconsortium/data_submission_guide/blob/971828bc67887210ce9131bc56de1c898f7469e8/DatasetFlow.png)

</details>
