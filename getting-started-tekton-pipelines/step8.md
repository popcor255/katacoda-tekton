### Creating a PipelineRun

You are now ready to create your first **PipelineRun**!

To run your `Pipeline`, instantiate it with a [`PipelineRun`](pipelineruns.md) as follows:

Create this file:
`tutorial-pipeline-run-1.yaml`{{execute}}

Go to the **tutorial-pipeline-run-1.yaml**  editor and copy and paste this yaml:

```yaml
apiVersion: tekton.dev/v1beta1
kind: PipelineRun
metadata:
  name: tutorial-pipeline-run-1
spec:
  serviceAccountName: tutorial-service
  pipelineRef:
    name: tutorial-pipeline
  resources:
    - name: source
      resourceRef:
        name: hello-world-git
    - name: image
      resourceRef:
        name: hello-world-image
```

Once you saved this file you can apply it to the Kubernetes Cluster:
`kubectl apply -f  tutorial-pipeline-run-1.yaml`{{execute}}

Run the following command to ensure that the pipeline run succeeded:
`tkn pipelinerun describe tutorial-pipeline-run-1`{{execute}}

🎉 Congratulations! You ran your first Pipelinerun! 🎉 

