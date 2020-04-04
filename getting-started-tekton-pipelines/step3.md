### Specifying Task inputs and outputs

In more complex scenarios, a **Task** requires you to define inputs and outputs. For example, a
**Task** could fetch source code with a Dockerfile and build a Docker image from it.

Use one or more **PipelineResources** to define the artifacts you want to pass in
and out of your **Task**. The following are examples of the most commonly needed resources.

The **git resource** specifies a git repository with
a specific revision from which the **Task** will pull the source code:

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

The **image resource** specifies the repository to which the image built by the **Task** will be pushed:


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