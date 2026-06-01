# spark-hive-glue-libs

Pre-built JARs for using [AWS Glue Data Catalog](https://github.com/awslabs/aws-glue-data-catalog-client-for-apache-hive-metastore) with Apache Spark via [Apache Gravitino](https://github.com/apache/gravitino).

## Directory Structure

```
spark3/glue-<version>/   # Spark 3.3 / 3.4 / 3.5
```

## JARs Included

| JAR | Description |
|---|---|
| `aws-glue-datacatalog-spark-client-3.4.0.jar` | AWS Glue Data Catalog client for Spark (shades internal shims) |
| `hive-exec-2.3.10.jar` | Patched Hive exec (HIVE-12679, adds HiveMetaStoreClientFactory) |
| `hive-metastore-2.3.10.jar` | Patched Hive metastore |
| `hive-common-2.3.10.jar` | Patched Hive common |
| `hive-serde-2.3.10.jar` | Patched Hive serde |
| `hive-shims-2.3.10.jar` | Patched Hive shims |
| `aws-java-sdk-glue-1.12.31.jar` | AWS Glue SDK (must be in isolated classloader) |
| `aws-java-sdk-core-1.12.31.jar` | AWS SDK core |
| `jmespath-java-1.12.31.jar` | JMESPath for AWS SDK |

**Why patched Hive?** Spark's bundled `hive-exec-2.3.9` is missing the `HiveMetaStoreClientFactory` interface required by the Glue client (added via HIVE-12679 patch).

**Why AWS SDK in this dir?** Spark's `IsolatedClientLoader` loads these JARs in an isolated classloader. `com.amazonaws.*` must be resolved inside that classloader alongside the patched Hive — not from Spark's parent classloader — to avoid version conflicts with `hadoop-aws`.

## Usage with Gravitino

```bash
# Download JARs for Spark 3.3 / 3.4 / 3.5, Glue client 3.4.0
BASE=https://raw.githubusercontent.com/datastrato/spark-hive-glue-libs/main/spark3/glue-3.4.0
mkdir -p /opt/glue-hive-jars
for jar in \
  aws-glue-datacatalog-spark-client-3.4.0.jar \
  hive-exec-2.3.10.jar \
  hive-metastore-2.3.10.jar \
  hive-common-2.3.10.jar \
  hive-serde-2.3.10.jar \
  hive-shims-2.3.10.jar \
  aws-java-sdk-glue-1.12.31.jar \
  aws-java-sdk-core-1.12.31.jar \
  jmespath-java-1.12.31.jar; do
  wget "$BASE/$jar" -P /opt/glue-hive-jars/
done

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
