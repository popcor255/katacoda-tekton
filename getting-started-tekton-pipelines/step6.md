## Creating a Pipeline

A **Pipeline** defines an ordered series of **Tasks** that you want to execute
along with the corresponding inputs and outputs for each **Task**. You can specify whether the output of one
**Task** is used as an input for the next **Task** using the **from** property.
**Pipelines** offer the same variable substitution as **Tasks**.

Create this file: `touch tutorial-pipeline.yaml`{{execute}}

Go to the **tutorial-pipeline.yaml**  editor and copy and paste this yaml:

```yaml
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: tutorial-pipeline
spec:
  resources:
    - name: source
      type: git
    - name: image
      type: image
  tasks:
    - name: build-and-push-to-dockerhub
      taskRef:
        name: build-and-push-docker-image-from-git
      params:
        - name: DOCKERFILE
          value: ./Dockerfile
          #configure: this will change the default value of your params in your tasks
      resources:
        inputs:
          - name: source
            resource: git
        outputs:
          - name: image
            resource: image
    - name: deploy-app
      taskRef:
        name: deploy-using-kubectl
      resources:
        inputs:
          - name: image
            resource: image
            from:
              - build-and-push-to-dockerhub
```

Once you saved this file you can apply it to the Kubernetes Cluster:
`kubectl apply -f tutorial-pipeline.yaml`{{execute}}


The above **Pipeline** is referencing a **Task** called **deploy-using-kubectl** defined as follows:

Create this file: `touch deploy-using-kubectl-task.yaml`{{execute}}

Go to the **deploy-using-kubectl-task.yaml**  editor and copy and paste this yaml:

```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: deploy-using-kubectl
spec:
  params:
    - name: URL
      type: string
      description: The location of the infra/yaml that will be applied
      default: "https://raw.githubusercontent.com/popcor255/Kubernetes-Objects/master/pods/"
    - name: PATH
      type: string
      description: This is the name of the file
      default: my-pod.yaml
  resources:
    inputs:
      - name: image
        type: image
  steps:
    - name: fetch-yaml
      image: ellerbrock/alpine-bash-curl-ssl
      command: ["/bin/bash"]
      args:
        - -c
        - |
          set -e
          curl $(params.URL)$(params.PATH) > $(params.PATH)
    - name: replace-image
      image: mikefarah/yq
      command: ["yq"]
      args:
        - "w"
        - "-i"
        - "$(params.PATH)"
        - "spec.containers[0].image"
        - "$(resources.inputs.image.url)"
    - name: run-kubectl
      image: lachlanevenson/k8s-kubectl
      command: ["kubectl"]
      args:
        - "apply"
        - "-f"
        - "$(params.PATH)"
```

Once you saved this file you can apply it to the Kubernetes Cluster:
`kubectl apply -f deploy-using-kubectl-task.yaml`{{execute}}

