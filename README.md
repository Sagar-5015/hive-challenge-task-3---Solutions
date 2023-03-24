# hive-challenge-task-3---Solutions

Scenario Based questions:

Q .Will the reducer work or not if you use “Limit 1” in any HiveQL query? 
Ans - No, reducer will not work. Reducer works only for arregation, joins and analytical queries execution and insert overwrite operations.

Q. Suppose I have installed Apache Hive on top of my Hadoop cluster using default metastore configuration. Then, what will happen if we have multiple clients trying to access Hive at the same time? 
Ans - The default metastore for hive is derby.db where all metadata gets stored and it has few limitations. The main limitation of this default metastore is only single person(or client) can work at a single time, this metastore can't handle the concurrent load and not suitable to provide access for multiple clients.

Q. Suppose, I create a table that contains details of all the transactions done by the customers: CREATE TABLE transaction_details (cust_id INT, amount FLOAT, month STRING, country STRING) ROW FORMAT DELIMITED FIELDS TERMINATED BY ‘,’ ;
Now, after inserting 50,000 records in this table, I want to know the total revenue generated for each month. But, Hive is taking too much time in processing this query. How will you solve this problem and list the steps that I will be taking in order to do so?

Ans :- Dynamic partition on month will be effective method to calculate total monthly revenue.
a) Firstly after creating a table i will prepare one more table for amount and month columns. After that, i will apply the dynamic partition on month column. Later i will perform insert overwrite operation on this partitioned table and will load data in it. After loading, i will check whether 12 month partitions are created. 
Set hive.dynamic.partition.mode=nonstrict;   After setting this property i will execute the queries by using where clause and month name. I will get the sum(amount) for each month. In this way we can calculate the monthly total revenue.

Q.  How can you add a new partition for the month December in the above partitioned table?
Ans :- After applying dynamic partition, each partition will get created for each distinct value, so there is no need to add any new partition for particular month, it gets created automatically.

Q.  I am inserting data into a table based on partitions dynamically. But, I received an error – FAILED ERROR IN SEMANTIC ANALYSIS: Dynamic partition strict mode requires at least one static partition column. How will you remove this error?
Ans :- By setting the below property to nonstrict this error will get resolved.
set hive.exec.dynamic.partition.mode=nonstrict;

Q. Suppose, I have a CSV file – ‘sample.csv’ present in ‘/temp’ directory with the following entries:
id first_name last_name email gender ip_address
How will you consume this CSV file into the Hive warehouse using built-in SerDe?
Ans - Firstly create a table and then use the following methods.
row format serde 'org.apache.hadoop.hive.serde2.opencsvserde'
with serdeproperties(
"separatorchar" = ",",
"quotechar" = "\"
"escapechar" = "\\"
Then load data from the /temp directory and then data will get stored in hive warehouse.

Q. Suppose, I have a lot of small CSV files present in the input directory in HDFS and I want to create a single Hive table corresponding to these files. The data in these files are in the format: {id, name, e-mail, country}. Now, as we know, Hadoop performance degrades when we use lots of small files.
So, how will you solve this problem where we want to create a single Hive table for lots of small files without degrading the performance of the system?

Ans :- These all small CSV files needs to be stored in the same input directory in HDFS. Create a external table (id, name, e-mail, country) for these columns and load and at the end just give the location path i.e input directory in HDFS. The data will get stored in incremental order and no need to create multiple files.
Whenever, if the column names are same and these files are coming on daily basis in such cases external table needs to be formed and path location should be fixed, the data will get stored incrementally. If the data is different then we have to make another directory in HDFS. 

Q. LOAD DATA LOCAL INPATH ‘Home/country/state/’
OVERWRITE INTO TABLE address;

The following statement failed to execute. What can be the cause?
The syntax for loading data is wrong. We do not use word OVERWRITE while loading data in fresh table. "OVERWRITE" is used only when we create another table and wanted to load data from the parent table for performing other operations.
