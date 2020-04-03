 # Getting Started with Tekton Pipelines

This guide helps you get started with Tekton Pipelines by walking you through a simple `Hello World` tutorial. The tutorial shows you how to:

- Create a `Task`	
- Create a `Pipeline` containing your `Tasks`	
- Use a `TaskRun` to instantiate and execute a `Task` outside of a `Pipeline`	
- Use a `PipelineRun` to instantiate and run a `Pipeline` containing your `Tasks`

## Installation

We already installed minikube for you. Check that it is properly installed, by running the minikube version command:

minikube version{{execute}}

Start the cluster, by running the minikube start command:

minikube start{{execute}}

Install Tekton Pipelines onto your cluster:

kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml

Great! You now have a running Kubernetes cluster with Tekton Pipelines.