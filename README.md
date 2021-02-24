# Spark  Phoenix connector

## Spark Phoenix Connector example in CDP

### Launch Phoenix Shell
```sh
python /opt/cloudera/parcels/CDH/lib/phoenix/bin/sqlline.py
```

### Creating Phoenix table
```sql
CREATE TABLE IF NOT EXISTS EMPLOYEE (
  ID BIGINT NOT NULL, 
  NAME VARCHAR, 
  AGE INTEGER, 
  SALARY FLOAT
  CONSTRAINT emp_pk PRIMARY KEY (ID)
);
```

### Inserting data to Phoenix table
```sql
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (1, 'Ranga', 32, 10000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (2, 'Nishanth', 2, 30000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (3, 'Raja', 52, 60000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (4, 'Yashu', 10, 8000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (5, 'Manoj', 16, 15000);
SELECT * FROM EMPLOYEE;
```

### Launch Spark-shell
```sh
sudo -u spark spark-shell \
  --master yarn \
  --conf "spark.executor.extraClassPath=/opt/cloudera/parcels/CDH/lib/phoenix/lib/phoenix-spark-5.0.0.7.1.4.0-203.jar:/opt/cloudera/parcels/CDH/lib/phoenix/phoenix-client.jar:/opt/cloudera/parcels/CDH/lib/hbase/conf/hbase-site.xml" \
  --conf "spark.driver.extraClassPath=/opt/cloudera/parcels/CDH/lib/phoenix/lib/phoenix-spark-5.0.0.7.1.4.0-203.jar:/opt/cloudera/parcels/CDH/lib/phoenix/phoenix-client.jar:/opt/cloudera/parcels/CDH/lib/hbase/conf/hbase-site.xml"
```

### Spark code
```scala
import org.apache.spark.SparkContext
import org.apache.spark.sql.SQLContext
import org.apache.phoenix.spark._

val sc = new SparkContext("local", "Spark Phoenix Example")
val sqlContext = new SQLContext(sc)

val df = sqlContext.load(
  "org.apache.phoenix.spark",
  Map("table" -> "EMPLOYEE", "zkUrl" -> "c4543-node4.coelab.cloudera.com:2181")
)

df.show(4)

df.filter(df("NAME") === "Ranga").show()
```
