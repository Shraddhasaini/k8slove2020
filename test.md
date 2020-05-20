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
