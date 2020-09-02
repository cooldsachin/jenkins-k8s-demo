# jenkins-k8s-demo
A. Install Jenkins in K8s cluster
  
  1. Create a kubernetes cluster
  
  2. In K8s cluster, we need to create and apply 4 yaml files
     a. for persistent volume to store Jenkins data
     b. for creating deployment of Jenkins 
     c. for creating service to expose Jenkins to external world 
     d. for creating cluster role so that your service account binds to cluster role - Here, a "ClusterRole" named "service reader" is created, and specific permissions 
        (such as ["get", "watch", "list"] are assigned to specific resources.
  
  3. Run the following command to create Jenkins deployment on K8s -
     kubectl apply -f jenkins-k8s-volume.yaml
     kubectl apply -f jenkins-k8s-deployment.yaml
     kubectl apply -f jenkins-k8s-service.yaml
     kubectl apply -f jenkins-k8s-clusterbinding.yaml
     
  3. Run the following command to create a cluster role binding named "service reader pod".        
     Its "clusterrole" is "service reader", and its name is "default:default".
     The first "default" is the namespace, the second "default" is the name of the service account, which will be referred to in the subsequent deployment configuration file.          Here, because I didn't create a separate namespace for Jenkins, it uses the default namespace "default".
  
     kubectl create clusterrolebinding service-reader-pod --clusterrole=service-reader  --serviceaccount=default:default

  4. Login to Jenkins
     Once you done with yaml execution, the external ip  of one of the k8s node and node port 30000 need to put on browser such as http://<External-IP>:30000 and Jenkins login        page will open and asking for initial password which can be retrieved from
     kubctl logs <POD-NAME>
     At the bottom you will find password with pattern like - adsubjsbvcdbhcvfnhhfjkjfk
  
B. Configure CLoud (Kubernetes) in Jenkins  
  1. Now you are in Jenkins Dashboard, Go to manage Jenkins - Manage Nodes - Configure CLoud
  
  2. It will ask to install plugin for cloud template - go to manage plugin and install Kubernetes plugin.
  
  3. Once install go to configure cloud and configure k8s and its pod template. (Watch my Youtube video for configuration steps - https://www.youtube.com/watch?v=laAAzdw11dA)
  
C. Configure service account to authenticate Gcloud using Jenkins
   1. Create service account in gcloud with proper role permissions - (IAM -> Service account)
      
   2. create key in .json format and download it.
      gcloud iam service-accounts keys create --iam-account my-iam-account@somedomain.com key.json
      
   3. In Kubernetes, Import credentials as a Secret. Now that you have the service account key, you need a way to load it into your container.
      kubectl create secret generic registry-jenkins --from-file=key.json=PATH-TO-KEY-FILE.json
   
 D. To run Jenkins job using Kubernetes pod agent - Please check JenkinsFile and also you can watch video on how to run job using Kubernetes slave agent - https://www.youtube.com/watch?v=laAAzdw11dA
   
