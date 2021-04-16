# METHOD 1
# Build and Secure Networks in Google Cloud: Challenge Lab

## The challenge contains 6 required tasks
1. Remove the overly permissive rules.
2. Start the bastion host instance.
3. Create a firewall rule that allows SSH (tcp/22) from the IAP service and add network tag on bastion.
4. Create a firewall rule that allows traffic on HTTP (tcp/80) to any address and add network tag on juice-shop.
5. Create a firewall rule that allows traffic on SSH (tcp/22) from the acme-mgmt-subnet network address and add network tag on juice-shop.
6. SSH to bastion host via IAP and juice-shop via bastion. 

### Task 1. Create the production environment
    The first step is to create open-access firewall rules.
    In the GCP Console go to Navigation Menu >VPC Network > Firewall.
    Click on the box next to the rule named open-access.
    Then Click on Delete to remove.
    
### 2. Start the bastion host instance
    In this step, you have a virtual machine and want to start.
    In the GCP Console go to Navigation Menu >Compute Engine > VM Instance.
    Click on the box next to the instance named bastion.
    Click on Start to run the instance.
    
### 3.Create a firewall rule that allows SSH (tcp/22) from the IAP service and add network tag on bastion
In this step, you have to create a firewall rule that allows SSH (tcp/22) from the IAP service.

    Add network tag on bastion VM.
    Go to the VM Instance page, click on the bastion instance and click the Edit option
    Now Add bastion to the Network tags field.
    At the end of the page click Save.
    
    Now you have to create a firewall for bastion:
    In the GCP Console go to Navigation Menu >VPC Network > Firewall.
    Click Create firewall rule.
    Configure the following settings:
    Field- Value
    Name- e.g. allow-ssh-from-iap
    Direction of traffic- Ingress
    Targets- Specified target tags
    Target tags-bastion
    Source IP ranges- 35.235.240.0/20
    Protocols and ports- Select TCP and enter 22 to allow SSH
    
### 4. Create a firewall rule that allows traffic on HTTP (tcp/80) to any address and add network tag on juice-shop
    In this step, you have to create a firewall rule that allows traffic on HTTP (tcp/80) to any address.
    In the GCP Console go to Navigation Menu >VPC Network > Firewall.
    Click Create firewall rule.
    Configure the following settings:
    Field- Value
    Name- e.g. allow-http-ingress
    Direction of traffic- Ingress
    Targets- Specified target tags
    Target tags-juice-shop
    Source IP ranges-0.0.0.0/0
    Protocols and ports- Select TCP and enter 80 to allow HTTP
    
    Add network tag on juice-shop VM.
    Go to the VM Instance page, click on the juice-shop instance and click the Edit option
    Now Add juice-shop to the Network tags field.
    At the end of the page click Save.
    
### 5.Create a firewall rule that allows traffic on SSH (tcp/22) from acme-mgmt-subnet network address and add network tag on juice-shop

    In this step, you have to create a firewall rule that allows traffic on SSH (tcp/22) from acme-mgmt-subnet network address.
    In the GCP Console go to Navigation Menu >VPC Network.
    Copy the IP address of the aceme-mgmt-subnet.
    
    In the GCP Console go to Navigation Menu >VPC Network > Firewall> Firewall Rules.
    Click Create firewall rule.
    Configure the following settings:
    Field- Value
    Name- e.g.allow-ssh-from-mgmt-subnet
    Direction of traffic- Ingress
    Targets- Specified target tags
    Target tags-bastion,juice-shop
    Source IP ranges- IP address range of your aceme-mgmt-subnet
    Protocols and ports- Select TCP and enter 22 to allow SSH.

### 6.SSH to bastion host via IAP and juice-shop via bastion

    After configuring the firewall rules, try to verify the environment via the bastion.
    In the GCP Console go to Navigation Menu >Compute Engine > VM Instance.
    Copy the Internal IP of the juice-shop instance.
    Then click on the SSH button in the row of the bastion instance.
    From the SSH console, access the juice-shop from the bastion using the following command:

    ssh <internal-IP-of-juice-shop>
    
    Note:Replace <internal-IP-of-juice-shop> with Internal IP





# METHOD 2




# Build and Secure Networks in Google Cloud: Challenge Lab



## Your challenge

You need to configure this simple environment securely. Your first challenge is to set up appropriate firewall rules and virtual machine tags. You also need to ensure that SSH is only available to the bastion via IAP.

For the firewall rules, make sure:

- The bastion host does not have a public IP address.
- You can only SSH to the bastion and only via IAP.
- You can only SSH to juice-shop via the bastion.
- Only HTTP is open to the world for `juice-shop`.

Tips and tricks:

- Pay close attention to the network tags and the associated VPC firewall rules.
- Be specific and limit the size of the VPC firewall rule source ranges.
- Overly permissive permissions will not be marked correct.

## Solving tasks

### Task 1: Check the firewall rules. Remove the overly permissive rules.

* Go to **VPC network** > **Firewall** > will see **open-access**
* Use the following command from the cloud console:

```yaml
gcloud compute firewall-rules delete open-access
```

### Task 2: Navigate to Compute Engine in the Cloud Console and identify the bastion host. The instance should be stopped. Start the instance

* Go to **Compute Engine**  > **VM Instances** > Select **bastion** > click on **Start**

### Task 3: The bastion host is the one machine authorized to receive external SSH traffic. Create a firewall rule that allows SSH (tcp/22) from the IAP service. The firewall rule should be enabled on bastion via a network tag.

* Run the following:

```yaml
gcloud compute firewall-rules create ssh-ingress --allow=tcp:22 --source-ranges 35.235.240.0/20 --target-tags ssh-ingress --network acme-vpc

gcloud compute instances add-tags bastion --tags=ssh-ingress --zone=us-central1-b
```

### Task 4: The `juice-shop` server serves HTTP traffic. Create a firewall rule that allows traffic on HTTP (tcp/80) to any address. The firewall rule should be enabled on juice-shop via a network tag

- Run the following:

```yaml
gcloud compute firewall-rules create http-ingress --allow=tcp:80 --source-ranges 0.0.0.0/0 --target-tags http-ingress --network acme-vpc

gcloud compute instances add-tags juice-shop --tags=http-ingress --zone=us-central1-b
```

### Task 5: You need to connect to `juice-shop` from the bastion using SSH. Create a firewall rule that allows traffic on SSH (tcp/22) from `acme-mgmt-subnet` network address. The firewall rule should be enabled on `juice-shop` via a network tag

* Run the following:

```yaml
gcloud compute firewall-rules create internal-ssh-ingress --allow=tcp:22 --source-ranges 192.168.10.0/24 --target-tags internal-ssh-ingress --network acme-vpc

gcloud compute instances add-tags juice-shop --tags=internal-ssh-ingress --zone=us-central1-b
```

### Task 6: In the Compute Engine instances page, click the SSH button for the bastion host. Once connected, SSH to `juice-shop`

* Go to **Compute Engine** > **VM instances** > **SSH** to **bastion** host
* Run the following:

```yaml
ssh <internal IP of the juice-shop>
```
