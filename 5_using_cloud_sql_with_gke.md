# AK8S-18 Using Cloud SQL with Kubernetes Engine 

## Overview

In this lab, you set up a Kubernetes Deployment of WordPress connected to Cloud SQL via the SQL Proxy. The SQL Proxy lets you interact with a Cloud SQL instance as if it were installed locally (localhost:3306), and even though you are on an unsecured port locally, the SQL Proxy makes sure you are secure over the wire to your Cloud SQL Instance.

To complete this lab you'll create several components. First you create a GKE cluster, next you create a Cloud SQL Instance to connect to, and a Service Account to provide permission for your Pods to access the Cloud SQL Instance. Finally you deploy WordPress on your GKE cluster, with the SQL Proxy as a Sidecar, connected to your Cloud SQL Instance.

## Objectives

In this lab, you learn how to perform the following tasks:

-    Create a Cloud SQL instance and database for Wordpress

-    Create credentials and Kubernetes Secrets for application authentication

-    Configure a Deployment with a Wordpress image to use SQL Proxy

-    Install SQL Proxy as a sidecar container and use it to provide SSL access to a CloudSQL instance external to the GKE Cluster


## 0. Activate Google Cloud Shell

```bash
gcloud auth list

gcloud config list project
```

## 1. Create a GKE Cluster

In Cloud Shell, type the following command to create environment variables for the GCP zone and cluster name that will be used to create the cluster for this lab.

```bash
export my_zone=us-central1-a
export my_cluster=standard-cluster-1
```

Configure tab completion for the kubectl command-line tool.
```bash
source <(kubectl completion bash)
```

Create a VPC-native Kubernetes cluster.
```bash
gcloud container clusters create $my_cluster \
   --num-nodes 3 --enable-ip-alias --zone $my_zone
```

Configure access to your cluster for kubectl:
```bash
gcloud container clusters get-credentials $my_cluster --zone $my_zone
```
    
In Cloud Shell enter the following command to clone the lab repository to the lab Cloud Shell.
```bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```

Create a soft link as a shortcut to the working directory.
```bash
ln -s ~/training-data-analyst/courses/ak8s/v1.1 ~/ak8s
```

Change to the directory that contains the sample files for this lab.
```bash
cd ~/ak8s/Cloud_SQL/
```

## 2. Enable Cloud SQL APIs

1. In the GCP Console, on the `Navigation menu`, click `APIs & Services`.

2. Click `+ Enable APIs AND Services`.

3. For `Search for APIs & Services`, type `SQL` and then click the `Cloud SQL` API tile.
    
4. Click `Enable` to enable Cloud SQL API.

If the API is already enabled, a `Manage` button appears instead, with an `API enabled` message. In that case, no action is required.

5. Again in the `Search for APIs & Services`, search for `Cloud SQL Admin API` and click it.

6. Click `Enable` to enable Cloud SQL Admin API.

If the API is already enabled, a `Manage` button appears instead, with an `API enabled` message. In that case, no action is required.




## 3. Create a Cloud SQL Instance

1. In the Cloud Shell, run the following command to create the instance:
```bash
gcloud sql instances create sql-instance --tier=db-n1-standard-2 --region=us-central1
```

2. In the GCP Console, navigate to `SQL`.
    
3. You should see sql-instance listed , click on the name, and then click on the `Users` tab.

You will have to wait a few minutes for the Cloud SQL instance to be provisioned. When you see the existing root user listed you can proceed to the next step.

![](https://cdn.qwiklabs.com/6a%2FHhRHEK%2F59%2Fjb6iz8qN3DCiSGjoQWmai0HzZefV8w%3D)

4. Click __ADD USER ACCOUNT__ and create an account, using `sqluser` as the User name and `sqlpassword` as the Password.

Leave the __Host name__ option set to __Allow any host (%)__. and click __ADD__.

5. Go back to __Overview__ tab, still in your instance (`sql-instance`), and copy your Instance connection name.

example name: qwiklabs-gcp-04-b80574c7b876:us-central1:sql-instance

You will probably need to scroll down a bit to see it.

6. Create an environment variable to hold your Cloud SQL instance name, substituting the placeholder with the name you copied in the previous step.

```bash
export SQL_NAME=[Cloud SQL Instance Name]
```

7. Your command should look similar to the following:

![](https://cdn.qwiklabs.com/kxgM2Xhb77QFHrip7gwjsRmBBCDLhJfCpLu%2FQngocxc%3D)


8. Connect to your Cloud SQL instance.
```bash
gcloud sql connect sql-instance
```

9. When prompted to enter the root password press enter. The root SQL user password is blank by default.

The `mysql>` prompt appears indicating that you are now connected to the Cloud SQL instance using the MySQL client.

10. Create the database required for Wordpress. This is called wordpress by default.
```bash
create database wordpress;
```

11. Select the wordpress database:
```
use wordpress;
```

12. Select the wordpress database:
```bash
show tables;
```

This will report Empty set as you have not created any tables yet.

13. Exit the MySQL client.
```bash
exit;
```



## 4. Prepare a service account with permission to access Cloud SQL

1. To create a Service Account, in the GCP Console navigate to __IAM & admin> Service accounts__.

2. Click __+ Create Service Account__.

3. Specify a __Service account name__ called `sql-access` then click __CREATE AND CONTINUE__.

4. Click __Select a role__.
    
5. Search for __Cloud SQL__, select __Cloud SQL Client__ and click __Continue__.
    
6. Click __DONE__.
    
7. Click three dots in the far right of your recently created Service account and click __Manage keys__.
    
8. Click on __ADD KEY__ and select __Create new key__.
    
9. Make sure __JSON__ is selected under Key type. Click __CREATE__.

This will create a public/private key pair, and download the private key file automatically to your computer. You'll need this JSON file later.

10. Click __Close__ to close the notification dialog.

11. Locate the JSON credential file you downloaded and rename it to `credentials.json`.




## 5. Create Secrets
You create two Kubernetes Secrets: one to provide the MySQL credentials and one to provide the Google credentials (the service account).

1. To create a Secret for your MySQL credentials, enter the following in the Cloud Shell.
```bash
kubectl create secret generic sql-credentials \
   --from-literal=username=sqluser\
   --from-literal=password=sqlpassword
```
If you used a different username and password when creating the Cloud SQL user accounts substitute those here.

2. In the Cloud Shell, click the __More icon__ on the far right of the Cloud Shell menu bar and select __Upload File__.

![](https://cdn.qwiklabs.com/6QZFMSH4FXArKe3dUeY5mPDeN6VqmVFsHn1%2F7hrPQvc%3D)

3. Select `credentials.json` credential file you downloaded in the previous task to the Cloud Shell.

4. In the Cloud Shell move the credential file to the current working directory.
```bash
mv ~/credentials.json .
```
Files uploaded to the Cloud Shell are uploaded to the user's home directory. It is easier to work with files in the current working directory with kubectl so moving it makes the next step simpler.

5. Create a Secret for your GCP Service Account credentials using the following command:
```bash
kubectl create secret generic google-credentials\
   --from-file=key.json=credentials.json
```
Note that the file is uploaded to the Secret using the name key.json. That is the file name that a container will see when this Secret is attached as a Secret Volume.




## 6. Deploy the SQL Proxy Agent as a sidecae container

A sample deployment manifest file called sql-proxy.yaml has been provided for you that deploys a demo Wordpress application container with the SQL Proxy agent as a sidecar container.

In the Wordpress container environment settings the WORDPRESS_DB_HOST is specified using the localhost IP address. The cloudsql-proxy sidecar container is configured to point to the Cloud SQL instance you created in the previous task. The database username and password are passed to the Wordpress container as secret keys, and the JSON credentials file is passed to the container using a Secret volume. A Service is also created to allow you to connect to the Wordpress instance from the internet.

`sql-proxy.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
  template:
    metadata:
      labels:
        app: wordpress
    spec:
      containers:
        - name: web
          image: gcr.io/cloud-marketplace/google/wordpress
          ports:
            - containerPort: 80
          env:
            - name: WORDPRESS_DB_HOST
              value: 127.0.0.1:3306
            # These secrets are required to start the pod.
            # [START cloudsql_secrets]
            - name: WORDPRESS_DB_USER
              valueFrom:
                secretKeyRef:
                  name: sql-credentials
                  key: username
            - name: WORDPRESS_DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: sql-credentials
                  key: password
            # [END cloudsql_secrets]
        # Change <INSTANCE_CONNECTION_NAME> here to include your GCP
        # project, the region of your Cloud SQL instance and the name
        # of your Cloud SQL instance. The format is
        # $PROJECT:$REGION:$INSTANCE
        # [START proxy_container]
        - name: cloudsql-proxy
          image: gcr.io/cloudsql-docker/gce-proxy:1.11
          command: ["/cloud_sql_proxy",
                    "-instances=<INSTANCE_CONNECTION_NAME>=tcp:3306",
                    "-credential_file=/secrets/cloudsql/key.json"]
          # [START cloudsql_security_context]
          securityContext:
            runAsUser: 2  # non-root user
            allowPrivilegeEscalation: false
          # [END cloudsql_security_context]
          volumeMounts:
            - name: cloudsql-instance-credentials
              mountPath: /secrets/cloudsql
              readOnly: true
        # [END proxy_container]
      # [START volumes]
      volumes:
        - name: cloudsql-instance-credentials
          secret:
            secretName: google-credentials
      # [END volumes]
---
apiVersion: "v1"
kind: "Service"
metadata:
  name: "wordpress-service"
  namespace: "default"
  labels:
    app: "wordpress"
spec:
  ports:
  - protocol: "TCP"
    port: 80
  selector:
    app: "wordpress"
  type: "LoadBalancer"
  loadBalancerIP: ""
```

The important sections to note in this manifest are:

- In the Wordpress env section the variable `WORDPRESS_DB_HOST` is set to `127.0.0.1:3306`. This will connect to a container in the same Pod listening on port 3306. This is the port that the SQL-Proxy listens on by default.

- In the Wordpress env section the variables `WORDPRESS_DB_USER` and `WORDPRESS_DB_PASSWORD` are set using values stored in the `sql-credential` Secret you created in the last task.

- In the `cloudsql-proxy` container section the command switch that defines the SQL Connection name, `"-instances=<INSTANCE_CONNECTION_NAME>=tcp:3306"`, contains a placeholder variable that is not configured using a ConfigMap or Secret and so must be updated directly in this example manifest to point to your Cloud SQL instance.

- In the `cloudsql-proxy` container section the JSON credential file is mounted using the Secret volume in the directory `/secrets/cloudsql/` . The command switch `"-credential_file=/secrets/cloudsql/key.json"` points to the filename in that directory that you specified when creating the `google-credentials` Secret.

- The Service section at the end creates an external LoadBalancer called "wordpress-service" that allows the application to be accessed from external internet addresses.

1. Use sed to update the placeholder variable for the SQL Connection name to the instance name of your Cloud SQL instance.
```bash
sed -i 's/<INSTANCE_CONNECTION_NAME>/'"${SQL_NAME}"'/g'\
   sql-proxy.yaml
```

2. Deploy the application.
```bash
kubectl apply -f sql-proxy.yaml
```

3. Query the status of the Deployment:
```bash
kubectl get deployment wordpress
```
Repeat this command until you that one instance is available.
```txt
NAME      DESIRED CURRENT UP-TO-DATE  AVAILABLE   AGE
wordpress 1       1       1           1           2m
```

4. List the services in your GKE cluster.
```bash
kubectl get services
```
The external LoadBalancer ip-address for the wordpress-service is the address you use to connect to your Wordpress blog. Repeat the command until you get an external address as shown here.
```txt
NAME              TYPE         CLUSTER-IP   EXTERNAL-IP    PORT(S)
kubernetes        ClusterIP    10.12.0.1    <none>         443/TCP
wordpress-service LoadBalancer 10.12.3.17   35.238.218.6   80:31095/TCP
```


## 7. Connect to your Wordpress instance

1. Open a new browser tab and connect to your Wordpress site using the external LoadBalancer ip-address. This will start the initial Wordpress installation wizard.

2. Select English (United States) and click Continue.

3. Enter a sample name for the Site Title.

4. Enter a Username and Password to administer the site.

5. Enter an email address.

None of these values are particularly important, you will not need to use them.

6. Click Install Wordpress.

After a few seconds you will see the Success! Notification. You can log in if you wish to explore the Wordpress admin interface but it is not required for the lab.

The initialization process has created new database tables and data in the wordpress database on your Cloud SQL instance. You will now validate that these new database tables have been created using the SQL proxy container.

7. Switch back to the Cloud Shell and connect to your Cloud SQL instance.
```bash
gcloud sql connect sql-instance
```

8. When prompted to enter the root password press enter. The root SQL user password is blank by default.

The `mysql>` prompt appears indicating that you are now connected to the Cloud SQL instance using the MySQL client.

9. Select the wordpress database:
```sql
use wordpress;
```

10. Select the wordpress database:
```sql
show tables;
```

This will now show a number of new database tables that were created when Wordpress was initialized demonstrating that the sidecar SQL Proxy container was configured correctly.

mysql> show tables;
+-----------------------+
| Tables_in_wordpress   |
+-----------------------+
| wp_commentmeta        |
| wp_comments           |
| wp_links              |
| wp_options            |
| wp_postmeta           |
| wp_posts              |
| wp_term_relationships |
| wp_term_taxonomy      |
| wp_termmeta           |
| wp_terms              |
| wp_usermeta           |
| wp_users              |
+-----------------------+
12 rows in set (0.19 sec)

11. List all of the Wordpress user table entries:
```sql
select * from wp_users;
```
This will list the database record for the Wordpress admin account showing the email you chose when initializing Wordpress.

12. Exit the MySQL client.
```sql
exit;
```