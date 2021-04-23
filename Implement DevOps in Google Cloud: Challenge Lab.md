# Task 1: Configure a Jenkins pipeline for continuous deployment to Kubernetes Engine
## Inspect Provisioned Resources

   1. In the Cloud Console, navigate to **Kubernetes Engine > Clusters** to inspect the provisioned cluster named ```jenkins-cd```.

   2. Navigate to **Source Repositories**, click on ```sample-app``` and review the **Jenkinsfile** in the root of that repository.

   3. Also, review the YAML files for the three types of deployment (production, canary, and dev) in the ```sample-app/k8s``` directory.

   4. Navigate to **Compute Engine > VM instances**, click on the **SSH** button of the instance named ```kraken-jumphost```.

   5. In the SSH window, run the following command to configure the compute zone:
       ```yaml
       gcloud config set compute/zone us-east1-b
       ```
   6. (Optional) confirm that your cluster is running by executing the following commands:

      ```yaml
      gcloud container clusters list
       kubectl cluster-info
       ```

 ## Install and Setup Helm

   1. You will use Helm to install Jenkins from the Charts repository. Helm is a package manager that makes it easy to configure and deploy Kubernetes applications. To install Helm, run the following commands to fetch the Helm installer script, and then execute it locally.
```yaml
        curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
        chmod 700 get_helm.sh
        ./get_helm.sh
```
   2. Setting up and updating Helm by running the following commands:
```yaml
        helm repo add stable https://charts.helm.sh/stable
        helm repo update
```

## Configure and Install Jenkins

   1. Use git to clone the lab’s sample code:
    
```yaml
        git clone https://github.com/GoogleCloudPlatform/continuous-deployment-on-kubernetes.git
```       
   
   2. Change to the following directory:
```yaml
     cd continuous-deployment-on-kubernetes
  ```   
  
      
   3. To configure and install Jenkins, run the following command to deploy with the Helm CLI.
    
```yaml
    helm install cd stable/jenkins -f jenkins/values.yaml --version 1.2.2 --wait
```
   4. Once the command completed, ensure the Jenkins pod goes to the Running state and the container is in the READY state:

```yaml    kubectl get pods
```
   Example Output:
    
   5. The Jenkins chart will automatically create an admin password for you. To retrieve it, run:

```yaml   
     printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
```

## Connect to Jenkins

   1. Go back to the Cloud Console, then open a cloud shell and run the following command to get the credentials for your cluster:
```yaml
    gcloud container clusters get-credentials jenkins-cd --zone us-east1-b
```
   2. Run the following command to setup port forwarding to the Jenkins UI from the Cloud Shell.

```yaml
    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
    kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &
```
   (You don’t need to configure the Jenkins service account because it has already been created during provisioning.)

   3. To get to the Jenkins user interface, click on the Web Preview button in the cloud shell, then click Preview on port 8080.
      
   4. You should now be able to log in with username ```admin``` and your auto-generated password.
   
   
## Clone the Sample App Repository and Create the Production Namespace

   1. Go back to the SSH window, clone the ```sample-app``` source code from the Cloud Source Repository by running the following command:
```yaml
    gcloud source repos clone sample-app --project=<YOUR-PROJECT-ID>
```
   Remember to replace ```<YOUR-PROJECT-ID>``` with your GCP project ID

   2. Change to the sample application directory and then create the Kubernetes namespace to logically isolate the deployment:
```yaml
    cd sample-app
    kubectl create ns production
```

## Creating the Jenkins Pipeline
   Adding your service account credentials

Configure your credentials to allow Jenkins to access the code repository. Jenkins will use your cluster’s service account credentials in order to download code from the Cloud Source Repositories.

   1. In the Jenkins user interface, click **Manage Jenkins** in the left navigation then click **Manage Credentials**.

   2. Click **Jenkins**

   3. Click **Global credentials (unrestricted)**.

   4. Click **Add Credentials** in the left navigation.

   5. Select **Google Service Account from metadata** from the **Kind** drop-down and click **OK**.
    

## Configure the Jenkins job

After configuring your credentials, follow these steps to configure a Pipeline job.

   1. Click **Jenkins** to return the welcome page.

   2. Reload the page a few times until you see a pipeline job named **sample-app** in the Jenkins user interface.

   (If you fail to get any job, click **Jenkins > New Item** in the left navigation to create the multibranch pipeline.)

   3. Click on the job name of **sample-app**.
    

   4. Click **Configure** in the left pane.

   5. In the configuration page, enter **sample-app** as the Display Name in the General section.
    

   6. In the Branch Sources section, click on **Add source** and choose **Git** from the dropdown menu.

   7. Copy the Cloud Source URL of the **sample-app** repository to the **Project Repository field**. The URL should look like this:
```yaml
    https://source.developers.google.com/p/<YOUR-PROJECT-ID>/r/sample-app
```
   Select the service account for your GCP project from the **Credentials** dropdown list.
    

   8. Check **Periodically if not otherwise run** in the **Scan Multibranch Pipeline Trigger** section, and then select **1 minute** for the interval.
    

   9. Scroll to the bottom and click on **Save**.

   10. If you correctly configured the pipeline, you will see **Finished: SUCCESS** at the end of the Scan Multibranch Pipeline Log.


## Examine the Console Output from the Jenkins Pipeline

   1. Click on **sample-app » master** under Build Executor Status in the left pane, then click on the job under Build History.
    
   2. Click **Console Output** in the left navigation to monitor the build process.
   
   This process takes about five minutes to complete.


# Task 2: Push an update to the application to a development branch

   1. Go back to the SSH window, run the following command to create a development branch in the **sample-app** directory.
```yaml
    git checkout -b new-feature
```
   2. Open the **main.go** file with ```vi```, ```nano```, or the Cloud Shell Editor.

   3. Update the version in the following line:
```yaml
    const version string = "1.0.0"
```
   to
```yaml
    const version string = "2.0.0"
```
   then save the change.

   4. Open the **html.go** file and change the two instances of ```<div class="card blue">``` to ```<div class="card orange">```.

   5. Commit and push your changes by running the following commands:
```yaml
    git config --global user.email "you@example.com"
    git config --global user.name "Your Name"
    git add Jenkinsfile html.go main.go
    git commit -m "Version 2.0.0"
    git push origin new-feature
```
   6. Go to the Jenkins user interface, click **Scan Multibranch Pipeline Now** if you want to trigger the build immediately.


# Task 3: Push a Canary deployment to the production namespace

   1. Go back to the SSH window, run the following command to create a canary branch in the **sample-app** directory.
```yaml
    git checkout -b canary
```
   2. Merge the change from the development branch:
```yaml
    git merge new-feature
```
   3. Push the canary to the Git server:
```yaml
    git push origin canary
````
   4. Go to the Jenkins user interface, click **Scan Multibranch Pipeline Now** if you want to trigger the build immediately.

# Task 4: Promote the Canary Deployment to production

   1. Go back to the SSH window, run the following commands to merge the canary branch, and push it to the Git server.
```yaml
    git checkout master
    git merge canary
    git push origin master
```
   2. Go to the Jenkins user interface, click **Scan Multibranch Pipeline Now** if you want to trigger the build immediately.


**Congratulations! You completed this challenge lab.**
