### Configuring Resources and Credentials for Tasks

In the following example, you can see a `Task` definition with the `git` input and `image` output
introduced earlier. The arguments of the `Task` command support variable substitution so that
the `Task` definition is constant and the value of parameters can change during runtime.

You will need this task in the next step.

Create this file:
`build-and-push-docker-image-from-git-task-run.yaml`{{execute}}

Go to the **echo-hello-world-task.yaml**  editor and copy and paste this yaml:


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
    - name: DOCKERFILE
      description: Path to the Dockerfile to build.
      default: ./Dockerfile
    - name: CONTEXT
      description: Path to the directory to use as context.
      default: .
    - name: TLSVERIFY
      description: Verify the TLS on the registry endpoint (for push/pull to a non-TLS registry)
      default: "false"
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
          SHORT_GIT_HASH="$(cat .git/FETCH_HEAD | awk '{print substr($1,0,7)}')"
          IMAGE_NAME="$(outputs.resources.image.url)"
          NEW_IMAGE_ID="$IMAGE_NAME:$SHORT_GIT_HASH"
          NEW_IMAGE_ID="$(echo $NEW_IMAGE_ID | sed s/\$NAMESPACE/$NAMESPACE/)"
          echo "Building Image $NEW_IMAGE_ID"
          buildah bud --tls-verify="$(inputs.params.TLSVERIFY)" --layers -f "$(inputs.params.DOCKERFILE)" -t "$NEW_IMAGE_ID"  -t "$IMAGE_NAME:latest" "$(inputs.params.CONTEXT)"
          echo "Pushing Image $NEW_IMAGE_ID"
          buildah push --tls-verify="$(inputs.params.TLSVERIFY)" "$NEW_IMAGE_ID" "docker://$NEW_IMAGE_ID"
          buildah push --tls-verify="$(inputs.params.TLSVERIFY)" "$IMAGE_NAME:latest" "docker://$IMAGE_NAME:latest"
      securityContext:
        privileged: true
      volumeMounts:
        - name: varlibcontainers
          mountPath: /var/lib/containers
      env:
        - name: NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
  volumes:
    - name: varlibcontainers
      emptyDir: {}
```

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

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tutorial-service
secrets:
  - name: regcred
```