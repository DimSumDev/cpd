## Catch all of random Cloud Pak for Data technical ite>ms
> [!NOTE]
> This below information on pods is from 3.5/2021-11 CPD 3.5 Draft Support Manual.pdf
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


