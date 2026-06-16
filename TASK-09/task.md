### The Nautilus DevOps team is crafting jobs in the Kubernetes cluster. While they're developing actual scripts/commands, they're currently setting up templates and testing jobs with dummy commands. Please create a job template as per details given below:
#
Create a job named countdown-devops.

The spec template should be named countdown-devops (under metadata), and the container should be named container-countdown-devops

Utilize image ubuntu with latest tag (ensure to specify as ubuntu:latest), and set the restart policy to Never.

Execute the command sleep 5

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.
