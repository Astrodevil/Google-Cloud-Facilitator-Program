*1.Create a project Jumphost instance*

The first step is to create a Jumphost instance

*In the GCP Console go to Navigation Menu >Compute Engine > VM Instance.
*Write the below parameters, check machine type, and Image type.
*The name of instance be nucleus-jumphost
*Region be Default Region
*Zone be Default Zone
*The machine type be f1-micro.
*Using the default image type (Debian Linux)
*Click Create


*2.Create a Kubernetes service cluster*

In this step, you have to create a Kubernetes Service Cluster

*Create the cluster in the us-east1 region.
*Using the Docker container hello-app (`gcr.io/google-samples/ hello-app:2.0`) as a place holder.
*Open the app on port 8080
*Activate Cloud Shell and write the following commands
  ```yaml
  gcloud config set compute/zone us-east1-b
  gcloud container clusters create nucleus-jumphost-webserver1
  gcloud container clusters get-credentials nucleus-jumphost-webserver1
  kubectl create deployment hello-app --image=gcr.io/google-samples/hello-app:2.0
  kubectl expose deployment hello-app --type=LoadBalancer --port 8080
  kubectl get service
  ```
It will create a Kubernetes cluster


*3.Setup an HTTP load balancer*

In this step, you have to create a serve the site via Nginx web servers

*Activate the cloud shell and Copy and Paste the following commands
  ```yaml
  cat << EOF > startup.sh
  #! /bin/bash
  apt-get update
  apt-get install -y nginx
  service nginx start
  sed -i — ‘s/nginx/Google Cloud Platform — ‘“\$HOSTNAME”’/’ /var/www/html/index.nginx-debian.html
  EOF
  ```
Now you have to perform the steps to HTTP(s) Load Balancer in front of two web servers

*Creating an instance template:
  ```yaml
  gcloud compute instance-templates create nginx-template --metadata-from-file startup-script=startup.sh
```
*Creating a target pool:
  ```yaml
  gcloud compute target-pools create nginx-pool  (press n if asked)
  ```

*3. Creating a managed instance group:*
 ```yaml 
 gcloud compute instance-groups managed create nginx-group \
  --base-instance-name nginx \
  --size 2 \
  --template nginx-template \
  --target-pool nginx-pool 
  gcloud compute instances list
  ```

*4. Creating a firewall rule to allow traffic (80/tcp):*
  ```yaml
  gcloud compute firewall-rules create www-firewall --allow tcp:80
  gcloud compute forwarding-rules create nginx-lb \
  --region us-east1 \
  --ports=80 \
  --target-pool nginx-pool
  gcloud compute forwarding-rules list
  ```

*5. Creating a health check:*  
  ```yaml
  gcloud compute http-health-checks create http-basic-check
  gcloud compute instance-groups managed set-named-ports nginx-group --named-ports http:80
  ```

*6. Creating a backend service and attach the managed instance group:*
  ```yaml
  gcloud compute backend-services create nginx-backend \
  --protocol HTTP --http-health-checks http-basic-check --global
  ```
  ```yaml
  gcloud compute backend-services create nginx-backend \
  --protocol HTTP --http-health-checks http-basic-check --global
  
  gcloud compute url-maps create web-map --default-service nginx-backend
  ```
  ```yaml
  gcloud compute target-http-proxies create http-lb-proxy --url-map web-map
  
  gcloud compute forwarding-rules create http-content-rule \
  --global \
  --target-http-proxy http-lb-proxy \
  --ports 80
  ```
  ```yaml
  gcloud compute forwarding-rules list
  ```
