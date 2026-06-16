### The Nautilus DevOps team has already deployed a ReplicaSet to host an application that requires a highly available infrastructure. Your task is to expose the application running in the existing ReplicaSet by creating a Kubernetes NodePort Service.
#
Follow the specifications below to create the Service and ensure the application pods are accessible:

A ReplicaSet named nginx-replicaset is already running in the cluster.

The pods managed by the ReplicaSet use the following labels:

Assign labels app as nginx_app, and type as front-end.<br>
Create a NodePort Service named nginx-service to expose the application.<br>
Set the NodePort to 30080.<br>
Expose port 80 of the application.<br>

Note: Do not delete or modify the configuration of the deployed ReplicaSet application.
