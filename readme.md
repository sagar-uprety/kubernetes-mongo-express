
# Kuberentes-Mongo-Express

Deploying a sample Mongo-Express (Client) and MongoDB Stack in Kuberentes Cluster. We will do it in two ways:
-  Stateless Way
-  StatefulSets Way (Recommended)

## Get Started 
Clone the repository and navigate to the project root directory. Make sure you the following requirements:
- A running Kubernetes Cluster in cloud or Minikube (if testing locally)
- If you are using cloud provider, make sure to download the kubeconfig.yaml file and export it  your enviroment as:

```
export KUBECONFIG=path-to-your-kubeconfig-file
```


## 1.  Stateless Way 
We will use the following components:
- Deployment - MondoDB and MongoExpress Pod
- Internal Service - MongoDB
- External Service (NodePort) - Mongo Express
- Config Map - For Database URLs
- Secret - For storing DB User and Password
  
### Generate MongoDB Secrets
- AS, Kubernets Secrets are base64 encoded. We need to encode your credentials before applying to secret. 

We can use any 3rd party tool for Windows. For Mac/Linux, run the following command and paste the resulting values in mongodb-sercret.yaml file: 

```bash
echo "mongodb-root-username" | base64

echo "your mongodb-root-password" | base64
```

### Apply kubectl commands in the following order
```bash   
kubectl apply -f mongo-db-secret.yaml
kubectl apply -f mongo-db.yaml
kubectl apply -f mongo-db-configmap.yaml 
kubectl apply -f mongo-express.yaml
```
### View the resources status using these kubectl get commands
```bash
kubectl get pod
kubectl get pod -o wide
kubectl get service
kubectl get secret
kubectl get configmap
```

### (Minikube Users Only): Give a URL to external service in minikube
```bash
minikube service mongo-express-service
```
You will get a service IP address you can use to access the application. Hurray!
