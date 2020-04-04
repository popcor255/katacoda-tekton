### Specifying Task inputs and outputs

In more complex scenarios, a **Task** requires you to define inputs and outputs. For example, a
**Task** could fetch source code with a Dockerfile and build a Docker image from it.

Use one or more **PipelineResources** to define the artifacts you want to pass in
and out of your **Task**. The following are examples of the most commonly needed resources.

The **git resource** specifies a git repository with
a specific revision from which the **Task** will pull the source code.

The **image resource** specifies the repository to which the image built by the **Task** will be pushed.

Create these files in the editor and apply them with kubectl.

Make sure to change **DOCKER_HUB_USERNAME** with your username.

Create this file:
`touch hello-world-git.yaml`{{execute}}

Go to the **hello-world-git.yaml**  editor and copy and paste this yaml:

```yaml
apiVersion: tekton.dev/v1alpha1
kind: PipelineResource
metadata:
  name: hello-world-git
spec:
  type: git
  params:
    - name: revision
      value: master
    - name: url
      value: https://github.com/popcor255/python-flask-docker-hello-world
```

Once you saved this file you can apply it to the Kubernetes Cluster:
`kubectl apply -f hello-world-git.yaml`{{execute}}


Create this file:
`touch hello-world-image.yaml`{{execute}}

Go to the **hello-world-image.yaml**  editor and copy and paste this yaml:

```yaml
apiVersion: tekton.dev/v1alpha1
kind: PipelineResource
metadata:
  name: hello-world-image
spec:
  type: image
  params:
    - name: url
      value: docker.io/DOCKER_HUB_USERNAME/hello-world
```

Once you saved this file you can apply it to the Kubernetes Cluster:
`kubectl apply -f hello-world-image.yaml`{{execute}}

