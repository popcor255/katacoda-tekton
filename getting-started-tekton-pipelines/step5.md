### Running your Task

You are now ready to use the **Task** for another **TaskRun**!

A **TaskRun** binds the inputs and outputs to already defined **PipelineResources**, sets the **Service Account Name**
sets values for variable substitution parameters, and executes the **Steps** in the **Task**.

Create this file:
`touch build-and-push-docker-image-from-git-task-run.yaml`{{execute}}

Go to the **build-and-push-docker-image-from-git-task-run.yaml**  editor and copy and paste this yaml:

```yaml
apiVersion: tekton.dev/v1beta1
kind: TaskRun
metadata:
  name: build-and-push-docker-image-from-git-task-run
spec:
  serviceAccountName: tutorial-service
  taskRef:
    name: build-and-push-docker-image-from-git
  resources:
    inputs:
      - name: source
        resourceRef:
          name: hello-world-git
    outputs:
      - name: image
        resourceRef:
          name: hello-world-image
```

Once you saved this file you can apply it to the Kubernetes Cluster:
`kubectl apply -f build-and-push-docker-image-from-git-task-run.yaml`{{execute}}


### Tips

To examine the resources you've created so far, use the following command

`kubectl get tekton-pipelines`{{execute}}

Succeeded status indicates the Task has completed with no errors. You
can confirm that the output Docker image has been created in the location specified in the resource definition.

To view detailed information about the execution of your TaskRun, view the logs

`tkn taskrun logs build-and-push-docker-image-from-git-task-run`{{execute}}