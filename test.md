# Exam

### :one: Question 1
>create a dockerfile named adhochttpd.dockerfile
>
>when you build dockerfile it must install httpd server
>
>put your email id in suitable dockerfile instruction
>
>expose 80 port
>
>In your entrypoint execute a script that can host two webapplication script name start.sh
>
>use variable x=app in ENV dockerfile instruction and use x=app1 and x=app2 in shell script
>
>you can use github repo for both applications --https://github.com/mdn/beginner-html-site-styled
>
>you can use github repo for both applications --https://github.com/microsoft/project-html-website
>
>build this dockerfile and upload it on your dockerhub account
>
>docker image name must be yourdockerid/may2020q1:v1
>

#### adhochttpd.dockerfile
```shell
FROM centos
MAINTAINER shraddhasaini99@gmail.com
RUN yum install -y httpd
ENV x=app
EXPOSE 80
RUN mkdir /myapps
RUN mkdir /scripts
COPY beginner-html-site-styled /myapps/app1
COPY project-html-website /myapps/app2
COPY start.sh /scripts/start.sh
RUN chmod +x /scripts/start.sh
ENTRYPOINT ["/bin/bash","/scripts/start.sh"]
```

#### Git clone command
```shell
git clone https://github.com/mdn/beginner-html-site-styled
git clone https://github.com/microsoft/project-html-website
```

#### start.sh
```shell
#!/bin/bash

if [ "$x" == "app1" ]
then
	cp -rf /mycode/webapp1/* /var/www/html/
	httpd -DFOREGROUND

elif [ "$x" == "app2" ]
then
	cp -rf /mycode/webapp2/* /var/www/html/
	httpd -DFOREGROUND
else
	echo "You have not selected correct env for app1 or app2" > /var/www/html/index.html
	httpd -DFOREGROUND
fi
```
#### Docker build command
```shell
docker build -f adhochttpd.dockerfile -t shraddhasaini/may2020q1:v1 .
```
---------

### :two: Question 2
>write a pod file named q2.yaml where your pod name is adhocpod1
>
>it must be underyour own namespace use container port 80 and docker image nginx
>
>label of the pod must be adhoc:yournameq2 like adhoc:ashuq2
>
>create a service called q2svcyourname example q2svcashu of NodePort type and check by accessing it
>

##### q2.yml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    adhoc: shraddhasainiq2
  name: adhocpod1
spec:
  containers:
  - image: nginx
    name: adhocpod1
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
#### command
```shell
kubectl create -f q2.yaml
```

#### q2svcshraddhasaini.yml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: q2svcshraddhasaini
spec:
  type: NodePort
  selector:
    adhoc: shraddhasainiq2
  ports:
    - port: 80
      targetPort: 80
```
#### Command
```shell
kubectl create -f q2svcshraddhasaini.yml
```
--------
### :three: Question 3
>write a pod file named q3.yaml where your pod name is adhocpod2
>
>it must be underyour own namespace use container port 80 with docker image pushed in question 1
>
>label of the pod must be adhoc:yournameq3 like adhoc:ashuq3
>
>it must host webapp2 configured in docker image
>
>pod must be scheduled in minion 2
>
>create a service called q3svcyourname example q3svcashu of NodePort type and check by accessing it
>
>NodePort must be some custom port in the range of 30000-32000 make sure someone is not using the same port number
>

#### q3.yml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    adhoc: shraddhasainiq3
  name: adhocpod2
spec:
  nodeSelector:
    kubernetes.io/hostname: ip-172-31-36-77.ec2.internal
  containers:
  - env:
    - name: x
      value: app2
    image: shraddhasaini/may2020q1:v1
    name: adhocpod2
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
#### Command
```shell
kubectl create -f q3.yml
```

#### q3svcshraddhasaini.yml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: q3svcshraddhasaini
spec:
  type: NodePort
  selector:
    adhoc: shraddhasainiq3
  ports:
    - port: 80
      targetPort: 80
      nodePort: 31805
```
#### Command
```shell
kubectl create -f q3svcshraddhasaini.yml
```
---------
### :four: Question 4
>write a replicasets file named q4rs.yaml where your rs name is adhocrsyourname4
>
>it must be underyour own namespace use container port 80 with docker image pushed in question 1
>
>label of the pod must be adhoc:yournameq4 like adhoc:ashuq4
>
>create a service called q4svcyourname example q4svcashu of Loadbalancer type and check by accessing it

##### q4rs.yaml
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: adhocrsshraddhasaini4
  labels:
    app: adhocrsshraddhasainii4
spec:
  replicas: 1
  selector:
    matchLabels:
      adhoc: shraddhasainiq4 
  template:
    metadata:
      name: adhocpod4
      labels:
        adhoc: shraddhasainiq4
    spec:
      containers:
      - env:
        - name: x
          value: app2
        image: shraddhasaini/may2020q1:v1
        imagePullPolicy: Always
        name: adhocpod4
        ports:
        - containerPort: 80
```
#### Command
```shell
kubectl create -f q4rs.yaml
```
#### q4svcshraddhasaini.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: adhocrsshraddhasaini4
  name: q4svcshraddhasaini
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    adhoc: shraddhasainiiq4
  type: LoadBalancer
```
#### Command
```shell
kubectl create -f q4svcshraddhasaini.yaml
```
----------
### :five: Question 5
>write a deployment file named q5dep1.yaml where your deployment name is adhocdepyourname5
>
>it must be underyour own namespace use container port 80 with docker image pushed in question 1
>
>label of the pod must be adhoc:yournameq5 like adhoc:ashuq5
>
>make sure 3 pods must be there forever
>
>create a service called q5svcyourname example q5svcashu of Loadbalancer type and check by accessing it
>
##### q5dep1.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: adhhocdepshraddhasaini5
  labels:
    adhoc: shraddhasainiq5
spec:
  replicas: 3
  selector:
    matchLabels:
      adhoc: shraddhasainiq5
  template:
    metadata:
      labels:
        adhoc: shraddhasainiq5
    spec:
       containers:
        - env:
          - name: x
            value: app2
          image: shraddhasaini/may2020q1:v1
          imagePullPolicy: Always
          name: adhocpod2
          ports:
          - containerPort: 80
```
#### Command
```
kubectl create -f q5dep1.yaml
```
#### q5svcshraddhasaini.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    adhoc: q5svcshraddhasaini
  name: q5svcshraddhasaini
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    adhoc: shraddhasainiq5
  type: LoadBalancer
```
#### Command
```
kubectl create -f q5svcshraddhasaini.yaml
```
----------
### :six: Question 6
>write a pod file named portainer.yaml where your pod name is adhocpod6
>
>it must be underyour own namespace use container port 9000 and docker image portainer/portainer
>
>label of the pod must be adhoc:yournameq6 like adhoc:ashuq6
>
>portainer pod must be schedule in minion 3
>
>create a service called q6svcyourname example q6svcashu of NodePort type and check by accessing it
>
#### portainer.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    adhoc: shraddhasainiq6
  name: adhocpod6
spec:
  nodeSelector:
    kubernetes.io/hostname: ip-172-31-41-74.ec2.internal
  containers:
  - image: portainer/portainer
    name: adhocpod6
    ports:
    - containerPort: 9000
```
#### Command
```
kubectl create -f portainer.yaml
```

#### q6svcshraddhasaini.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: q6srvshraddhasaini
spec:
  type: NodePort
  selector:
    adhoc: shraddhasainiq6
  ports:
    - port: 9000
      targetPort: 9000
```
#### Command
```
kubectl create -f q6svcshraddhasaini.yaml
```
----------
### :seven: Question 7
>write a pod file named q7.yaml where your pod name is adhocpod7
>
>use alpine docker image and that run date commmand in every 3 seconds and store /mnt/date.txt
>
>label of the pod must be adhoc:yournameq7 like adhoc:ashuq7
>
>atleast after 3 minutes you can check the output and store output in your local system in file named adhocoutput.txt
>
#### q7.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    adhoc: shraddhasainiq7
  name: adhocpod7
spec:
  containers:
  - image: alpine
    name: adhocpod7
    command: ["/bin/shsh","-c","while true; do date>>/mnt/date.txt; sleep 3; done"]
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```
#### Command
```
kubectl create -f q7.yaml
```



