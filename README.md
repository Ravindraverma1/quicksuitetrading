# MariaDB  Cluster on Kubernetes

This repository contains Kubernetes manifests to deploy a highly available MariaDB  cluster with 3 nodes. The setup includes persistent storage, configuration through ConfigMaps, and services for both internal and external access.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Components](#components)
- [Deployment Steps](#deployment-steps)
- [Accessing the Cluster](#accessing-the-cluster)
- [Monitoring the Cluster](#monitoring-the-cluster)

## Prerequisites

Before you begin, ensure you have the following:

- A Kubernetes cluster up and running
- `kubectl` command-line tool configured to interact with your cluster
- Sufficient permissions to create namespaces, StatefulSets, and services

## Components

The deployment consists of the following components:

1. **Namespace**: A dedicated namespace for isolating resources.
2. **Persistent Volumes (PVs)**: Three PVs for each MariaDB instance.
3. **Persistent Volume Claim (PVC)**: A claim to request storage.
4. **ConfigMap**: Configuration settings for the MariaDB  cluster.
5. **StatefulSet**: Manages the MariaDB instances ensuring stable network identities and storage.
6. **Headless Service**: Provides stable network identities for the StatefulSet.
7. **LoadBalancer Service**: Exposes the MariaDB cluster to external clients.

## Deployment Steps

```bash
kubectl apply -f namespace.yaml

kubectl apply -f pv-mariadb-0.yaml
kubectl apply -f pv-mariadb-1.yaml
kubectl apply -f pv-mariadb-2.yaml

kubectl apply -f pvc.yaml

kubectl apply -f configmap.yaml

kubectl apply -f statefulset.yaml

kubectl apply -f service-loadbalancer.yaml


##Accessing the Cluster

To access the MariaDB cluster, retrieve the external IP of the LoadBalancer service:

kubectl get svc mariadb-loadbalancer -n mariadb-cluster
Connect to the MariaDB cluster using the mysql client:

mysql -h <EXTERNAL-IP> -P 3306 -u user -p
Replace <EXTERNAL-IP> with the actual external IP address obtained from the previous command.

##Monitoring the Cluster

To check the status of the  cluster, log in to one of the MariaDB pods and use the following commands:


Log in to a MariaDB pod:


kubectl exec -it mariadb-0 -n mariadb-cluster -- /bin/bash
Access the MariaDB client:

mysql -u root -p

#Check cluster status:
SHOW STATUS LIKE 'wsrep_%';

Look for the following variables in the output:

wsrep_cluster_size: Number of nodes in the cluster
wsrep_incoming_addresses: Addresses of all nodes in the cluster
wsrep_cluster_status: Should be Primary
wsrep_connected: Should be ON
wsrep_local_state_comment: Should be Synced


#Files
namespace.yaml: Creates a namespace for the cluster.
pv-mariadb-0.yaml: Persistent volume for MariaDB instance 0.
pv-mariadb-1.yaml: Persistent volume for MariaDB instance 1.
pv-mariadb-2.yaml: Persistent volume for MariaDB instance 2.
pvc.yaml: Persistent volume claim for the MariaDB instances.
configmap.yaml: Configuration for the MariaDB  cluster.
service-headless.yaml: Headless service for the StatefulSet.
statefulset.yaml: StatefulSet for deploying MariaDB instances.
service-loadbalancer.yaml: LoadBalancer service for external access.


#Conclusion
This setup provides a highly available MariaDB  cluster on Kubernetes with persistent storage and external access. Follow the deployment steps to set up the cluster and monitor its status to ensure it is running correctly.


This `README.md` provides detailed instructions on how to deploy and manage your MariaDB  cluster on Kubernetes, including prerequisites, deployment steps, accessing the cluster, and monitoring its status.
