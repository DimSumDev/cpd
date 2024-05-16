## Catch all of random Cloud Pak for Data technical ite>ms
> [!NOTE]
> This below information on pods is from **3.5/2021-11 CPD 3.5 Draft Support Manual.pdf**  
> This ONLY covers **Core Services** / **Knowledge Catalog** / **Some WSL**  

### Pods for data persistence 
The following table provides a rough overview on how the different types of metadata are persisted by CP4D.  

| Type of data | Database | Pod |
| --- | --- | --- |
| Assets | CouchDB / Cloudant | wdp-couchdb |
| Governance artifact | DB2 | wdp-db2 |
| Global search index | ElasticSearch | elasticsearch-master |
| IAS, MI, AD, DQ data | DB2 | iis-xmetarepo |
| IAS search index, QS results | SOLR | solr-0 |
| Platform metadata | Cockroach DB | zen-metastoredb |

### Pod reference table
The following table provides reference information on the pods making up CP4D and its services.  

Pod name &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;|Service|Scope*|Description*|Impact of restart*
:--------------- | :---: | --- | :--- | :--- 
iis-services     |WKC    |AD, DQ, IAS, MI|Legacy IIS services tier. Runs IAS, AD, QS, DQ, MI backend and UI components. | All AD, QS, and DQ jobs are cancelled. The IAS, MI, and DQ UIs are restarted. Takes > 10 min to restart.
iis-xmetarepo    |WKC    |AD, DQ, IAS, MI | XMeta metadata repository storing IAS, MI, and AD metadata (all legacy IGC metadata). | Requires subsequent restart of iis-services pod.
finley-ml        |WKC    |AD, QS|Implements term assignment machine learning capabilities.|Running AD, QS, and DQ jobs may fail.
is-en-conductor  |WKC    |AD, DQ, DS|Legacy IIS engine tier. Runs DataStage jobs for AD, and DQ, as well as ODF.|Running AD and DQ jobs will fail.
