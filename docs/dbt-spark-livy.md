This README will focus on our new adapter dbt-spark-livy that allows you to use dbt along with Apache spark-livy and Cloudera Data Platform with Livy server support.

------

### **Installing requirements**

Install the requirements using pip/pip3:

``pip install -r https://raw.githubusercontent.com/cloudera/dbt-spark-livy/main/requirements.txt``

``pip install dbt-core``

``pip install dbt-spark-livy``

Install dbt by referring to [Install-dbt](https://docs.getdbt.com/docs/installation)

------

### **Setup CDP**

To work with spark in CDP, we need two things - a Spark Datahub and an user to run queries.

 
#### **User**

For this demo, we will use a Machine User created inside CDP as the user running queries. Inside CDP > User Management, add a new Machine User and set the workload password. 


The steps for this are documented here:

1. [Creating a Machine User](https://docs.cloudera.com/management-console/cloud/user-management/topics/mc-create-machine-user.html)
2. [Setting the workload password](https://docs.cloudera.com/management-console/cloud/user-management/topics/mc-setting-the-ipa-password.html)

With the user created & the workload password set, take a note of the Workload username & password. Notice in the below screenshot, for a Machine User called ‘cia_test_user’ the workload username is ‘srv_cia_test_user’. 

![userimage](https://github.com/TapasSenapati/dbt-spark-livy/blob/main/docs/alex_wood_0-1651165263767.png?raw=true)

Keep the **workload user & password** details handy for later.

--------

#### **Create Cloudera Data HUB Spark account**

1. Start by activating your CDW Environment as documented [here](https://docs.cloudera.com/data-warehouse/cloud/aws-environments/topics/dw-activating-environments-4-data-catalogs.html).
2. This will create a default database catalog, which we will use in this demo. You are able to create non-default database catalogs, as documented [here](https://docs.cloudera.com/data-warehouse/cloud/managing-warehouses/topics/dw-adding-new-database-catalog.html).
3. Next, create a datahub as documented [here](https://www.cloudera.com/tutorials/cdp-how-to-create-a-data-hub.html). Choose a cluster that provides spark . For our our use case we went with 7.2.14 - Data Engineering: Apache Spark3. Once up and running you should have a data hub similar to below
![datahubimage](https://github.com/TapasSenapati/dbt-spark-livy/blob/main/docs/datahub.png?raw=true)
4. With the datahub in running state you can view the details of the datahub by clicking on it. The livy server UI and other relevant addresses are also accessible from the same page.
![spark3](https://github.com/TapasSenapati/dbt-spark-livy/blob/main/docs/datahub_spark3.png?raw=true)
5. Go to endpoints and note down the livy server 3 endpoint.This is the endpoint dbt will be connecting to.**Note this down**.
![endpoint](https://github.com/TapasSenapati/dbt-spark-livy/blob/main/docs/endpoint.png?raw=true) 

-------
#### **Connecting dbt to Spark-Livy**

dbt requires that we configure a profile that defines how to connect to our data warehouse. For this, we need the workload credentials & connection details we collected earlier.

First lets do a quick check to see if connections are succesful. Open terminal and run command.

```
curl -u 'srv_cia_test_user':'Password123!' -v -X POST --data '{"kind": "spark"}' -H "Content-Type: application/json" -k https://dbt-spark-gateway.ciadev.cna2-sx9y.cloudera.site/dbt-spark/cdp-proxy-api/livy_for_spark3/sessions
```
Change the username password and hostname with your details. You should get a response Connection #0 to host left intact and see a new idle session being generated on the livy for spark3 ui.
 
The profile lives in a `.dbt` directory in your home directory and is called `profiles.yml`. On Linux, this would look like `~/.dbt/profiles.yml`. If you haven't used dbt before, create the directory with `mkdir ~/.dbt` and create the `profiles.yml` file with your favourite text editor.

You can learn more about the dbt profile from the dbt docs [here](https://docs.getdbt.com/dbt-cli/configure-your-profile)

Use the following template for the contents of the file:

First, add your Workload user/pass to the “user” and “password” fields.
 
Next, add the “host” field in the template we noted earlier from cdp ui step 5.[cloudera-spark](#create-cloudera-data-hub-spark-account)

For our case we created a sample dbt project using `dbt init` called spark_test. If asked for details that you are unsure go with defaults as we will anyway be updating the profile file for connection latter.

My completed profile looks like this:
```
spark_test:
  outputs:
    dev:
      host: https://dbt-spark-gateway.ciadev.cna2-sx9y.cloudera.site/dbt-spark/cdp-proxy-api/livy_for_spark3
      method: livy
      schema: testdbt
      threads: 1
      type: spark_livy
      password: Password123!
      user: srv_cia_test_user
  target: dev
```

To ensure we’ve configured our profile correctly, let’s run a connection test. For this inside of dbt folder spark_test we use the command:

dbt debug

In the output of this command, you should see the following:
All checks passed similar to this. 
![dbt_debug](https://github.com/TapasSenapati/dbt-spark-livy/blob/main/docs/dbt_debug.png?raw=true) 

This confirms a successful connection to the server.

----------

#### **Version Notes:**

The adpater has been tested on the following version.

- DBT Core 1.1.1
- spark_livy: 1.1.0 
- python version: 3.9.13
- Cloudera Manager CM VERSION 7.6.2 RUNTIME VERSION 7.2.15

All other dependencies will be pulled in while installing [requirements.](#installing-requirements)
  


