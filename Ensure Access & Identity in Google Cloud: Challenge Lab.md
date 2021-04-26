# Ensure-Access-Identity-in-Google-Cloud-Challenge-Lab:


## Task 1: Create a custom security role.

**Execute this command on cloud shell to create yaml file for custom role:**
```yaml
nano role-definition.yaml
```
**Replace the below content in the file:**

title: "orca_storage_update"
description: "Permissions"
stage: "ALPHA"
includedPermissions:
- storage.buckets.get
- storage.objects.get
- storage.objects.list
- storage.objects.update
- storage.objects.create





**Ctrl+x
Y
Enter**



```yaml
gcloud iam roles create orca_storage_update --project $DEVSHELL_PROJECT_ID \
--file role-definition.yaml
```


## Task 2: Create a service account.


**commands for task2 & task3:**
```yaml
gcloud iam service-accounts create orca-private-cluster-sa --display-name "my service account"

gcloud alpha projects add-iam-policy-binding $DEVSHELL_PROJECT_ID --member serviceAccount:orca-private-cluster-sa@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role "projects/<projectid>/roles/orca_storage_update"

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID --member serviceAccount:orca-private-cluster-sa@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role “roles/monitoring.viewer”

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID --member serviceAccount:orca-private-cluster-sa@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role “roles/logging.logWriter” 

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID --member serviceAccount:orca-private-cluster-sa@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role “roles/monitoring.metricWriter”
```

## Task 4: Create and configure a new Kubernetes Engine private cluster


In the command replace the **project id** 

```yaml
gcloud container clusters create orca-test-cluster --num-nodes 1 --master-ipv4-cidr=172.16.0.64/28 --network orca-build-vpc --subnetwork orca-build-subnet --enable-master-authorized-networks  --master-authorized-networks 192.168.10.2/32 --enable-ip-alias --enable-private-nodes --enable-private-endpoint --service-account orca-private-cluster-sa@<Project ID>.iam.gserviceaccount.com --zone us-east1-b
```


## Task 5: Deploy an application to a private Kubernetes Engine cluster.

```yaml
gcloud container clusters get-credentials orca-test-cluster --internal-ip --zone us-east1-b --project <Project ID>

kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

## Congratulations You completed this lab.
