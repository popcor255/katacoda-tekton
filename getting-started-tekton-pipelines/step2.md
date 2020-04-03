## Creating and running a `Task`

A **Task** defines a series of **Steps** that run in a desired order and complete a set amount of build work. Every **Task** runs as a Pod on your Kubernetes cluster with each **Step** as its own container. For example, the following **Task** outputs "Hello World":

```bash
cat <<EOF | kubectl -f apply -
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
EOF
```

To run this **Task**, instantiate it using a **TaskRun**:

```bash
cat <<EOF | kubectl -f apply -
apiVersion: tekton.dev/v1beta1
kind: TaskRun
metadata:
  name: echo-hello-world-task-run
spec:
  taskRef:
    name: echo-hello-world
EOF
```