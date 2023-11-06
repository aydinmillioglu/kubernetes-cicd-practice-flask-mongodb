# Simple Containerized CRUD-Web using MongoDB and Flask App

This repository is a simple example of a Python Flask application functioning as a "Task Manager" with MongoDB as the database management system. The Python-MongoDB connector used in this project is PyMongo.

This repository is built on top of [rianrajagede/flask-mongodb](https://github.com/rianrajagede/flask-mongodb).

Host info of the db is left dynamic and set via env variables. It allows us to change the environments like test, prp or prod. By this way, env changes does not require rebuild. 

## Table of Contents

1. [Docker Compose Installation](#docker-compose-installation)
2. [Minikube Installation](#minikube-installation)
3. [Kubernetes Installation](#kubernetes-installation)
4. [CI/CD Integration](#cicd-integration)

## Docker Compose Installation

To set up the application using Docker Compose, follow these steps:

```bash
docker-compose build
host="mongo" docker-compose up
```

The app will be accessible at [localhost:5002](http://localhost:5002).

## Minikube Installation

If you're using macOS, start by installing Minikube with Homebrew:

```bash
brew install minikube
```

Make sure you have Docker installed. If not, you can download and install it from [Docker Desktop](https://www.docker.com/products/docker-desktop/).

Now, start Minikube:

```bash
minikube start
```

## Kubernetes Installation

Follow these steps to set up the application in a Kubernetes cluster:

1. Create a namespace:

   ```bash
   kubectl create namespace taskmanager
   ```

2. After starting Minikube and creating namespace, deploy the app and the database:

   ```bash
   kubectl apply -f kubernetes/mongo-deployment.yaml -f kubernetes/mongo-service.yaml -f kubernetes/app-deployment.yaml -f kubernetes/app-service.yaml
   ```

## CI/CD Integration

If Jenkins is not already installed, you can install it using Kubernetes:

```bash
kubectl apply -f kubernetes/jenkins.yaml
```

To obtain the initial password for Jenkins, use the following command:

```bash
kubectl logs jenkins-deployment-65cd757474-4pfkq -n taskmanager
```

Access Jenkins at [http://127.0.0.1:8080](http://127.0.0.1:8080).

CI/CD Pipeline:

1. Navigate to "Manage Jenkins" > "Credentials" > "(global)" > "Add Credentials" and add the following credentials:
    - **id:** dockerhub-cred

2. Navigate to "Manage Jenkins" > "Credentials" > "(global)" > "Add Credentials" and add the following credentials:
    - **id:** github-cred

3. Create a new pipeline job named "task-manager" and configure it as follows:
   - Choose "Pipeline script from SCM"
   - Add your project URL: [https://github.com/aydinmillioglu/kubernetes-cicd-practice-flask-mongodb.git](https://github.com/aydinmillioglu/kubernetes-cicd-practice-flask-mongodb.git)
   - Use GitHub credentials that we added.