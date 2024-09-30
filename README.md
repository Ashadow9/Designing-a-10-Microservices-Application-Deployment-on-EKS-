# Designing-a-10-Microservices-Application-Deployment-on-EKS


![](https://miro.medium.com/v2/resize:fit:736/1*BR95t3O5WYZykgPvJnv2HA.png)

In the realm of cloud computing, deploying a complex application with precision and efficiency is a paramount challenge. Amazon Web Services (AWS) offers Elastic Kubernetes Service (EKS), a fully managed Kubernetes service that simplifies the deployment, management, and scaling of containerized applications. In this blog post, we’ll delve into the intricacies of orchestrating a 10-tier application on AWS EKS.

# **Designing the 10-Microservices Application**

## **1\. Presentation Tier 🖥️:**

* Host your frontend application here.
    
* Utilize AWS Elastic Load Balancer (ELB) for distributing incoming traffic.
    

## **2\. Web Server Tier 🌐:**

* Deploy web servers using containers managed by Kubernetes pods.
    
* Leverage Kubernetes Services for load balancing within the web server tier.
    

## **3\. Application Tier 🚀:**

* Run application logic in containers using Kubernetes Deployments.
    
* Implement auto-scaling based on demand to ensure optimal performance.
    

## **4\. API Gateway Tier 🚪:**

* Use Amazon API Gateway to create, publish, and manage APIs.
    
* Enable authentication and throttling for secure and controlled access.
    

## **5\. Business Logic Tier 💼:**

* Containerize business logic components and deploy using Kubernetes.
    
* Utilize AWS Lambda for serverless execution of specific functions.
    

## **6\. Message Queue Tier 📬:**

* Implement message queues like Amazon Simple Queue Service (SQS) for asynchronous communication between components.
    
* Ensure reliability and scalability of message processing.
    

## **7\. Data Access Tier 📊:**

* Manage data storage and retrieval using Amazon Relational Database Service (RDS) or Amazon DynamoDB.
    
* Implement read replicas for scalability and fault tolerance.
    

## **8\. Caching Tier 🚀:**

* Utilize Amazon ElastiCache for in-memory caching to enhance application performance.
    
* Configure caching strategies based on the application’s needs.
    

## **9\. Storage Tier 🗄️:**

* Use Amazon S3 for scalable and durable object storage.
    
* Implement data lifecycle policies to manage data efficiently.
    

## **10\. Infrastructure Tier 🌐:**

* Leverage Amazon EKS to manage and orchestrate containers effectively.
    
* Utilize AWS Identity and Access Management (IAM) for secure access control.
    

![](https://miro.medium.com/v2/resize:fit:736/0*lBtxr2c2uMcXmz9y.png)

# **Step-by-Step Implementation :-**

1. **Create an AWS EC2 instance**
    

![image](https://github.com/user-attachments/assets/fbcaf36c-e714-41fb-b08d-a31aa6a288f5)


**2\. Create a user and give the following permissions :-**

![image](https://github.com/user-attachments/assets/b42730d9-ab64-4869-ad8a-cc1598b46afc)


Connect with the EC2 instance you can use ssh or Putty or mobaXterm.

**3\. After connect install aws ctl on your server to give your credentials**  
[https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
![image](https://github.com/user-attachments/assets/a0c205b6-5b03-409c-88b3-7f10256fba69)


**4\. After connecting install Jenkins on your server**  
[https://www.jenkins.io/doc/book/installing/linux/#debianubuntu](https://www.jenkins.io/doc/book/installing/linux/#debianubuntu)

**5\. Now install kubctl on the linux**  
[https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux)

**6\. Install eksctl**  
[https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/setting-up-eksctl.html](https://docs.aws.amazon.com/emr/latest/EMR-on-EKS-DevelopmentGuide/setting-up-eksctl.html)

**7\. Install docker and give permission**

```bash
sudo apt-get install docker.io
sudo usermod -aG docker ubuntu
sudo newgrp docker
```

**8\. Install sonarqube from docker image**

```bash
docker run -d -p 9000:9000 sonarqube:lts-community
```
![image](https://github.com/user-attachments/assets/c84efa52-21a6-4181-be9a-1684d4ee7129)

**9\. Install EKS**

```bash
eksctl create cluster --name=my-eks2 \
        --region=ap-south-1 \
        --zones=ap-south-1a,ap-south-1b \
        --without-nodegroup

// after the above setup complete run this
eksctl utils associate-iam-oidc-provider \
 --region ap-south-1 \
 --cluster my-eks2 \
 --approve 

eksctl create nodegroup --cluster=my-eks2 \
   --region=ap-south-1 \
   --name=node2 \
   --node-type=t3.medium \
   --nodes=3 \
   --nodes-min=2 \
   --nodes-max=3 \
   --node-volume-size=20 \
   --ssh-public-key=10-tier-key \
   --managed \
   --asg-access \
   --external-dns-access \
   --full-ecr-access \
   --appmesh-access \
   --alb-ingress-access
```
![image](https://github.com/user-attachments/assets/0c3830de-84e4-4629-b503-e96c7a3ba476)

![image](https://github.com/user-attachments/assets/dad8ffa0-3281-4a3f-b313-e02cb7bbf7fc)


Installing some pugins in Jenkins as,

```yaml
sonarqube scanner
docker
docker pipeline
docker common
cloudbee docker build and publish
kubernetes
kubernetes cli
```

**Configure Sonar Server in Manage Jenkins**

Grab the Public IP Address of your EC2 Instance, Sonarqube works on Port 9000 , sp &lt;Public IP&gt;:9000. Goto your Sonarqube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token

![image](https://github.com/user-attachments/assets/d4a86de6-c1a6-43b5-bf5d-3ec11c328741)



Click on Update Token

![image](https://github.com/user-attachments/assets/f4c675c7-d716-4159-92dd-f97bac85e4bd)


**Copy this Token**

Go to Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this

![image](https://github.com/user-attachments/assets/838a8aac-ce8b-40c2-96fd-644e5abab8f6)

Now, goto Dashboard → Manage Jenkins → Configure System

![image](https://github.com/user-attachments/assets/42755544-95d8-43f2-bdb7-c04d77bd5290)

Click on Apply and Save

**Configure System option** is used in Jenkins to configure different server

**Global Tool Configuration** is used to configure different tools that we install using Plugins

We will install sonar-scanner in tools.

![image](https://github.com/user-attachments/assets/90f94feb-86f4-4597-8333-034eb8a0576d)

**11\. Go to EKS service and add all traffic to its SG**

![image](https://github.com/user-attachments/assets/f79351ae-d55e-429e-99db-0432d68edbac)

![image](https://github.com/user-attachments/assets/ee79466a-d6d8-4a61-a7c4-989d45ecb557)

![image](https://github.com/user-attachments/assets/78a8b45b-bd6e-4fbf-914a-93dd3f903e19)


**12\. Create a Service Account and role and Assign that role create a secret service account, and generate a token**

Creating Service Account

1\. create namespace:-

![image](https://github.com/user-attachments/assets/4446fcb0-85b5-4020-9c1d-cb94b9288632)



2\. Create sa.yml file and add the follow code

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
```

```yaml
kubectl apply -f sa.yaml
```
![image](https://github.com/user-attachments/assets/f30ac298-878e-449b-9e7c-154ec43ef6b9)

3\. Now we need to create role

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
 name: app-role
 namespace: webapps
rules:
 - apiGroups:
 - ""
 - apps
 - autoscaling
 - batch
 - extensions
 - policy
 - rbac.authorization.k8s.io
 resources:
 - pods
 - configmaps
 - deployments
 - daemonsets
 - componentstatuses
 - events
 - endpoints
 - horizontalpodautoscalers
 - ingress
 - jobs
 - limitranges
 - namespaces
 - nodes
 - pods
 - persistentvolumes
 - persistentvolumeclaims
 - resourcequotas
 - replicasets
 - replicationcontrollers
 - serviceaccounts
 - services
 verbs:
 - get
 - list
 - watch
 - create
 - update
 - patch
 - delete
```

![image](https://github.com/user-attachments/assets/0feb9f87-a3e1-4a22-955a-a6cdec9b3604)


4\. now assigning the role to the service account

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role
subjects:
  - namespace: webapps
    kind: ServiceAccount
    name: jenkins
```

![image](https://github.com/user-attachments/assets/85fcf356-0d5a-41de-88d8-de523f560252)


5\. now creating a token for service account

```yaml
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins
```
![image](https://github.com/user-attachments/assets/da7b80e8-2c9a-4be2-94b4-31f546628eb6)


Go to Jenkins and add a pipeline

![](https://miro.medium.com/v2/resize:fit:736/0*EcuT0dEzhK1ccOV0.png)

Lets goto our Pipeline and add below Script.

```groovy
pipeline {
    agent any
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('git checkout') {
            steps {
                git branch: 'latest', url: 'https://github.com/SushantOps/10-Tier-MicroService-Appliction.git'
            }
        }
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=10-Tier -Dsonar.ProjectName=10-Tier -Dsonar.java.binaries=. '''   
                }
                    
            }
                
        }
        stage('adservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/adservice') {
                            sh 'docker build -t sushantkapare1717/adservice:latest .'
                            sh "docker push sushantkapare1717/adservice:latest"
                            sh "docker rmi sushantkapare1717/adservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('cartservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/cartservice/src/') {
                            sh 'docker build -t sushantkapare1717/cartservice:latest .'
                            sh "docker push sushantkapare1717/cartservice:latest"
                            sh "docker rmi sushantkapare1717/cartservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('checkoutservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/checkoutservice/') {
                            sh 'docker build -t sushantkapare1717/checkoutservice:latest .'
                            sh "docker push sushantkapare1717/checkoutservice:latest"
                            sh "docker rmi sushantkapare1717/checkoutservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('currencyservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/currencyservice/') {
                            sh 'docker build -t sushantkapare1717/currencyservice:latest .'
                            sh "docker push sushantkapare1717/currencyservice:latest"
                            sh "docker rmi sushantkapare1717/currencyservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('emailservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/emailservice/') {
                            sh 'docker build -t sushantkapare1717/emailservice:latest .'
                            sh "docker push sushantkapare1717/emailservice:latest"
                            sh "docker rmi sushantkapare1717/emailservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('frontend'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/frontend/') {
                            sh 'docker build -t sushantkapare1717/frontend:latest .'
                            sh "docker push sushantkapare1717/frontend:latest"
                            sh "docker rmi sushantkapare1717/frontend:latest"
                        }
                    }
            }
            }
        }
    
        stage('loadgenerator'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/loadgenerator/') {
                            sh 'docker build -t sushantkapare1717/loadgenerator:latest .'
                            sh "docker push sushantkapare1717/loadgenerator:latest"
                            sh "docker rmi sushantkapare1717/loadgenerator:latest"
                        }
                    }
            }
            }
        }
    
        stage('paymentservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/paymentservice/') {
                            sh 'docker build -t sushantkapare1717/paymentservice:latest .'
                            sh "docker push sushantkapare1717/paymentservice:latest"
                            sh "docker rmi sushantkapare1717/paymentservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('productcatalogservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/productcatalogservice/') {
                            sh 'docker build -t sushantkapare1717/productcatalogservice:latest .'
                            sh "docker push sushantkapare1717/productcatalogservice:latest"
                            sh "docker rmi sushantkapare1717/productcatalogservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('recommendationservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/recommendationservice/') {
                            sh 'docker build -t sushantkapare1717/recommendationservice:latest .'
                            sh "docker push sushantkapare1717/recommendationservice:latest"
                            sh "docker rmi sushantkapare1717/recommendationservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('shippingservice'){
            steps{
             script{
              withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        dir('/var/lib/jenkins/workspace/10-Tier/src/shippingservice/') {
                            sh 'docker build -t sushantkapare1717/shippingservice:latest .'
                            sh "docker push sushantkapare1717/shippingservice:latest"
                            sh "docker rmi sushantkapare1717/shippingservice:latest"
                        }
                    }
            }
            }
        }
        
        stage('K8-Deploy'){
            steps{
                withKubeConfig(caCertificate: '', clusterName: 'my-eks2', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://EBCE08CF45C3AA5A574E126370E5D4FC.gr7.ap-south-1.eks.amazonaws.com') {
                    sh 'kubectl apply -f deployment-service.yml'
                    sh 'kubectl get pods'
                    sh 'kubectl get svc'
                }
            }
        }
}
}
```

How to get the key

```yaml
kubectl -n examplens describe secret mysecretname
```

Now run the pipeline

After that we run that command

```yaml
kubectl get pods -n webapps
```

![](https://miro.medium.com/v2/resize:fit:736/0*ZQKaW4Pfn340JyXr.png)

![](https://miro.medium.com/v2/resize:fit:736/1*sYrGLLwBtA9t3gSIQG9VRA.png)

![](https://miro.medium.com/v2/resize:fit:736/1*HM2SUQOIjgQ-YmCCZr7mSQ.png)

**Terminate the AWS EC2 Instance**

**Lastly, do not forget to terminate the AWS EC2 Instance.**

### If you like this article, please share with others. ❤️
