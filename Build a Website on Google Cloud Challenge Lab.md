# Build a Website on Google Cloud: Challenge Lab

> Launch the lab [here](https://www.qwiklabs.com/focuses/11765?parent=catalog)

## Your challenges

Open the Cloud Shell and copy paste these commands which are given below.

_after doing each task wait for atleast 20 sec then **check your progress**.it will take some time to update your assessment._

---

## Task 1: Download the monolith code and build your container :-

```yaml
git clone https://github.com/googlecodelabs/monolith-to-microservices.git
```

```yaml
cd ~/monolith-to-microservices
./setup.sh
```

```yaml
cd ~/monolith-to-microservices/monolith
npm start
```

```yaml
gcloud services enable cloudbuild.googleapis.com
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/fancytest:1.0.0 .
```

---

## Task 2: Create a kubernetes cluster and deploy the application :-

```yaml
gcloud config set compute/zone us-central1-a
gcloud services enable container.googleapis.com
gcloud container clusters create fancy-cluster --num-nodes 3
```

```yaml
kubectl create deployment fancytest --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/fancytest:1.0.0
kubectl expose deployment fancytest --type=LoadBalancer --port 80 --target-port 8080
```

---

## Task 3: Create a containerized version of your Microservices :-

```yaml
cd ~/monolith-to-microservices/microservices/src/orders
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/orders:1.0.0 .
```

```yaml
cd ~/monolith-to-microservices/microservices/src/products
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/products:1.0.0 .
```

---

## Task 4: Deploy the new microservices :-

```yaml
kubectl create deployment orders --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/orders:1.0.0
kubectl expose deployment orders --type=LoadBalancer --port 80 --target-port 8081
```

```yaml
kubectl create deployment products --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/products:1.0.0
kubectl expose deployment products --type=LoadBalancer --port 80 --target-port 8082
```

---

## Task 5: Configure the Frontend microservice :-

```yaml
cd ~/monolith-to-microservices/react-app
nano .env
```

_In this step just Press **Ctrl+X**._

Now run further commands.

---

## Task 6: Create a containerized version of the Frontend microservice :-

```yaml
cd ~/monolith-to-microservices/microservices/src/frontend
gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/frontend:1.0.0 .
```

---

## Task 7: Deploy the Frontend microservice :-

```yaml
kubectl create deployment frontend --image=gcr.io/${GOOGLE_CLOUD_PROJECT}/frontend:1.0.0
```

```yaml
kubectl expose deployment frontend --type=LoadBalancer --port 80 --target-port 8080
```

