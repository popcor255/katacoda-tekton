### Configuring Resources and Credentials for Tasks

In the following example, you can see a **Task** definition with the **git** input and **image** output
introduced earlier. The arguments of the **Task** command support variable substitution so that
the **Task** definition is constant and the value of parameters can change during runtime.

You are going to create a task that will build a docker image from the source code.
The Task will take that image and push it to an image registry like Docker Hub.

You will need this task in the next step.

Create this file:
`touch build-and-push-docker-image-from-git-task.yaml`{{execute}}

Go to the **build-and-push-docker-image-from-git-task.yaml** editor and copy and paste this yaml:

```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: build-and-push-docker-image-from-git
spec:
  params:
    - name: BUILDER_IMAGE
      description: The location of the builder image
      default: quay.io/buildah/stable:v1.14.3
  resources:
    inputs:
      - name: source
        type: git
    outputs:
      - name: image
        type: image
  steps:
    - name: build-and-push
      image: $(inputs.params.BUILDER_IMAGE)
      workingDir: /workspace/source
      command: ["/bin/bash"]
      args:
        - -c
        - |
          set -e
          IMAGE_NAME="$(outputs.resources.image.url)"
          buildah bud --tls-verify="false" --layers -f "./Dockerfile" -t "$IMAGE_NAME:latest" .
          buildah push --tls-verify="false" "$IMAGE_NAME:latest" "docker://$IMAGE_NAME:latest"
      securityContext:
        privileged: true
      volumeMounts:
        - name: varlibcontainers
          mountPath: /var/lib/containers
  volumes:
    - name: varlibcontainers
      emptyDir: {}
```

Once you saved this file you can apply it to the Kubernetes Cluster:
`kubectl apply -f build-and-push-docker-image-from-git-task.yaml`{{execute}}

Before you can create your `TaskRun`, you must create a `secret` to push your image
to your desired image registry.

**Note** You can get your Docker access token at [https://hub.docker.com/settings/security)](https://hub.docker.com/settings/security).

```bash
kubectl create secret docker-registry regcred \
                    --docker-server=docker.io \
                    --docker-username=<your-name> \
                    --docker-password=<your-token> \
                    --docker-email=<your-email>
```

You must create a **ServiceAccount** that uses this **secret** so you can reference it in your **TaskRun**.

Create this file:
`touch tutorial-service-account.yaml`{{execute}}

Go to the **tutorial-service-account.yaml** editor and copy and paste this yaml:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tutorial-service
secrets:
  - name: regcred
```

Once you saved this file you can apply it to the Kubernetes Cluster:
`kubectl apply -f tutorial-service-account.yaml`{{execute}}
