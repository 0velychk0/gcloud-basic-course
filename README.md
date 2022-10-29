# Gcloud CLI(Command Line Interface)
https://cloud.google.com/sdk/gcloud

## list the active account name
    gcloud auth list
```
Credentialed Accounts
ACTIVE: *
ACCOUNT: student-01-a00299cf42a4@qwiklabs.net
To set the active account, run:
    $ gcloud config set account `ACCOUNT`
```

## list the project ID
    gcloud config list project
```
[core]
project = qwiklabs-gcp-02-80d74d58b68a
Your active configuration is: [cloudshell-10666]
````

## To create a new virtual machine instance from the command line:
    gcloud compute instances create gcelab2 --machine-type e2-medium --zone us-east1-b
```
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-02-80d74d58b68a/zones/us-east1-b/instances/gcelab2].
NAME: gcelab2
ZONE: us-east1-b
MACHINE_TYPE: e2-medium
PREEMPTIBLE:
INTERNAL_IP: 10.142.0.3
EXTERNAL_IP: 35.243.244.30
STATUS: RUNNING
```

## use SSH to connect to your instance via gcloud. Make sure to add your zone, or omit the --zone flag if you've set the option globally:
    gcloud compute ssh gcelab2 --zone us-east1-b
```
Linux gcelab2 5.10.0-18-cloud-amd64 #1 SMP Debian 5.10.140-1 (2022-09-02) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sat Oct 29 11:25:59 2022 from 34.91.235.62
```

## Install nginx on VM machine:
    sudo apt-get update
    sudo apt-get install -y nginx
    ps auwx | grep nginx

## Set project region to us-central1
     gcloud config set compute/region us-central1
```Updated property [compute/region].```

## To view the project region setting, run the following command:
    gcloud config get-value compute/region
```
Your active configuration is: [cloudshell-15275]
us-central1
```

## Run the following gcloud command, to view the project id for your project:
    gcloud config get-value project
    
## Run the following gcloud command to view details about the project:
    gcloud compute project-info describe --project $(gcloud config get-value project)

## Setting environment variables
```
$ export PROJECT_ID=$(gcloud config get-value project)
Your active configuration is: [cloudshell-15275]

$ export ZONE=$(gcloud config get-value compute/zone)
Your active configuration is: [cloudshell-15275]_

$ echo -e "PROJECT ID: $PROJECT_ID\nZONE: $ZONE"
PROJECT ID: qwiklabs-gcp-01-c7bd54e4fecf
ZONE: us-central1-b
```

## Creating a virtual machine with the gcloud tool
    gcloud compute instances create gcelab2 --machine-type e2-medium --zone $ZONE
```
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-01-c7bd54e4fecf/zones/us-central1-b/instances/gcelab2].
NAME: gcelab2
ZONE: us-central1-b
MACHINE_TYPE: e2-medium
PREEMPTIBLE:
INTERNAL_IP: 10.128.0.2
EXTERNAL_IP: 34.27.184.158
STATUS: RUNNING
```
List the compute instance available in the project:
    gcloud compute instances list
```
NAME: gcelab2
ZONE: us-central1-b
MACHINE_TYPE: e2-medium
PREEMPTIBLE:
INTERNAL_IP: 10.128.0.2
EXTERNAL_IP: 34.27.184.158
STATUS: RUNNING
```

## List the compute instance available in the project with filter:
    gcloud compute instances list --filter="name=('gcelab2')"
```
NAME: gcelab2
ZONE: us-central1-b
MACHINE_TYPE: e2-medium
PREEMPTIBLE:
INTERNAL_IP: 10.128.0.2
EXTERNAL_IP: 34.27.184.158
STATUS: RUNNING
```

## List the firewall-rules
    gcloud compute firewall-rules list
    
## List the Firewall rules for the default network:
    gcloud compute firewall-rules list --filter="network='default'"


## To connect to your VM with SSH, run the following command:
    gcloud compute ssh gcelab2 --zone $ZONE

## To get communication working we need to:
### Add a tag to the virtual machine:
    gcloud compute instances add-tags gcelab2 --tags http-server,https-server
```
Updated [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-01-c7bd54e4fecf/zones/us-central1-b/instances/gcelab2].
```

### Update the firewall rule to allow:
    gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
```
Creating firewall...working..Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-01-c7bd54e4fecf/global/firewalls/default-allow-http].
Creating firewall...done.
NAME: default-allow-http
NETWORK: default
DIRECTION: INGRESS
PRIORITY: 1000
ALLOW: tcp:80
DENY:
DISABLED: False
```

### Check list of rules:
    gcloud compute firewall-rules list --filter=ALLOW:'80'
```
NAME: default-allow-http
NETWORK: default
DIRECTION: INGRESS
PRIORITY: 1000
ALLOW: tcp:80
DENY:
DISABLED: False

To show all fields of the firewall, please show in JSON format: --format=json
To show all fields in table format, please see the examples in --help.
```

### Verify communication is possible for http to the virtual machine:
    curl http://$(gcloud compute instances list --filter=name:gcelab2 --format='value(EXTERNAL_IP)')
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
```



## View the available logs on the system:
    gcloud logging logs list
```
NAME: projects/qwiklabs-gcp-01-c7bd54e4fecf/logs/GCEGuestAgent
NAME: projects/qwiklabs-gcp-01-c7bd54e4fecf/logs/OSConfigAgent
NAME: projects/qwiklabs-gcp-01-c7bd54e4fecf/logs/cloudaudit.googleapis.com%2Factivity
NAME: projects/qwiklabs-gcp-01-c7bd54e4fecf/logs/cloudaudit.googleapis.com%2Fdata_access
NAME: projects/qwiklabs-gcp-01-c7bd54e4fecf/logs/cloudaudit.googleapis.com%2Fsystem_event
NAME: projects/qwiklabs-gcp-01-c7bd54e4fecf/logs/compute.googleapis.com%2Fshielded_vm_integrity
```

## View the logs that relate to compute resources:
    gcloud logging logs list --filter="compute"

## Read the logs related to the resource type of gce_instance:
     gcloud logging read "resource.type=gce_instance" --limit 5

## Read the logs for a specific virtual machine:
    gcloud logging read "resource.type=gce_instance AND labels.instance_name='gcelab2'" --limit 5


## Google Container Engine (GKE)
### Create a GKE cluster
    gcloud container clusters create --machine-type=e2-medium --zone=us-west1-a lab-cluster 

### Get authentication credentials for the cluster
    gcloud container clusters get-credentials lab-cluster 

### Deploy an application to the cluster
    kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0

### To create a Kubernetes Service, which is a Kubernetes resource that lets you expose your application to external traffic, run the following kubectl expose command:
    kubectl expose deployment hello-server --type=LoadBalancer --port 8080

### To inspect the hello-server Service, run kubectl get:
    kubectl get service
NAME           TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)          AGE
hello-server   LoadBalancer   10.16.10.156   34.168.145.188   8080:32544/TCP   87s
kubernetes     ClusterIP      10.16.0.1      <none>           443/TCP          6m38s

### To view the application from your web browser opn ip address or curl:
    curl http://34.168.145.188:8080/
```
Hello, world!
Version: 1.0.0
Hostname: hello-server-76d47868b4-xfbnr
```

### Deleting the cluster
    gcloud container clusters delete lab-cluster 



