title: Kubenetes Hello World in Localhost Environment
date: 2016-09-27
categories:
- Kubenetes
tags:
- docker
- coreos
- vagrant
---

Today we will use vagarant to start a multi-node Kubenetes environment which is provided by CoreOS. Then deploy a Python Flask demo app to the cluster.

# 1. Setup Kubenetes Cluster

Reference the document written by CoreOS https://coreos.com/kubernetes/docs/latest/kubernetes-on-vagrant.html The only change I did is setting the worker count equal to 2 in `config.rb` file.

```bash
$work_count=2
```

After the cluster is ready, we can use some commands to check the cluter's status and information, such as:

```bash
➜  vagrant git:(master) ✗ kubectl cluster-info
Kubernetes master is running at https://172.17.4.101:443

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
➜  vagrant git:(master) ✗ kubectl get nodes
NAME           STATUS                     AGE
172.17.4.101   Ready,SchedulingDisabled   3d
172.17.4.201   Ready                      3d
172.17.4.202   Ready                      3d
➜  vagrant git:(master) ✗
```

# 2. Create a Python Flask app container

## 2.1 Create Flask app

Create a `flask-demo.py` file in folder `hellworld/`, and the Python code in the file is:

```bash
➜  hellworld git:(master) ✗ more flask-demo.py
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
```

Run the app in your localhost by:

```bash
$ python flask-demo.py
```

You should see "Hello World!" when you type URL http://127.0.0.1:5000 in your localhost browser. Next we will package our app to docker container.

## 2.2 Create Docker Container Image

Create another file called `Dockerfile` in the same folder:

```bash
➜  hellworld git:(master) ✗ more Dockerfile
FROM python:2.7
MAINTAINER Peng Xiao <xiaoquwl@gmail.com>

RUN apt-get update
RUN apt-get install -y --no-install-recommends python-pip
EXPOSE 5000
RUN pip install flask
COPY flask-demo.py .
CMD python flask-demo.py
```

We will build a Docker image from this Docker file through `docker build` command.

```bash
docker build -t $DOCKER_HUB_ID/flask-demo:v1 .
```

You can use your own tag following by your docker hub ID. (You can also use other Container Registry like Google) After the building finished, you can use `docker images` to see what docker images do we have.

```bash
➜  hellworld git:(master) ✗ docker images
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
xiaopeng163/flask-demo   v1                  98b1439d4a14        20 hours ago        699.9 MB
```

## 2.3 Test the Docker Image

We can create a docker container and start it from `docker run` command.

```bash
$ docker run -d -p 5000:5000 --name helloworld xiaopeng163/flask-demo:v1
```

Then you can use `wget` or `curl` to test.

```bash
$ curl 127.0.0.1:5000
```

You will get the result `Hello World!`

## 2.4 Commit and Push the Container Image to DockHub

After stop the container we can push our images to docker hub through `docker push` command.

```bash
docker stop helloworld
docker push xiaopeng163/flask-demo:v1
```

# 3. Deploy app to Kubenetes Cluster

## 3.1 Create a deployment

Let’s run our flask app on Kubernetes cluster with the kubectl run command. The run command creates a new deployment. We need to provide the deployment name and app image location (include the full repository url for images hosted outside Docker hub). We want to run the app on a specific port so we add the --port parameter:

```bash
$ kubectl run flask-demo --image=xiaopeng163/flask-demo:v1 --port=
deployment "flask-demo" created
```

As shown in the output, the `kubectl run` created a Deployment object. Deployments are the recommended way for managing creation and scaling of pods. In this example, a new deployment manages a single pod replica running the flask-demo:v1 image. To view the Deployment we just created run:

```bash
➜  vagrant git:(master) ✗ kubectl get deployments
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
flask-demo   1         1         1            1           5h
```

To see the pod which was managed by the deployment:

```bash
✗ kubectl get pods
NAME                        READY     STATUS    RESTARTS   AGE
flask-demo-80854800-4t8wm   1/1       Running   0          7h
```

We can get more information about this pod through the following commands: for example, we can know which node the pod is located.

```bash
➜  vagrant git:(master) ✗ kubectl get pods flask-demo-80854800-4t8wm -o wide
NAME                        READY     STATUS    RESTARTS   AGE       IP          NODE
flask-demo-80854800-4t8wm   1/1       Running   0          1m        10.2.98.2   172.17.4.202
```

```bash
kubectl describe pods flask-demo-80854800-4t8wm
```

## 3.2 Check the Falsk app

We can access the exactly node and check the flask-demo container's running status:

 - using `vagrant ssh nodename` to access the node
 - using `docker ps` to get the flask demo container ID
 - using  `docker exec` to get into the container and check flask process


```bash
core@w1 ~ $ docker exec -it 66d59c4f0874 bash
root@flask-demo-80854800-4t8wm:/# ps -ef | grep python
root         1     0  0 01:11 ?        00:00:00 /bin/sh -c python flask-demo.py
root         5     1  0 01:11 ?        00:00:04 python flask-demo.py
root        40    30  0 08:00 ?        00:00:00 grep python
root@flask-demo-80854800-4t8wm:/# curl 127.0.0.1:5000
Hello World!root@flask-demo-80854800-4t8wm:/#
```

There is another way to access the container directly through `kubectl exec -it $POD_NAME bash`

```bash
➜  vagrant git:(master) ✗ kubectl exec -it flask-demo-80854800-4t8wm bash
root@flask-demo-80854800-4t8wm:/# ps -ef | grep flask
root         1     0  0 01:11 ?        00:00:00 /bin/sh -c python flask-demo.py
root         5     1  0 01:11 ?        00:00:04 python flask-demo.py
root        52    42  0 08:01 ?        00:00:00 grep flask
root@flask-demo-80854800-4t8wm:/#
```

# 4. Scale up Flask app

One of the powerful features offered by Kubernetes is how easy it is to scale your application. Suppose you suddenly need more capacity for your application; you can simply tell the deployment to manage a new number of replicas for your pod:

```bash
➜  vagrant git:(master) ✗ kubectl get pods
NAME                        READY     STATUS    RESTARTS   AGE
flask-demo-80854800-4t8wm   1/1       Running   0          1h
➜  vagrant git:(master) ✗ kubectl scale deployment flask-demo --replicas=4
deployment "flask-demo" scaled
➜  vagrant git:(master) ✗ kubectl get pods
NAME                        READY     STATUS    RESTARTS   AGE
flask-demo-80854800-39irt   1/1       Running   0          4s
flask-demo-80854800-4t8wm   1/1       Running   0          1h
flask-demo-80854800-hkkkw   1/1       Running   0          4s
flask-demo-80854800-t7ykr   1/1       Running   0          4s
➜  vagrant git:(master) ✗ kubectl get deployments
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
flask-demo   4         4         4            4           1h
```

We also can check each pod's location. They are distribued in two nodes.

```bash
➜  vagrant git:(master) ✗ kubectl get pods -o wide
NAME                        READY     STATUS    RESTARTS   AGE       IP          NODE
flask-demo-80854800-39irt   1/1       Running   0          6m        10.2.98.3   172.17.4.202
flask-demo-80854800-4t8wm   1/1       Running   0          1h        10.2.98.2   172.17.4.202
flask-demo-80854800-hkkkw   1/1       Running   0          6m        10.2.30.4   172.17.4.201
flask-demo-80854800-t7ykr   1/1       Running   0          6m        10.2.30.5   172.17.4.201
```
