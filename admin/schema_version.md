# Schema version
The data of Cortex is stored in an ElasticSearch index. The name of the index
is suffixed by the revision of the schema. When the schema of Cortex database
changes, a new one is created and the version is incremented. By default, index
base name is "cortex" but can be configured (`search.index` in
application.conf).

The following table show for each version of Cortex the default name of the
index:

| Cortex version | Index name  |
|----------------|-------------|
| 1.x            | N/A         |
| 2.0.0          | cortex_1    |
| 2.1.0          | cortex_2    |
| 3.0.0-RC1      | cortex_3    |
| 3.0.0-RC3      | cortex_4    |
