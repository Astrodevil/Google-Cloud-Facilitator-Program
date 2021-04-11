**Deploy to Kubernetes in Google Cloud: Challenge Lab**


**step 1:----------- Create a Docker image and store the Dockerfile -----------------**


gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh | bash

gcloud source repos clone valkyrie-app

cd valkyrie-app

cat > Dockerfile <<EOF

FROM golang:1.10

WORKDIR /go/src/app

COPY source .

RUN go install -v

ENTRYPOINT ["app","-single=true","-port=8080"]

EOF

docker build -t valkyrie-app:v0.0.1 .

cd ..

cd marking

./step1.sh







**step 2:----------- Test the created Docker image -----------------**


cd ..

cd valkyrie-app

docker run -p 8080:8080 valkyrie-app:v0.0.1 &

cd ..

cd marking

./step2.sh  





**step 3:----------- Push the Docker image in the Container Repository -----------------**


cd ..

cd valkyrie-app
docker tag valkyrie-app:v0.0.1 gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1
docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1
sed -i s#IMAGE_HERE#gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1#g k8s/deployment.yaml





**step 4:------------ Create and expose a deployment in Kubernetes -----------------**'


sed -i s#IMAGE_HERE#gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.1#g k8s/deployment.yaml

gcloud container clusters get-credentials valkyrie-dev --zone us-east1-d

kubectl create -f k8s/deployment.yaml

kubectl create -f k8s/service.yaml

git merge origin/kurt-dev

kubectl edit deployment valkyrie-dev





**step 5:------------ Update the deployment with a new version of valkyrie-app -----------------**


docker build -t gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.2 . 

docker push gcr.io/$GOOGLE_CLOUD_PROJECT/valkyrie-app:v0.0.2

kubectl edit deployment valkyrie-dev

docker ps

**step 6:-------------------- Create a pipeline in Jenkins to deploy your app -----------------**


docker kill container_id

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")

kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &

printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

gcloud source repos list

sed -i "s/green/orange/g" source/html.go


# Update project in Jenkinsfile

sed -i "s/YOUR_PROJECT/$GOOGLE_CLOUD_PROJECT/g" Jenkinsfile

git config --global user.email "you@example.com"     <------ put from first consol 

git config --global user.name "student"               <--------- from login status

git add .

git commit -m "built pipeline init"

git push 

in last on jenkins windows tamplate just click on built which is in nevigation manu 
