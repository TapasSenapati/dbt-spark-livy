This README will focus on our new adapter dbt-spark-livy that allows you to use dbt along with Apache spark-livy and Cloudera Data Platform with Livy server support.

------

### **Installing requirements**

Install the requirements using pip/pip3:

``pip install -r https://raw.githubusercontent.com/cloudera/dbt-spark-livy/main/requirements.txt``

``pip install dbt-core``

``pip install dbt-spark-livy``

------

### **Setup CDP**

To work with spark in CDP, we need two things - a Spark Datahub and an user to run queries.

 
#### **User**

For this demo, we will use a Machine User created inside CDP as the user running queries. Inside CDP > User Management, add a new Machine User and set the workload password. 


The steps for this are documented here:

1. [Creating a Machine User](https://docs.cloudera.com/management-console/cloud/user-management/topics/mc-create-machine-user.html)
2. [Setting the workload password](https://docs.cloudera.com/management-console/cloud/user-management/topics/mc-setting-the-ipa-password.html)

With the user created & the workload password set, take a note of the Workload username & password. Notice in the below screenshot, for a Machine User called ‘cia_test_user’ the workload username is ‘srv_cia_test_user’. 

![userimage](https://community.cloudera.com/t5/image/serverpage/image-id/34286iE85E825F1278E53D/image-size/medium?v=v2&px=400)

Keep the workload user & password details handy for later.

#### **Cloudera Data HUB Spark**

1. Start by activating your CDW Environment as documented [here](https://docs.cloudera.com/data-warehouse/cloud/aws-environments/topics/dw-activating-environments-4-data-catalogs.html).
2. This will create a default database catalog, which we will use in this demo. You are able to create non-default database catalogs, as documented [here](https://docs.cloudera.com/data-warehouse/cloud/managing-warehouses/topics/dw-adding-new-database-catalog.html).
3. Next, create a datahub as documented [here](https://www.cloudera.com/tutorials/cdp-how-to-create-a-data-hub.html). Choose a cluster that provides spark . For our our use case we went with 7.2.14 - Data Engineering: Apache Spark3
   






