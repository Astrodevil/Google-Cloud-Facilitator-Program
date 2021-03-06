# open the cloud Shell and let's get started :
```yaml
gcloud config set compute/zone us-east1-b
git clone https://source.developers.google.com/p/$DEVSHELL_PROJECT_ID/r/sample-app
gcloud container clusters get-credentials jenkins-cd
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=$(gcloud config get-value account)
export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &
printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
```
# PREVIEW ON PORT 8080, SIGN IN JENKINS
	> username : admin
	> password : see at your cloud shell output (the previous step)

# BACK TO CLOUD SHELL, RUN :
```yaml
cd sample-app
kubectl create ns production
kubectl apply -f k8s/production -n production
kubectl apply -f k8s/canary -n production
kubectl apply -f k8s/services -n production
```
```yaml
kubectl get svc
kubectl get service gceme-frontend -n production
```
```yaml
git init
git config credential.helper gcloud.sh
git remote add origin https://source.developers.google.com/p/$DEVSHELL_PROJECT_ID/r/sample-app
git config --global user.email "[EMAIL]@qwiklabs.net"
git config --global user.name "student [NAME]"
git add .
git commit -m "initial commit"
git push origin master
```
#BACK TO JENKINS DASHBORD
	> Manage Jenkins > Manage Credentials > System
	> Select Global credentials (unrestricted)
   	> Select Add Credentials
        > Kind: Google Service Account from metadata
        > Project Name: [PROJECT_ID]
        > Select OK

#BACK TO JENKINS DASHBORD
	> New Item
	> Enter an item name: sample-app
    	> Select Multibranch Pipeline
    	> OK
    	> in sample-app config
        > Branch Sources: Git
            + Project Repository: https://source.developers.google.com/p/[PROJECT_ID]/r/sample-app
            + Credentials: qwiklabs service account
        > Scan Multibranch Pipeline Triggers, check Periodically if not otherwise run
            + Interval: 1 minute
        > SAVE 

#BACK TO CLOUD SHELL
```yaml
git checkout -b new-feature
vi main.go
```
> press "i"
	  change version number 
		The version is defined in this line : const version string = "1.0.0" 
		update it to the following : const version string = "2.0.0" (in line 46)
	> Esc -> :wq
```yaml
vi html.go
```
> press "i"
	  Change the two instances of div class="card blue" with following: div class="card orange" (in line 37 & 81)
	> Esc -> :wq

# BACK TO CLOUD SHELL, RUN :
```yaml
git add Jenkinsfile html.go main.go
git commit -m "Version 2.0.0"
git push origin new-feature
```
# CHECK YOUR JENKINS DASHBOARD

## BACK TO CLOUD SHELL, RUN:
```yaml
curl http://localhost:8001/api/v1/namespaces/new-feature/services/gceme-frontend:80/proxy/version
```
```yaml
kubectl get service gceme-frontend -n production  
git checkout -b canary
git push origin canary
export FRONTEND_SERVICE_IP=$(kubectl get -o \
jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)
git checkout master
git push origin master
```
# CHECK YOUR JENKINS DASHBOARD

# BACK TO CLOUD SHELL, RUN:
```yaml
export FRONTEND_SERVICE_IP=$(kubectl get -o \
jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)
```
```yaml
while true; do curl http://$FRONTEND_SERVICE_IP/version; sleep 1; done
```
# NOTE: after the you see output 2.0.0
```yaml
kubectl get service gceme-frontend -n production
```
# Note if task 4 not yet marked, try run (may take a long delay):
```yaml
git merge canary
git push origin master
export FRONTEND_SERVICE_IP=$(kubectl get -o \
jsonpath="{.status.loadBalancer.ingress[0].ip}" --namespace=production services gceme-frontend)
```

**Congratulations! You completed this challenge lab.**
