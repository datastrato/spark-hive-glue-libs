# spark-hive-glue-libs

Pre-built JARs for using [AWS Glue Data Catalog](https://github.com/awslabs/aws-glue-data-catalog-client-for-apache-hive-metastore) with Apache Spark via [Apache Gravitino](https://github.com/apache/gravitino).

## Directory Structure

```
spark3/glue-<version>/   # Spark 3.3 / 3.4 / 3.5
```

## JARs Included

| JAR | Description |
|---|---|
| `aws-glue-datacatalog-spark-client-<version>.jar` | AWS Glue Data Catalog client for Spark |
| `hive-exec-2.3.10.jar` | Patched Hive exec (HIVE-12679, adds HiveMetaStoreClientFactory) |
| `hive-metastore-2.3.10.jar` | Patched Hive metastore |

The patched Hive JARs replace Spark's bundled `hive-exec-2.3.9` which is missing the `HiveMetaStoreClientFactory` interface required by the Glue client.

## Usage with Gravitino

```bash
# Download JARs (Spark 3.3 / 3.4 / 3.5, Glue client 3.4.0)
BASE=https://raw.githubusercontent.com/datastrato/spark-hive-glue-libs/main/spark3/glue-3.4.0
mkdir -p /opt/glue-hive-jars
wget $BASE/aws-glue-datacatalog-spark-client-3.4.0.jar -P /opt/glue-hive-jars/
wget $BASE/hive-exec-2.3.10.jar -P /opt/glue-hive-jars/
wget $BASE/hive-metastore-2.3.10.jar -P /opt/glue-hive-jars/

# Create Gravitino catalog
gravitino catalog create \
  --provider glue \
  --property aws-region=us-east-1 \
  --property glue.hive-jars-dir=/opt/glue-hive-jars
```

## Version Matrix

| Directory | Glue Client | Spark | Hive (patched) |
|---|---|---|---|
| `spark3/glue-3.4.0` | 3.4.0 | 3.3 / 3.4 / 3.5 | 2.3.10 |
