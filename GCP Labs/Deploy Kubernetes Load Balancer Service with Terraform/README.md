# Deploy Kubernetes Load Balancer Service with Terraform

## Objectives
In this lab, you will learn how to:
- Deploy a Kubernetes cluster along with a service using Terraform

## Prerequisites
For this lab, you should have experience with the following:
- Familiarity with Kubernetes Services
- Familiarity with kubectl CLI.

### Task 1. Clone the sample code


1. In Cloud Shell, start by cloning the sample code:
   
    ```bash
    gsutil -m cp -r gs://spls/gsp233/* .
    ```
    
3. Navigate to the tf-gke-k8s-service-lb directory:
   
    ```bash
    cd tf-gke-k8s-service-lb
    ```

### Task 2. Understand the code


1. Review the contents of the main.tf file:

    ```bash
    cat main.tf
    ```

2. Review the contents of the k8s.tf file:

    ```bash
    cat k8s.tf
    ```

### Task 3. Initialize and install dependencies

1. Run terraform init:

   ```bash
   terraform init
   ```

   <img width="1016" height="442" alt="image" src="https://github.com/user-attachments/assets/6b16cf2e-8f40-4df2-803b-d2fb40ac1901" />

2. Run the terraform apply command, which is used to apply the changes required to reach the desired state of the configuration:

   ```bash
   terraform apply -var="region=us-east4" -var="location=us-east4-b"
   ```
   <img width="1057" height="478" alt="image" src="https://github.com/user-attachments/assets/7e6bbf46-136c-4060-8f15-dea946eab3d6" />

### Verify resources created by Terraform

- In the console, navigate to `Navigation menu > Kubernetes Engine`.
- Click on `tf-gke-k8s` cluster and check its configuration.
- In the left panel, click `Gateways, Services & Ingress` and check the nginx service status.
- Click the `Endpoints IP address` to open the `Welcome to nginx! page` in a new browser tab.

  <img width="836" height="289" alt="image" src="https://github.com/user-attachments/assets/1451433b-452d-4750-88ea-c4b890e1fd59" />


