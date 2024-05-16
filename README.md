## Catch all of random Cloud Pak for Data technical items
> [!NOTE]
> This below information on pods is from **3.5/2021-11 CPD 3.5 Draft Support Manual.pdf**  
> This ONLY covers **Core Services** / **Knowledge Catalog** / **Some WSL**
> (*) AD: Automated discovery, QS: Quick scan, IAS: Information assets, MI: Metadata import, DS: DataStage, UG: Unified governance, WKC: Watson Knowledge Catalog, ODF: Open discovery framework, PRF: Profiling, DPS: Data protection service, GS: Global search, WF: workflow  

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

   Pod name           |Service|Scope*|Description*|Impact of restart*
:---------------------| :---: | --- | :--- | :--- 
iis-services          |WKC    |AD, DQ, IAS, MI |Legacy IIS services tier. Runs IAS, AD, QS, DQ, MI backend and UI components.                   | All AD, QS, and DQ jobs are cancelled. The IAS, MI, and DQ UIs are restarted. Takes > 10 min to restart.
iis-xmetarepo         |WKC    |AD, DQ, IAS, MI |XMeta metadata repository storing IAS, MI, and AD metadata (all legacy IGC metadata).           | Requires subsequent restart of iis-services pod.
finley-ml             |WKC    |AD, QS          |Implements term assignment machine learning capabilities.                                       |Running AD, QS, and DQ jobs may fail.
is-en-conductor       |WKC    |AD, DQ, DS      |Legacy IIS engine tier. Runs DataStage jobs for AD, and DQ, as well as ODF.                     |Running AD and DQ jobs will fail.
is-enginecompute      |WKC    |AD, DS          |Used for parallel execution of DataStage jobs for AD and DQ (if configured).                    | Running AD and DQ jobs may fail.
gov-catalog-search    |WKC    |UG              |Indexer which updates SOLR index upon receiving Kafka events                                    | tbd. 
gov-enterprise-search |WKC    |UG              |UI serving the GraphExplorer visualization of enterprise Search graph data
odf-fastanalyzer      |WKC    |QS              |Backend for quick scan. Implements a local Hadoop cluster for running data discovery algorithms.| Running QS jobs will fail.
ia-analysis           |WKC    |AD, QS, DQ      |Backend for data quality projects. Also used for publishing QS results.                         | Running AD and DQ jobs may fail. DQ UI does not work.
wkc-gov-ui            |WKC    |WKC             |UI for WKC Glossary and workflow. Used for starting AD and QS jobs?                             | Affects UIs until pod is back.
gov-admin-ui          |WKC    |UG              |Management UI of legacy "IGC New". Used for customizing asset display page in IAS. Uses gov-app-config-service as backend. | Affects UIs until pod is back.
gov-app-config-service|WKC    |UG              |Backend of gov-admin-ui
gov-quality-ui        |WKC    |AD, DQ          |Data quality UI. Reads and writes to legacy Information Analyzer APIs.                          | Affects UIs until pod is back.
audit-trail-service   |WKC    |AD, QS          |Used for storing AD, and QS job history and details tbd.
solr                  |WKC    |AD, DQ, IAS     |Search index and data cache for IAS and DQ UIs. Staging area for QS results.                    | Affects DQ and IAS UIs until pod is back.
kafka                 |WKC    |UG              |Runs Apache Kafka. Used for AD/QS and for OMRS metadata sync.                                   | Running AD, SQ, and DQ jobs may fail.
zookeeper             |WKC    |UG              |Maintaining configuration information for Kafka and Solr. Used by AD/QS.                        | Requires subsequent restart of kafka pod.
audit-trail-service   |WKC    |AD, QS          |Stores AD and QS job history                                                                    | Do not restart while discovery runs
cassandra-0           |WKC    |IAS             |Repository for relationship graph data                                                          | Minor impact
portal-catalog        |WKC    |general         |Running the WKC catalog UI
portal-commonapi      |WKC    |general         |
portal-dashboards     |WKC    |general         |
catalog-api           |WKC    |general         |Back-end catalog and asset service, used to store metadata for catalog, project, etc.           | Minor impact
wdp-couchdb           |WKC    |general         |Repository for storing asset metadata for projects, catalogs, etc.                              | Minor impact
wdp-db2               |WKC    |general         |Repository for DPS, wkc-glossary, wkc-workflow, lineage                                         | Restarting will take 5-15 minutes to start the pod, while starting some of the services will be down
dataconn-engine-opdiscovery |WKC|PRF, DPS
dataconn-engine-service     |WKC|PRF, DPS      |Manages dataconn-engine-spark-cluster instances
dataconn-engine-sparkcluster|WKC|PRF, DPS      |Runs data flows
dc-main               |WKC    |general         |Runs catalog management UIs                                                                     | Minor impact
elasticsearch-master  |WKC    |GS              |Runs elastic search engine for global search
gov-ui-commons        |WKC    |UG              |Common bundle for UG UIs with resources such as fonts, icons etc., no functionality as such
gov-user-prefsservice |WKC    |UG              |Microservice storing user specific configuration settings in keyvalue form, used by the UG UIs to save some personalized settings
igc-ui-react          |WKC    |IAS             |UI serving Information Assets area
portal-job-manager    |WKC
portal-main           |WKC
portal-notifications  |WKC
rabbitmq-ha           |WKC    |general         |Used for internal messaging, a bit like Kafka in the UG stack                                  | No impact if restart one pod at a time since this service is HA
redis-ha-haproxy      |WKC    |general         |Proxy service to redis-ha master
redis-ha-server       |WKC    |general         |Cache service for WKC UI and other backend microservices                                       |No impact if restart one pod at a time since this service is HA
shop4info-event-consumer-0 |WKC| UG            |Enterprise search, receives events from Kafka
shop4info-mappers-service  |WKC| UG            |Enterprise search related
shop4info-rest-0           |WKC| UG            |Enterprise search, provides API interface
shop4info-scheduler   |WKC    |UG              |Enterprise search related
shop4info-type-registry-service |WKC|UG        |Enterprise search related 
spawner-api           |
wdp-connect-connection|WKC      |general       |Provides access to the connection and datasource assets in the CAMS repository.                |None
wdp-connect-connector |WKC      |general       |Helper for wdp-connect-connection                                                              |None
couchdb               |CP4D     |lite          |Used to users and other metadata, not needed starting from V3.5
wdp-lineage           |WKC      |general       |Backend to serve WKC Activity lineage seen in the WKC Catalog Asset view on Lineage tab
wdp-policy-service    |WKC      |DPS           |Backend to do policy enforcement for data protection rules
wdp-profiling         |WKC      |general       |Profiling tab in WKC asset browser
wdp-profiling-messaging|WKC     |general       |Profiling tab in WKC asset browser
wdp-profiling-ui      |WKC      |general       |Profiling tab in WKC asset browser
wdp-shaper            |WKC      |general       |Data refinery
wkc-glossary-service  |WKC      |general       |Backend for WKC glossary interacts with OMAG via Kafka and uses RabbitMQ for DPS. Connects to Db2 (BGDB ), XMETA (ILGDB), uses Redis
wkc-search            |WKC      |GS            |Global Search
wkc-workflow-service  |WKC      |WF            |Backend for wkc governance artifacts workflow capabilities
jobs-api              |
jobs-ui               |
metadata-discovery    |
