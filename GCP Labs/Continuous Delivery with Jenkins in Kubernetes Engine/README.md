# Continuous Delivery with Jenkins in Kubernetes Engine 

In this lab, you learn how to set up a continuous delivery pipeline with Jenkins on Kubernetes engine. Jenkins is the go-to automation server used by developers who frequently integrate their code in a shared repository. The solution you build in this lab is similar to the following diagram:

<img width="1504" height="1050" alt="image" src="https://github.com/user-attachments/assets/8f57ca04-b4a8-4d25-ae60-94e83bed83e7" />

### In this lab, you complete the following tasks to learn about running Jenkins on Kubernetes:

- Provision a Jenkins application into a Kubernetes Engine cluster
- Set up your Jenkins application using Helm Package Manager
- Explore the features of a Jenkins application
- Create and exercise a Jenkins pipeline

# Task 1. Download the source code

# Task 2. Provision Jenkins

Create a Kubernetes cluster and enable Jenkins to access GitHub repository and Google Container Registry.

```bash
gcloud container clusters create jenkins-cd \
--num-nodes 2 \
--machine-type e2-standard-2 \
--scopes "https://www.googleapis.com/auth/source.read_write,cloud-platform"
```
<img width="1057" height="325" alt="image" src="https://github.com/user-attachments/assets/90a265df-365e-4389-aa69-d7fcf4a483f0" />

# Task 3. Set up Helm

1. Add Helm's stable chart repo:
    ```bash
    helm repo add jenkins https://charts.jenkins.io
    ```
2. Ensure the repo is up to date:
    ```bash
    helm repo update
    ```

# Task 4. Install and configure Jenkins

When installing Jenkins, a values file can be used as a template to provide values that are necessary for setup.

You use a custom values file to automatically configure your Kubernetes Cloud and add the following necessary plugins:

    Kubernetes:latest
    Workflow-multibranch:latest
    Git:latest
    Configuration-as-code:latest
    Google-oauth-plugin:latest
    Google-source-plugin:latest
    Google-storage-plugin:latest

This allows Jenkins to connect to your cluster and your Google Cloud project.

1. Use the Helm CLI to deploy the chart with your configuration settings:

    ```bash
    helm install cd jenkins/jenkins -f jenkins/values.yaml --wait
    ```

2. Once that command completes ensure the Jenkins pod goes to the Running state and the container is in the READY state:
    
    ```bash
    kubectl get pods
    ```
3. Configure the Jenkins service account to be able to deploy to the cluster:

    ```bash
    kubectl create clusterrolebinding jenkins-deploy --clusterrole=cluster-admin --serviceaccount=default:cd-jenkins
    ```

4. Run the following command to set up port forwarding to the Jenkins UI from Cloud Shell:

   ```bash
   export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/            instance=cd" -o jsonpath="{.items[0].metadata.name}")
   kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &
   ```

5. Now, check that the Jenkins Service was created properly:

   ```bash
   kubectl get svc
   ```

# Task 5. Connect to Jenkins

1. The Jenkins chart automatically creates an admin password for you. To retrieve it, run:
   
   ```bash
   printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
   ```

3. To get to the Jenkins user interface, in the Cloud Shell action bar, click Web Preview on port 8080:

4. If asked, log in with username admin and your auto-generated password.

# Task 6. Understand the application

The application mimics a microservice by supporting two operation modes.

- In backend mode: gceme listens on port 8080 and returns Compute Engine instance metadata in JSON format.
- In frontend mode: gceme queries the backend gceme service and renders the resulting JSON in the user interface.

  <img width="737" height="439" alt="image" src="https://github.com/user-attachments/assets/76a321fe-8bbf-4aa3-a8aa-68e215ca7302" />

# Task 7. Deploy the application

Deploy the application into two different environments:

- Production: The live site that your users access.
- Canary: A smaller-capacity site that receives only a percentage of your user traffic. Use this environment to validate your software with live traffic before it's released to all your users.


1. In Google Cloud Shell, navigate to the sample application directory:

```cd sample-app```

2. Create the Kubernetes namespace to logically isolate the deployment:

```kubectl create ns production```

3. Create the production and canary deployments, and the services using the kubectl apply commands:

```kubectl apply -f k8s/production -n production```

```kubectl apply -f k8s/canary -n production```

```kubectl apply -f k8s/services -n production```

