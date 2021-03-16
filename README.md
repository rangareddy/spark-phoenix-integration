# Spark  Phoenix connector

The following example demonstrate the **Spark Phoenix** integration

* [CDP Integration](#cdp)
* [HDP Integration](#hdp)

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
