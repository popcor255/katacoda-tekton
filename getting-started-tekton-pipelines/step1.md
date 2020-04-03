 # Getting Started with Tekton Pipelines

This guide helps you get started with Tekton Pipelines by walking you through a simple `Hello World` tutorial. The tutorial shows you how to:

- Create a **Task**	
- Create a **Pipeline** containing your **Tasks**
- Use a **TaskRun** to instantiate and execute a **Task** outside of a **Pipeline**
- Use a **PipelineRun** to instantiate and run a **Pipeline** containing your **Tasks**

## Installation

We already installed Minikube for you. Check that it is properly installed, by running the Minikube version command:

`minikube version`{{execute}}

Start the cluster, by running the Minikube start command:

`minikube start`{{execute}}

Install Tekton CLI:

`curl -LO https://github.com/tektoncd/cli/releases/download/v0.8.0/tektoncd-cli-0.8.0_Linux-64bit.deb && dpkg -i tektoncd-cli-0.8.0_Linux-64bit.deb` {{execute}}

Install Tekton Pipelines:

`kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml`{{execute}}

Great! You now have a running Kubernetes cluster with Tekton Pipelines.