### Configuring Pipeline execution credentials

The **run-kubectl** step in the above example requires additional permissions. You must grant those
permissions to your **ServiceAccount**.

First, create a new role called **tutorial-role**:

`kubectl create clusterrole tutorial-role --verb=* --resource=pods,deployments,deployments.apps`{{execute}}

Next, assign this new role to your **ServiceAccount**:

`kubectl create clusterrolebinding tutorial-binding --clusterrole=tutorial-role --serviceaccount=default:tutorial-service`{{execute}}

To run your **Pipeline**, instantiate it with a **PipelineRun** as follows: