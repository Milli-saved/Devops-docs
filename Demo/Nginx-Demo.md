# Nginx Demo 
In this demo we will see the usecases that nginx acts as 
1.  Web server
2. Proxy server
3. Loadbalancer
Lets start by implementing as web server
# Nginx as Web server
![Pasted image ](20231214123241.png)

For this demo we select one website project from GitHub project https://github.com/learning-zone/website-templates/tree/master and configure it on docker, to do so please execute the following command on the project directory 

```bash
# clone the repo

# navigate to the repo

# Run nginx by mounting the application to the container
docker run -p 8080:80 -v $PWD:/usr/share/nginx/html -it nginx /bin/bash
# Access the website using the hostname of the vm with port 8080
curl http://ec2-52-209-48-167.eu-west-1.compute.amazonaws.com:8080
```

# Nginx as proxy server
![Pasted image ](20231214123805.png)
For this demo we extend what we started earlier by adding one more project and configure nginx to do proxy pass
```bash
#create network for the apps to resolve eachother
docker network create web-apps
# Run nginx by mounting the application to the container in background mode
docker run -d -v $PWD:/usr/share/nginx/html --network web-apps --name webapp-1 nginx
# clone second project and navigate to the project dir

# Run nginx by mounting the application to the container in background mode
docker run -d -v $PWD:/usr/share/nginx/html --network web-apps --name webapp-2 nginx

# clone the third project and navigate to the project dir

# Run nginx by mounting the application to the container in background mode
docker run -d -v $PWD:/usr/share/nginx/html --network web-apps --name webapp-3 nginx
```
Create nginx config file with the following content in the folder called `conf` in the current dir, you can name the file as `my-proxy.conf`

```conf
server {
	server_name ec2-52-209-48-167.eu-west-1.compute.amazonaws.com;
	listen 8080;
	location /app1 {
		proxy_pass http://webapp-1;
	}
	location /app2 {
		proxy_pass http://webapp-2;
	}
	location /app3 {
		proxy_pass http://webapp-3;
	}

}
```


```bash
# Now run the nginx proxy server as follows
docker run -dp 8080:80 -v $PWD:/usr/share/nginx/html -v $PWD/conf:/etc/nginx/conf.d --network web-apps --name webapp-3 nginx
# now do check the application by using the following command
curl http://ec2-52-209-48-167.eu-west-1.compute.amazonaws.com:8080/app1
curl http://ec2-52-209-48-167.eu-west-1.compute.amazonaws.com:8080/app2
curl http://ec2-52-209-48-167.eu-west-1.compute.amazonaws.com:8080/app3
```

# Nginx as Load balancer
![Pasted image ](20231215210855.png)

For this to work, you need to run the same application in multiple replicas with different name in the same network. For the demonstration purpose we will use different app. Follow the following steps.

```bash
#create network for the apps to resolve eachother
docker network create web-apps
# Run nginx by mounting the application to the container in background mode
docker run -d -v $PWD:/usr/share/nginx/html --network web-apps --name webapp-1 nginx
# clone second project and navigate to the project dir

# Run nginx by mounting the application to the container in background mode
docker run -d -v $PWD:/usr/share/nginx/html --network web-apps --name webapp-2 nginx

# clone the third project and navigate to the project dir

# Run nginx by mounting the application to the container in background mode
docker run -d -v $PWD:/usr/share/nginx/html --network web-apps --name webapp-3 nginx
```

Create nginx config file with the following content in the folder called `conf` in the current dir, you can name the file as `my-proxy.conf`

```conf
upstream app_lb {
  server webapp-1:80;
  server webapp-2:80;
  server webapp-3:80;
}
server {
	server_name ec2-52-209-48-167.eu-west-1.compute.amazonaws.com;
	listen 8080;
	location / {
		proxy_pass http://app_lb;
	}

}
```


```bash
# Now run the nginx proxy server as follows
docker run -dp 8080:80 -v $PWD:/usr/share/nginx/html -v $PWD/conf:/etc/nginx/conf.d --network web-apps --name webapp-3 nginx
# now do check the application by using the following command multiple times you should see differnet response now and then
curl http://ec2-52-209-48-167.eu-west-1.compute.amazonaws.com:8080/
```
