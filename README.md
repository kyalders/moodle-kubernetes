# Welcome to Moodle on Minikube Deployment
This repo serves as a place to walk through a Moodle deployment using ArgoCD on your local Minikube installation for local development.

# Installation & Setup

## Minikube Install
> **Note:** For our setup we already have Docker Desktop installed and running.

### Install with Chocolatey w/ PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   
 ### Install  and start Minikube
    choco install minikube
    minikube config set driver docker
    minikube start --driver=docker

## ArgoCD Install

 1. Create Namespace for AgroCD
 `kubectl create ns argocd`
 2. Apply ArgoCD manifest installation file from ArgoCD github repository
 `kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.5.8/manifests/install.yaml`
 3. Verify the installation by getting all the objects in the ArgoCD namespace.
`kubectl get  all  -n argocd`
4. To access the web GUI of ArgoCD, we need to do a port forwarding
`kubectl port-forward svc/argocd-server -n argocd 8080:443`
5. Open the ArgoCD web page using https://localhost:8080. Username is admin. From your console use the following to get your password
`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo`
> **Note:** you may have to run the output of the first command and pipe it into the base64 portion of the second command into a linux terminal if using a windows pc. 

## Moodle Setup
1. Connect your Moodle Repo to ArgoCD.
2. Create kubernetes secrets.
   ` kubectl create secret generic mysql-root-pw --from-literal=mysql-root-password=<yourmysqlrootpassword> --namespace=moodle
kubectl create secret generic moodle-db-server --from-literal=moodle-db-host=<your moodle cluster dns name. ex: moodle-mysql.moodle.svc.cluster.local> --namespace=moodle
kubectl create secret generic moodle-db-uname --from-literal=moodle-db-username=<yourusername> --namespace=moodle
kubectl create secret generic moodle-db-pw --from-literal=moodle-db-password=<yourmoodlepassword> --namespace=moodle
kubectl create secret generic moodle-db --from-literal=moodle-db-name=<moodledatabasename> --namespace=moodle`
3. Create your application in ArgoCD.
4. Connect to database using dbeaver.
5. Create moodle database and moodledbuser.
6. Provide authorization for user to access db.
7. Connect to the minikube instance and change the permissions of the mysql and moodle directory (`chmod -R 660 <dirname>`)

# References & Inspirations

 - https://medium.com/@mehmetodabashi/installing-argocd-on-minikube-and-deploying-a-test-application-caa68ec55fbf
 - https://github.com/OdabasiMehmet/nginx-kubernetes/blob/master/nginx/service.yaml
 - https://github.com/harsh4870/Moodle-on-kubernetes/tree/master
