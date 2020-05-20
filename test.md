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

#### Git clone
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
#### Docker build
```shell
docker build -f adhochttpd.dockerfile -t shraddhasaini/may2020q1:v1 .
```