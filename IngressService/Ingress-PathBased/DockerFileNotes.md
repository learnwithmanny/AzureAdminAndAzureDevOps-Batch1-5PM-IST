# This file explains the process of creating images to be used in Path Based routing of AKS Ingress Service
To implement path based routing using Ingress in AKS, we need two images. These two images should be deployed in dedicated path.
The same path needs to be used in ingress service yaml manifest under spec / rule / path. 

- For example 
	- https://publicipofingress/app1 should open application configured in app1 image 
	- https://publicipofingress/app2 should open application configured in app2 image

- Below Yaml should be used for this.
	apiVersion: networking.k8s.io/v1
	kind: Ingress
	metadata:
	  name: nginxapp1-ingress-service
	spec:
	  ingressClassName: nginx
	  rules:
	  - http:
	      paths:
	      - path: /app1
	        pathType: Prefix
	        backend:
	          service:
	            name: myapp1-clusterip-service
	            port:
	              number: 80
	      - path: /app2
	        pathType: Prefix
	        backend:
	          service:
	            name: myapp2-clusterip-service
	            port:
	              number: 80
#------------------------------------------------------------------------------------------#

#Image creation process for app1 and app2. 
Use docker to create image and push image to docker hub.

#App1

1.	Copy all source code files at one location. let's say c:\app1
2.	create a Dockerfile with below code and keep at same location. In this case at c:\app1
		
		FROM nginx
		RUN mkdir -p /usr/share/nginx/html/app1
		COPY index.html /usr/share/nginx/html/app1

3.	open docker console
4. 	Build Docker Image 
	> docker build -t <your-docker-hub-id>/myapp1image1:v1 .

	#Replace your docker hub account Id

	>docker build -t eglitmyk/myapp1image1:v1 .
	
5.	Push the image t docker hub
	
	>docker push <your-docker-hub-id>/myapp1image1:v1
	
	#Replace your docker hub account Id
	
	>docker push eglitmyk/myapp1image1:v1
	
6.	Verify the same on docker hub

#------------------------------------------------------------------------------------------#

#App2

1.	Copy all source code files at one location. let's say c:\app2
2.	create a Dockerfile with below code and keep at same location. In this case at c:\app2
		
		FROM nginx
		RUN mkdir -p /usr/share/nginx/html/app2
		COPY index.html /usr/share/nginx/html/app2

3.	open docker console
4. 	Build Docker Image 
	> docker build -t <your-docker-hub-id>/myapp2image1:v1 .

	#Replace your docker hub account Id

	>docker build -t eglitmyk/myapp2image1:v1 .
	
5.	Push the image t docker hub
	
	>docker push <your-docker-hub-id>/myapp2image1:v1
	
	#Replace your docker hub account Id
	
	>docker push eglitmyk/myapp2image1:v1
	
6.	Verify the same on docker hub
