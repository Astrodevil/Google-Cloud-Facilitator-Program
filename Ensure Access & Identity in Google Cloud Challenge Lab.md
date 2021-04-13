# Ensure Access & Identity in Google Cloud: Challenge Lab

---

> Launch the lab [here](https://www.qwiklabs.com/focuses/14572?locale=en&parent=catalog)

## Your challenges

Open the Cloud Shell and copy paste these commands which are given below.

_after doing each task just **Check Your Progress**.and your marks get upadated without any error._

---

## Task 1 : Create a custom security role :-

```yaml
nano role-definition.yaml
```

_Copy the below content to file_

```yaml
title: "orca_storage_update"
description: "Permissions"
stage: "ALPHA"
includedPermissions:
  - storage.buckets.get
  - storage.objects.get
  - storage.objects.list
  - storage.objects.update
  - storage.objects.create
```

_Save by : ctrl + o -> enter -> ctrl + x_

```yaml
gcloud iam roles create orca_storage_update --project $DEVSHELL_PROJECT_ID \
--file role-definition.yaml
```

---

## Task 2 & 3 : Create a service account and Bind a custom security role to an account :-

1. Navigate to IAM & Admin -> Service Accounts -> _CREATE SERVICE ACCOUNT_.

- **Service account name** : _orca-private-cluster-sa_ ,
  then click create and add **roles**.
- **Roles**:
  - orca_storage_update
  - Monitoring Viewer
  - Monitoring Metric Writer
  - Logs Writer

Click **CONTINUE** -> **DONE**

---

## Task 4 : Create and configure a new Kubernetes Engine private cluster :-

- Replace the project id:-

```yaml
gcloud container clusters create orca-test-cluster --num-nodes 1 --master-ipv4-cidr=172.16.0.64/28 --network orca-build-vpc --subnetwork orca-build-subnet --enable-master-authorized-networks --master-authorized-networks 192.168.10.2/32 --enable-ip-alias --enable-private-nodes --enable-private-endpoint --service-account orca-private-cluster-sa@<Project ID>.iam.gserviceaccount.com --zone us-east1-b
```

---

## Task 5 : Deploy an application to a private Kubernetes Engine cluster :-

1. Navigate to **Compute Engine** -> VM instance
2. launch **SSH** to orca-jumphost VM instance

- Replace the project id :-

```yaml
gcloud container clusters get-credentials orca-test-cluster --internal-ip --zone us-east1-b --project <Project ID>
```

```yaml
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

---
