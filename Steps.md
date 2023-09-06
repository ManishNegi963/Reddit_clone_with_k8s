# Prerequisites to be installed:

EC2 ( AMI- Ubuntu, Type- t2.medium{for Deployment server}, t2.micro {for CI derver} )

Docker

Minikube

kubectl

# Step 1 Run these commands

# For Docker Installation on CI server and Deployment server
    sudo apt-get update
    sudo apt-get install docker.io -y
    sudo usermod -aG docker $USER && newgrp docker

# For Minikube & Kubectl on Deployment server only
    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    sudo install minikube-linux-amd64 /usr/local/bin/minikube 

    sudo snap install kubectl --classic
    minikube start --driver=docker

# Step 2 Clone the source code to the CI server

    git clone https://github.com/ManishNegi963/reddit-clone-k8s-ingress.git

<img width="602" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/63ebe511-ae30-4c1c-9b5b-e6d3dea93f59">

# Step 3 Make a container of Application using docker by writing a Dockerfile


FROM node:19-alpine3.15

WORKDIR /reddit-clone

COPY . /reddit-clone

RUN npm install 

EXPOSE 3000

CMD ["npm","run","dev"]

<img width="600" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/37f1d00e-c45d-4dad-8287-41b02754e300">

# Step 4 Building docker image from Dockerfile and tagging the image

    docker build . -t mnmanish/reddit-clone

 <img width="602" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/b764abf6-681f-4fa9-81c0-8ce8789733e7">

# Step 5 Login to dockerHub and push image to dockerHub

    docker login

  <img width="600" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/3a2b9169-c551-4702-9a9e-521745d49e04">


    docker push mnmanish/reddit-clone:latest

  <img width="597" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/e1a248f0-95a6-4068-a5f1-e02e4dfa3a47">

# Step 6 Now, go to deployment server and write deployment file.

    vim deployment.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: reddit-clone-deployment
  labels:
    app: reddit-clone
spec:
  replicas: 2
  selector:
    matchLabels:
      app: reddit-clone
  template:
    metadata:
      labels:
        app: reddit-clone
    spec:
      containers:
      - name: reddit-clone
        image: mnmanish/reddit-clone
        ports:
        - containerPort: 3000

# Step 7 Write service,yml file

    vim service.yml

apiVersion: v1
kind: Service
metadata:
  name: reddit-clone-service
  labels:
    app: reddit-clone
spec:
  type: NodePort
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 31000
  selector:
    app: reddit-clone
    
# Step 8 Deploy the deployment.yml file and service.yml file.

    kubectl apply -f deployment.yml

  <img width="605" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/bcd76e8a-b500-4db3-b3b9-c0cc060e8d5f">

--

<img width="609" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/312f3fd7-aaba-4bfa-816a-9e2467beedfb">

--

<img width="596" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/93a7fedc-2632-42c8-8455-2875d09aed6e">

    kubectl apply -f service.yml

  <img width="577" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/c1007b61-3a7d-49a3-a49f-7b672b197c0f">

--

<img width="598" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/f5261cec-1113-4ee3-ba42-6f3e1b2707ab">

# Step 9 Expose the app

    kubectl expose deployment reddit-clone-deployment --type NodePort
 
  <img width="597" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/2ad5fc0b-a423-4e1e-a8b1-cd14cad9e30e">

# Step 10 Expose the app service

    kubectl port-forward svc/reddit-clone-service 3000:3000 --address 0.0.0.0 &

<img width="609" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/5696923b-74fa-47c3-9f42-7d4825090d89">

# Step 11 Open port on Security group of Deployment server

<img width="599" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/b275fca6-61b1-430b-8427-2da0ffce6ae2">

--  

<img width="606" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/420bc098-9006-4504-87cc-769b5d06ff12">


-- 

# Reddit clone app is deployed.

<img width="534" alt="image" src="https://github.com/ManishNegi963/Reddit_clone_with_k8s/assets/124788172/2fd36232-9efe-4566-95c7-919d41403241">


  

