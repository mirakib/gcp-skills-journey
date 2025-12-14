# Managing Deployments Using Kubernetes Engine

## Objectives

### In this lab, you will learn how to perform the following tasks:
- Use the kubectl tool
- Create deployment yaml files
- Launch, update, and scale deployments
- Update deployments and learn about deployment styles

## Prerequisites
- You have Linux System Administration skills.
- You understand DevOps theory, concepts of continuous deployment.

### Get sample code for this lab


1. Get the sample code for creating and running containers and deployments from the lab bucket:

    ```bash
    gcloud storage cp -r gs://spls/gsp053/kubernetes .
    cd kubernetes
    ```

2. Create a cluster with 3 nodes (this will take a few minutes to complete):

    ```bash
    gcloud container clusters create bootcamp \
      --machine-type e2-small \
      --num-nodes 3 \
      --scopes "https://www.googleapis.com/auth/projecthosting,storage-rw"
    ```

    <img width="1306" height="322" alt="image" src="https://github.com/user-attachments/assets/d27766df-cc5c-47d8-8c09-15a8c5777dbb" />

## Task 1. Learn about the deployment object


1. The explain command in kubectl can tell us about the deployment object:

    ```bash
    kubectl explain deployment
    ```

2. You can also see all of the fields using the --recursive option:

    ```bash
    kubectl explain deployment --recursive
    ```

3. You can use the explain command as you go through the lab to help you understand the structure of a deployment object and understand what the individual fields do:

    ```bash
    kubectl explain deployment.metadata.name
    ```


## Task 2. Create a deployment


1. Create the fortune-app deployment. Examine the deployment configuration file:

    ```bash
    cat deployments/fortune-app-blue.yaml
    ```


Output:
```yaml
# orchestrate-with-kubernetes/kubernetes/deployments/fortune-app-blue.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fortune-app-blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: fortune-app
  template:
    metadata:
      labels:
        app: fortune-app
        track: stable
        version: "1.0.0"
    spec:
      containers:
        - name: fortune-app
          # The new, centralized image path
          image: "us-central1-docker.pkg.dev/qwiklabs-resources/spl-lab-apps/fortune-service:1.0.0"
          ports:
            - name: http
              containerPort: 8080
```

Notice how the deployment is creating three replicas and it's using version 1.0.0 of the fortune-service container.

2. Go ahead and create your deployment object using kubectl create:

    ```bash
    kubectl create -f deployments/fortune-app-blue.yaml
    ```

3. Once you have created the deployment, you can verify that it was created:

    ```bash
    kubectl get deployments
    ```


3. Once the deployment is created, Kubernetes will create a ReplicaSet for the deployment. You can verify that a ReplicaSet was created for the deployment:

    ```bash
    kubectl get replicasets
    ```
You should see a ReplicaSet with a name like fortune-app-blue-xxxxxxx

4. View the Pods that were created as part of the deployment:
   
    ```bash
    kubectl get pods
    ```


5. Now, create a service to expose the fortune-app deployment externally.

    ```bash
    kubectl create -f services/fortune-app.yaml
    ```

6. Interact with the fortune-app by grabbing its external IP and then curling the /version endpoint:

    ```bash
    kubectl get services fortune-app
    ```


7. Interact with the fortune-app by grabbing its external IP and then curling the /version endpoint:

    ```bash
    kubectl get services fortune-app
    ```

8. You can also use the output templating feature of kubectl to use curl as a one-liner:

    ```bash
    curl http://`kubectl get svc fortune-app -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`/version
    ```
    <img width="1336" height="424" alt="image" src="https://github.com/user-attachments/assets/d9de8f89-fc98-4109-9c60-e66e925f0298" />

    <img width="644" height="152" alt="image" src="https://github.com/user-attachments/assets/d224e644-45d2-45a9-b211-0535e67ecf1b" />


## Scale a deployment


1. The replicas field can be most easily updated using the kubectl scale command:
```
kubectl scale deployment fortune-app-blue --replicas=5
```

2. Verify that there are now 5 fortune-app-blue Pods running:
```
kubectl get pods | grep fortune-app-blue | wc -l
```

3. Now scale back the application:
```
kubectl scale deployment fortune-app-blue --replicas=3
```

4. Again, verify that you have the correct number of Pods:
```
kubectl get pods | grep fortune-app-blue | wc -l
```
<img width="1117" height="166" alt="image" src="https://github.com/user-attachments/assets/e636f06f-71f5-4533-b82f-691e22fd74c8" />

Now you know about Kubernetes deployments and how to manage & scale a group of Pods.


## Task 3. Rolling update


## Task 4. Canary deployments

<img width="1330" height="339" alt="image" src="https://github.com/user-attachments/assets/c4d197ba-1df6-402c-8ee3-20f39dd87da5" />


## Task 5. Blue-green deployments

<img width="1349" height="320" alt="image" src="https://github.com/user-attachments/assets/e64c91f0-5318-42ba-9ff2-91b8ce6af624" />
