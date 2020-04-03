## Creating and running a `Task`

A **Task** defines a series of **Steps** that run in a desired order and complete a set amount of build work. Every **Task** runs as a Pod on your Kubernetes cluster with each **Step** as its own container. For example, the following **Task** outputs "Hello World":

Open a vi editor and copy and paste this yaml:
`vi echo-hello-world-task.yaml`{{execute}}

```yaml
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: echo-hello-world
spec:
  steps:
    - name: echo
      image: ubuntu
      command:
        - echo
      args:
        - "Hello World"
```

Once you saved the task apply it to the Kubernetes Cluster:
`kubectl apply -f echo-hello-world-task.yaml`{{execute}}

To run this **Task**, instantiate it using a **TaskRun**:

Open a vi editor and copy and paste this yaml:
`vi echo-hello-world-task-run.yaml`{{execute}}

```yaml
apiVersion: tekton.dev/v1beta1
kind: TaskRun
metadata:
  name: echo-hello-world-task-run
spec:
  taskRef:
    name: echo-hello-world
```

Once you saved the task-run apply it to the Kubernetes Cluster:
`kubectl apply -f echo-hello-world-task-run.yaml`{{execute}}