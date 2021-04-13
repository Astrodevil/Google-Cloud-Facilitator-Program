# Build and Secure Networks in Google Cloud: Challenge Lab

> Launch the lab [here](https://www.qwiklabs.com/focuses/12068?parent=catalog)

## Your challenges

Open the Cloud Shell and copy paste these commands which are given below.

_after doing each task just **Check Your Progress**.and your marks get upadated without any error._

---

## Task 1:Remove the overly permissive rules

```yaml
gcloud compute firewall-rules delete open-access
```

---

## Task 2:Start the bastion host instance

```yaml
gcloud compute instances start bastion
```

---

## Task 3:Create a firewall rule that allows SSH (tcp/22) from the IAP service and add network tag on bastion

```yaml
gcloud compute firewall-rules create ssh-ingress --allow=tcp:22 --source-ranges 35.235.240.0/20 --target-tags ssh-ingress --network acme-vpc
gcloud compute instances add-tags bastion --tags=ssh-ingress --zone=us-central1-b
```

---

## Task 4:Create a firewall rule that allows traffic on HTTP (tcp/80) to any address and add network tag on juice-shop

```yaml
gcloud compute firewall-rules create http-ingress --allow=tcp:80 --source-ranges 0.0.0.0/0 --target-tags http-ingress --network acme-vpc
```

---

## Task 5:Create a firewall rule that allows traffic on SSH (tcp/22) from acme-mgmt-subnet network address and add network tag on juice-shop

```yaml
gcloud compute instances add-tags juice-shop --tags=http-ingress --zone=us-central1-b
```

---

## Task 6:SSH to bastion host via IAP and juice-shop via bastion

```yaml
gcloud compute firewall-rules create internal-ssh-ingress --allow=tcp:22 --source-ranges 192.168.10.0/24 --target-tags internal-ssh-ingress --network acme-vpc
```

---

## Task 7:

```yaml
gcloud compute instances add-tags juice-shop --tags=internal-ssh-ingress --zone=us-central1-b
```

---
