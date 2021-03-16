# Spark  Phoenix connector

The following example demonstrate the **Spark Phoenix** integration

* [CDP Integration](#cdp)
* [HDP Integration](#hdp)
* [HDP Kerberized Integration](#hdp-kerberized)

# CDP

### Step1: Launch Phoenix Shell
```sh
python /opt/cloudera/parcels/CDH/lib/phoenix/bin/sqlline.py
```

### Step2: Creating Phoenix table
```sql
CREATE TABLE IF NOT EXISTS EMPLOYEE (
  ID BIGINT NOT NULL, 
  NAME VARCHAR, 
  AGE INTEGER, 
  SALARY FLOAT
  CONSTRAINT emp_pk PRIMARY KEY (ID)
);
```

### Step3: Inserting data to Phoenix table
```sql
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (1, 'Ranga', 32, 10000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (2, 'Nishanth', 2, 30000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (3, 'Raja', 52, 60000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (4, 'Yashu', 10, 8000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (5, 'Manoj', 16, 15000);
SELECT * FROM EMPLOYEE;
```

### Step4: Check the data in HBase
```sh
```

### Step5: Launch Spark-shell
```sh
sudo -u spark spark-shell \
  --master yarn \
  --conf "spark.executor.extraClassPath=/opt/cloudera/parcels/CDH/lib/phoenix/lib/phoenix-spark-5.0.0.7.1.4.0-203.jar:/opt/cloudera/parcels/CDH/lib/phoenix/phoenix-client.jar:/opt/cloudera/parcels/CDH/lib/hbase/conf/hbase-site.xml" \
  --conf "spark.driver.extraClassPath=/opt/cloudera/parcels/CDH/lib/phoenix/lib/phoenix-spark-5.0.0.7.1.4.0-203.jar:/opt/cloudera/parcels/CDH/lib/phoenix/phoenix-client.jar:/opt/cloudera/parcels/CDH/lib/hbase/conf/hbase-site.xml"
```

### Step6: Run the Spark code in spark-shell
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
_________________

# HDP


```sh
export JAVA_HOME=/usr/jdk64/jdk1.8.0_112/
```

### Step1: Launch Phoenix Shell
```sh
python /usr/hdp/current/phoenix-client/bin/sqlline.py
```

### Step2: Creating Phoenix table
```sql
CREATE TABLE IF NOT EXISTS EMPLOYEE (
  ID BIGINT NOT NULL, 
  NAME VARCHAR, 
  AGE INTEGER, 
  SALARY FLOAT
  CONSTRAINT emp_pk PRIMARY KEY (ID)
);
```

### Step3: Inserting data to Phoenix table
```sql
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (1, 'Ranga', 32, 10000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (2, 'Nishanth', 2, 30000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (3, 'Raja', 52, 60000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (4, 'Yashu', 10, 8000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (5, 'Manoj', 16, 15000);
SELECT * FROM EMPLOYEE;
```

### Step4: Check the data in HBase
```sh
```

### Step5: Launch Spark-shell
```sh
sudo -u spark spark-shell \
  --master yarn \
  --conf "spark.executor.extraClassPath=/usr/hdp/current/phoenix-client/lib/phoenix-spark-5.0.0.3.1.5.79-2.jar:/usr/hdp/current/phoenix-client/phoenix-client.jar:/usr/hdp/current/hbase-client/conf/hbase-site.xml" \
  --conf "spark.driver.extraClassPath=/usr/hdp/current/phoenix-client/lib/phoenix-spark-5.0.0.3.1.5.79-2.jar:/usr/hdp/current/phoenix-client/phoenix-client.jar:/usr/hdp/current/hbase-client/conf/hbase-site.xml"
```

### Step6: Run the Spark code in spark-shell
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
_________________

# HDP Kerberized

### Step1: Kinit with hbase user
```sh
kinit -kt /etc/security/keytabs/hbase.headless.keytab hbase-c3543@COELAB.CLOUDERA.COM
```
### Step2: Launch the Phoenix Shell using sqlline.py
```sh
python /usr/hdp/current/phoenix-client/bin/sqlline.py
```

### Step3: Create a Phoenix Employee table
```sql
CREATE TABLE IF NOT EXISTS EMPLOYEE (
  ID BIGINT NOT NULL, 
  NAME VARCHAR, 
  AGE INTEGER, 
  SALARY FLOAT
  CONSTRAINT emp_pk PRIMARY KEY (ID)
);
```

### Step4: List the Phoenix tables
```sql
> !tables
+------------+--------------+-------------+---------------+----------+------------+----------------------------+-----------------+--------------+-----------------+---------------+-----+
| TABLE_CAT  | TABLE_SCHEM  | TABLE_NAME  |  TABLE_TYPE   | REMARKS  | TYPE_NAME  | SELF_REFERENCING_COL_NAME  | REF_GENERATION  | INDEX_STATE  | IMMUTABLE_ROWS  | SALT_BUCKETS  | MUL |
+------------+--------------+-------------+---------------+----------+------------+----------------------------+-----------------+--------------+-----------------+---------------+-----+
|            | SYSTEM       | CATALOG     | SYSTEM TABLE  |          |            |                            |                 |              | false           | null          | fal |
|            | SYSTEM       | FUNCTION    | SYSTEM TABLE  |          |            |                            |                 |              | false           | null          | fal |
|            | SYSTEM       | LOG         | SYSTEM TABLE  |          |            |                            |                 |              | true            | 32            | fal |
|            | SYSTEM       | SEQUENCE    | SYSTEM TABLE  |          |            |                            |                 |              | false           | null          | fal |
|            | SYSTEM       | STATS       | SYSTEM TABLE  |          |            |                            |                 |              | false           | null          | fal |
|            |              | EMPLOYEE    | TABLE         |          |            |                            |                 |              | false           | null          | fal |
+------------+--------------+-------------+---------------+----------+------------+----------------------------+-----------------+--------------+-----------------+---------------+-----+
```

### Step5: Insert the data to Employee table
```sql
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (1, 'Ranga', 32, 10000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (2, 'Nishanth', 2, 30000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (3, 'Raja', 52, 60000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (4, 'Yashu', 10, 8000);
UPSERT INTO EMPLOYEE (ID, NAME, AGE, SALARY) VALUES (5, 'Manoj', 16, 15000);
```

### Step6: Select the Employee data
```sql
> SELECT * FROM EMPLOYEE;
+-----+-----------+------+----------+
| ID  |   NAME    | AGE  |  SALARY  |
+-----+-----------+------+----------+
| 1   | Ranga     | 32   | 10000.0  |
| 2   | Nishanth  | 2    | 30000.0  |
| 3   | Raja      | 52   | 60000.0  |
| 4   | Yashu     | 10   | 8000.0   |
| 5   | Manoj     | 16   | 15000.0  |
+-----+-----------+------+----------+
5 rows selected (0.07 seconds)

> !quit
```

### Step7: Login to hbase shell and check the data in HBase
```sh
hbase shell

> scan 'EMPLOYEE'
ROW                                             COLUMN+CELL
 \x80\x00\x00\x00\x00\x00\x00\x01               column=0:\x00\x00\x00\x00, timestamp=1615911856369, value=x
 \x80\x00\x00\x00\x00\x00\x00\x01               column=0:\x80\x0B, timestamp=1615911856369, value=Ranga
 \x80\x00\x00\x00\x00\x00\x00\x01               column=0:\x80\x0C, timestamp=1615911856369, value=\x80\x00\x00
 \x80\x00\x00\x00\x00\x00\x00\x01               column=0:\x80\x0D, timestamp=1615911856369, value=\xC6\x1C@\x01
 \x80\x00\x00\x00\x00\x00\x00\x02               column=0:\x00\x00\x00\x00, timestamp=1615911856418, value=x
 \x80\x00\x00\x00\x00\x00\x00\x02               column=0:\x80\x0B, timestamp=1615911856418, value=Nishanth
 \x80\x00\x00\x00\x00\x00\x00\x02               column=0:\x80\x0C, timestamp=1615911856418, value=\x80\x00\x00\x02
 \x80\x00\x00\x00\x00\x00\x00\x02               column=0:\x80\x0D, timestamp=1615911856418, value=\xC6\xEA`\x01
 \x80\x00\x00\x00\x00\x00\x00\x03               column=0:\x00\x00\x00\x00, timestamp=1615911856448, value=x
 \x80\x00\x00\x00\x00\x00\x00\x03               column=0:\x80\x0B, timestamp=1615911856448, value=Raja
 \x80\x00\x00\x00\x00\x00\x00\x03               column=0:\x80\x0C, timestamp=1615911856448, value=\x80\x00\x004
 \x80\x00\x00\x00\x00\x00\x00\x03               column=0:\x80\x0D, timestamp=1615911856448, value=\xC7j`\x01
 \x80\x00\x00\x00\x00\x00\x00\x04               column=0:\x00\x00\x00\x00, timestamp=1615911856478, value=x
 \x80\x00\x00\x00\x00\x00\x00\x04               column=0:\x80\x0B, timestamp=1615911856478, value=Yashu
 \x80\x00\x00\x00\x00\x00\x00\x04               column=0:\x80\x0C, timestamp=1615911856478, value=\x80\x00\x00\x0A
 \x80\x00\x00\x00\x00\x00\x00\x04               column=0:\x80\x0D, timestamp=1615911856478, value=\xC5\xFA\x00\x01
 \x80\x00\x00\x00\x00\x00\x00\x05               column=0:\x00\x00\x00\x00, timestamp=1615911856507, value=x
 \x80\x00\x00\x00\x00\x00\x00\x05               column=0:\x80\x0B, timestamp=1615911856507, value=Manoj
 \x80\x00\x00\x00\x00\x00\x00\x05               column=0:\x80\x0C, timestamp=1615911856507, value=\x80\x00\x00\x10
 \x80\x00\x00\x00\x00\x00\x00\x05               column=0:\x80\x0D, timestamp=1615911856507, value=\xC6j`\x01
 
 > exit
```

### Step8: Launch the spark-shell
```sh
spark-shell \
  --master yarn \
  --conf "spark.executor.extraClassPath=/usr/hdp/current/phoenix-client/lib/phoenix-spark*.jar:/usr/hdp/current/phoenix-client/phoenix-client.jar:/usr/hdp/current/hbase-client/conf/hbase-site.xml" \
  --conf "spark.driver.extraClassPath=/usr/hdp/current/phoenix-client/lib/phoenix-spark*.jar:/usr/hdp/current/phoenix-client/phoenix-client.jar:/usr/hdp/current/hbase-client/conf/hbase-site.xml"
```

### Step9: Run the Spark code in spark-shell
```scala
import org.apache.spark.SparkContext
import org.apache.spark.sql.SQLContext
import org.apache.phoenix.spark._

val sqlContext = new SQLContext(sc)

val df = sqlContext.load(
  "org.apache.phoenix.spark",
  Map("table" -> "EMPLOYEE", "zkUrl" -> "c3543-node2.coelab.cloudera.com:2181")
)

df.show(4)

df.filter(df("NAME") === "Ranga").show()
```
_________________


> python /usr/hdp/current/phoenix-client/bin/sqlline.py c3543-node2.coelab.cloudera.com:2181:/hbase-unsecure
