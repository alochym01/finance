## Ebook for Docker
- [Docker Kubernetes Lab Handbook](https://docker-k8s-lab.readthedocs.io/en/latest/)
- [rsync](https://ciscochina.github.io/engineering/linux/2017/07/06/rsync.html)
## Docker Container
- Docker terms
	+ Docker images
		+ Docker image is made up of layers
		+ Every image has a unique ID
		+ a collection of all the files that make up an executable software
application
		+  files in the Docker image are read-only and hence the content of the image cannot be altered
	+ Docker containers
		+ is spun off from the Docker image
	+ Docker Registry
		+ a place where Docker images can be stored
- Work with Docker Image 
	+ Docker Hub
		+ a repository of images
		+ default location is index.docker.io
	+ Searching Docker images
		+ sudo docker search mysql
- Work with interactive Container
	+ Tracking changes inside Docker
		+ to list container_ID using `docker ps -a`
		+ using docker diff
			+ syntax: `docker diff container_ID`
	+ Controlling containers
		+ start container using `docker start container_ID`
		+ restart container using `docker restart container_ID`
		+ stop container using `docker stop container_ID`
	+ Housekeeping containers
		+ using `docker rm container_ID`
	+ Building images from container
		+ pull image from docker hub
		+ create some text files in docker image
		+ push changed image to docker hub
			+ syntax: ` docker commit container_ID repository/image_name`
	+ Launching a container as a daemon
		+ using `-d` option which means detached mode from `docker run` cli
		+ syntax: `docker run -d ubuntu`
- Building Images
	+ [Dockerfile syntax](https://docs.docker.com/engine/reference/builder/#from)
		+ `FROM` which sets the base image for the build process
			```

				FROM <image>[:<tag>|@<digest>]

					<image>: This is the name of the image that will be used as the base image`
					<tag> is optional and default is `latest`
					<digest> is optional and less used
						

				example: 
					FROM ubuntu@sha256:8e2324f2288c26e139cbf	# from digest
					FROM ubuntu:16.04							# from tag
			```

		+ `MAINTAINER` instruction is an informational instruction
			```

				MAINTAINER <author's detail>`
					- enables the authors to set the details in an image
					- place it after the `FROM` instruction

				example
					MAINTAINER Dr. Peter <peterindia@gmail.com>

			```

		+ `COPY` instruction enables you to copy the files from the Docker host to the filesystem of the new image
			```

				COPY <src> ... <dst>

					<src>: This is the source directory
					...: This indicates that multiple source files
					<dst>:
						+ This is the destination path for the new image
						+ If multiple files have been specified, it must end with a slash (/)
				example
					COPY httpd.conf magic /etc/httpd/conf/
					COPY html /var/www/html
			```

		+ `ADD` instruction is similar to the `COPY` instruction
			+ handle the TAR files and remote URLs
			```

				ADD <src> ... <dst>

					<src>: This is the source directory
					...: This indicates that multiple source files
					<dst>: This is the destination path for the new image

				example
					ADD web-page-config.tar /

			```

		+ `ENV` instruction sets an environment variable in the new image
			```

				ENV <key> <value>

					<key>: This is the environment variable
					<value>: This is the value that is to be set for the environment variable
					
				example: 
					ENV DEBUG_LVL 3
					ENV APACHE_LOG_DIR /var/log/apache
			```

		+ `ARG` instruction lets you define variables that can be passed during the Docker image build time
			```

				ARG <variable>[=<default value>]
		
					<variable>: This is the build argument variable
					<default value>: This is the default value you could optionally specify to the build argument variable

				example
					ARG usr
					ARG uid=1000
					docker build --build-arg usr=app --build-arg uid=100 . # from cli
			```

		+ `environment variables` declared using:
			+ `ENV` or `ARG` instruction can be used in
				+ ADD
				+ COPY
				+ ENV
				+ EXPOSE
				+ LABEL
				+ USER
				+ WORKDIR
				+ VOLUME
				+ STOPSIGNAL
				+ ONBUILD
			```

				example
					ARG BUILD_VERSION
					LABEL com.example.app.build_version=${BUILD_VERSION}

			```

		+ `USER` instruction sets the startup user ID or username in the new image
			+ the containers will be launched with root as the user ID or UID
			```

				USER <UID>|<UName>
				
					<UID>: This is a numerical user ID
					<UName>: This is a valid username

				example
					USER hadn

			```

		+ `WORKDIR` instruction changes the current working directory from `/` to the path specified
			```

				WORKDIR <dirpath>

				example
					WORKDIR /var/log

			```

		+ `VOLUME` instruction
			+ creates a directory in the image filesystem
			+ can be share data between docker host and docker containers
			+ be used for mounting volumes from the Docker host
			```

				VOLUME instruction has two types of syntax
					VOLUME ["<mountpoint>"]	#JSON object
					VOLUME <mountpoint>		#bash shell
						<mountpoint> is the mount point that has to be created in the new image
			```

		+ `EXPOSE` instruction opens up a container network port
			+ for communicating between the container and the external world
			+ allows you to specify multiple ports in a single line
			```

				EXPOSE <port>[/<proto>] [<port>[/<proto>]...]
				
					<port>: This is the network port that has to be exposed to the outside world.
					<proto>: This is an optional field provided for a specific transport protocol, such as TCP and UDP. If no transport protocol has been specified, then TCP is assumed to be the transport protocol.

				example
					EXPOSE 7373/udp 8080
						+ 7373 as a UDP
						+ 8080 as a TCP
			```

		+ `LABEL` instruction enables
			+ add key-value pairs as metadata
			+ metadata can be further leveraged to provide meaningful Docker image management and orchestration
			+ can have one or more key-value pairs
			```

				LABEL <key-1>=<val-1> <key-2>=<val-2> ... <key-n>=<val-n>

				example
					LABEL 	org.version="2.0"
							org.release-date="2016-08-05"

			```

		+ `RUN` instruction is the real workhorse during the build
			+ can run any command.
			+ recommendation is to execute the multiple commands using one `RUN` instruction
			```

				RUN ["<exec>", "<arg-1>", ..., "<arg-n>"]	#JSON object cli

					<exec>: This is the executable to run during the build time
					<arg-1>, ..., <arg-n>: These are the variable numbers (zero or more) of arguments for the executable


				RUN <command>								#bash shell cli
					 always executed using /bin/sh -c

				example
					 RUN ["bash", "-c", "rm", "-rf", "/tmp/abc"]
			```

		+ `CMD` instruction is similar to the `RUN`
			+ the major difference between these two is the time of execution
				+ `RUN` instruction is executed during the build time
				+ `CMD` instruction is executed when the container is launched
from the newly created image
				+ `CMD` instruction provides a default execution for this container
			```

				CMD instruction has three types of syntax
					- CMD ["<exec>", "<arg-1>", ..., "<arg-n>"]	#JSON object
				
						<exec>: This is the executable, which is to be run during the launch ofthe container
						<arg-1>, ..., <arg-n>: These are the variable numbers (zero or more) of arguments for the executable



					- CMD ["<arg-1>", ..., "<arg-n>"]			#JSON object

						<arg-1>, ..., <arg-n>: These are the variable numbers (zero or more) of arguments for the ENTRYPOINT instruction, which will be explained in the next section



					- CMD <command>								#bash shell
					
			```

		+ `HEALTHCHECK` instruction comes in handy to monitor the health of the containerized application
					
			```

				HEALTHCHECK [<options>] CMD <command>

					<command>: The HEALTHCHECK command is to be executed at a prescribed interval. If the command exit status is 0, the container is considered to be in the healthy state. If the command exit status is 1, the container is considered to be in the unhealthy state.
					<options>: By default, the HEALTHCHECK command is invoked every 30 seconds, the command timeout is 30 seconds, and the command is retried three times before the container is declared unhealthy. Optionally, you can modify the default interval, timeout, and retries values using the following options:
						--interval=<DURATION> [default: 30s]
						--timeout=<DURATION> [default: 30s]
						--retries=<N> [default: 3]
					
				example
					HEALTHCHECK --interval=5m --timeout=3s  CMD curl -f http://localhost/ || exit 1
			```

		+ `ONBUILD` instruction
			+ registers a build instruction to an image
			+ this gets triggered when another image is built using this image as its base image
			+ can be registered as a trigger and those instructions will be triggered immediately after the `FROM` instruction

			```

				ONBUILD <INSTRUCTION>

				example
					ONBUILD ADD config /etc/appconfig

			```

		+ `.dockerignore` file
			+ a newline-separated TEXT file, wherein you can provide the files and the directories which are to be excluded from the build process
			+ The exclusion list in the file can have both the fully specified file/directory name and the wildcards

			```
				
				.git
				*.tmp

			```

	+ Publish Images
		+ Docker Hub
			+ sign up an account at [https://hub.docker.com/]( https://hub.docker.com/)
			+ steps for login by cli
				+ docker login
				+ make some changes
				+ docker commit
				+ docker push repository/image_name
		+ Automating the build process for images
			+ automatically build the image from the Dockerfile kept in github
			+ keeps all the automated build images [https://github.com/docker/docker-registry](https://github.com/docker/docker-registry.)
			+ steps to auto builds
				+ We first connect Docker Hub to our GitHub account
				+ Log in to Docker Hub from [https://hub.docker.com/login/](https://hub.docker.com/login/)
					+ click Create > Create Automated Build
				+ We'll now select Link Accounts > Github
				+ whenever the Dockerfile is updated in GitHub
	+ Docker networking

		![network topology](https://raw.githubusercontent.com/alochym01/finance/master/Docker_Topology.jpg)

		+ install packages requirement to check docker bridge `bridge-utils`
		+ [references 1](https://github.com/xiaopeng163/docker-k8s-lab/blob/master/docs/source/docker/bridged-network.rst)
		+ [references 2](http://prog3.com/sbdm/blog/shlazww/article/details/47284675)

		```
			hadn@ubuntu:~/Documents/learning$ docker network ls
			NETWORK ID          NAME                DRIVER              SCOPE
			d00fae34f146        bridge              bridge              local
			3d69027c35df        host                host                local
			8b76080dfecc        none                null                local
			

			hadn@ubuntu:~/Documents/learning$ docker network inspect bridge
			[
			    {
			        "Name": "bridge",
			        "Id": "d00fae34f14674643352d315cb4f434faf3f6ded3f1fd95c5d26595401cbb7e8",
			        "Created": "2019-02-10T07:54:21.882669795+07:00",
			        "Scope": "local",
			        "Driver": "bridge",
			        "EnableIPv6": false,
			        "IPAM": {
			            "Driver": "default",
			            "Options": null,
			            "Config": [
			                {
			                    "Subnet": "172.17.0.0/16",
			                    "Gateway": "172.17.0.1"
			                }
			            ]
			        },
			        "Internal": false,
			        "Attachable": false,
			        "Ingress": false,
			        "ConfigFrom": {
			            "Network": ""
			        },
			        "ConfigOnly": false,
			        "Containers": {},
			        "Options": {
			            "com.docker.network.bridge.default_bridge": "true",
			            "com.docker.network.bridge.enable_icc": "true",
			            "com.docker.network.bridge.enable_ip_masquerade": "true",
			            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
			            "com.docker.network.bridge.name": "docker0",
			            "com.docker.network.driver.mtu": "1500"
			        },
			        "Labels": {}
			    }
			]

		```

	+ Running service in Container
		+ Building HTTP server image using Dockerfile
			+ Step 1

				```
					# Base image is Ubuntu
					FROM ubuntu:16.04
	
					# Author: Dr. Peter
					MAINTAINER Dr. Peter <peterindia@gmail.com>
	
					# Install apache2 package
					RUN apt-get update && \
					 	apt-get install -y apache2 && \
					 	apt-get clean
	
					# Set the log directory PATH
					ENV APACHE_LOG_DIR /var/log/apache2
	
					# Launch apache2 server in the foreground
					ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

				```
			+ Step 2

				```

					#naming the image as apache2
					docker build -t apache2 .

				```

			+ Step 3 to check image apache2 is built

				```

					docker images

					REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
					apache2             latest              bd9bf8f69344        About a minute ago   241MB
				```
		+ Running the HTTP server image as a service
			+ we launch the container in the detached mode
				```

					 docker run -d apache2

				```
			+ see whether our Docker container generates any output on its stdin (standard input) or stderr (standard error)
				```
					
					docker logs container_ID

				```
					
		+ Connecting to the HTTP service
			+ get ip address of container
				```

					docker inspect container_ID

				```

			+ check apache running properly
				```

					wget -qO - 172.17.0.13

					-q: quiet mode
					-O: print output to screen

				```
		+ Exposing container services
			+ Exposing container port using cli
				+ the `-p` option
					```

						- <hostPort>:<containerPort>
						- <containerPort>
						- <ip>:<hostPort>:<containerPort>
						- <ip>::<containerPort>

							<ip> is the IP address of the Docker host
							<hostPort> is the Docker host port
							<containerPort> is the port number of the container

						example
							docker run -d -p 80:80 apache2
					```
			+ Exposing container port `EXPOSE` instruction of `Dockerfile`
				```

					# Base image is Ubuntu
					FROM ubuntu:16.04
					
					# Author: Dr. Peter
					MAINTAINER Dr. Peter <peterindia@gmail.com>
					
					# Install apache2 package
					RUN apt-get update && \
					apt-get install -y apache2 && \
					apt-get clean
					
					# Set the log directory PATH
					ENV APACHE_LOG_DIR /var/log/apache2
					
					# Expose port 80
					EXPOSE 80
					
					# Launch apache2 server in the foreground
					ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]					
				```
			+ NAT for container
				```
				
					iptables -t nat -L -n

					Chain PREROUTING (policy ACCEPT)
					target     prot opt source               destination         
					DOCKER     all  --  0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL
					
					Chain INPUT (policy ACCEPT)
					target     prot opt source               destination         
					
					Chain OUTPUT (policy ACCEPT)
					target     prot opt source               destination         
					DOCKER     all  --  0.0.0.0/0           !127.0.0.0/8          ADDRTYPE match dst-type LOCAL
					
					Chain POSTROUTING (policy ACCEPT)
					target     prot opt source               destination         
					MASQUERADE  all  --  172.17.0.0/16        0.0.0.0/0           
					MASQUERADE  tcp  --  172.17.0.2           172.17.0.2           tcp dpt:80
					
					Chain DOCKER (2 references)
					target     prot opt source               destination         
					RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
					DNAT       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8080 to:172.17.0.2:80
					
				```
			+ Retrieving the container port
				```
				
					docker ps

					CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
					b20835f7880e        apache2             "/usr/sbin/apache2ct…"   About an hour ago   Up About an hour    0.0.0.0:8080->80/tcp   lucid_brattain
				```
			+ Binding a container to a specific IP address
				```

					docker run -d -p 198.51.100.73:80:80 apache2

				```

			+ Autogenerating the Docker host port
				```

					docker run -d -p 80 apache2
					docker run -d -p 198.51.100.73::80 apache2

					docker ps

					CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
					b20835f7880e        apache2             "/usr/sbin/apache2ct…"   About an hour ago   Up About an hour    0.0.0.0:45871->80/tcp   lucid_brattain
				```

	+ Sharing data with container
		+ Data volume
			+ is the fundamental building block of data sharing in the Docker environment
			+ it simply gets mounted inside the container
			+ A data volume can be inscribed in a Docker image
				```

					- using the VOLUME instruction of the Dockerfile
					- using the -v option of the docker run subcommand

				```
			+ data volume is part of the Docker host filesystem and it gets
mounted
				```

					hadn@ubuntu:~/Documents/learning/lab$ mount|grep sda
					/dev/sda1 on / type ext4 (rw,relatime,errors=remount-ro,data=ordered)
				```
			+ Dockerfile sample
				```

					FROM ubuntu:16.04
					VOLUME /MountPointDemo

				```
			+  using the -v <container mount point path> option of the `docker run` subcommand
				```

					docker run -v /MountPointDemo -it ubuntu:16.04

				```
		+ Volume management cli
			+ `docker volume` command supports four subcommands
				```

					create: This creates a new volume
					inspect: This displays detailed information about one or more volumes
					ls: This lists the volumes in the Docker host
					rm: This removes a volume

					example
						docker volume create --name example
						docker volume ls

						DRIVER              VOLUME NAME
						local               example



						docker volume inspect example


						[
						    {
						        "CreatedAt": "2019-02-10T19:22:23+07:00",
						        "Driver": "local",
						        "Labels": {},
						        "Mountpoint": "/var/lib/docker/volumes/example/_data",
						        "Name": "example",
						        "Options": {},
						        "Scope": "local"
						    }
						]



						docker volume rm example
				```
		+ Sharing host data
			```
			
				1. -v <container mount path>
				2. -v <host path>:<container mount path>
				3. -v <host path>:<container mount path>:<read write mode>
				4. -v <volume name>:<container mount path>
				5. -v <volume name>:<container mount path>:<read write mode>

					<host path> format is an absolute path in the Docker host
					<container mountpath> is an absolute path in the container filesystem
					<volume name> is the name of the volume created using the docker volume create subcommand
					<read writemode> can be either the read-only (ro) or read-write (rw) mode
			```
			+ using -v option of the `docker run` subcommand
				+ mount folder
					```

						docker run -v /tmp/hostdir:/MountPoint -it ubuntu:16.04
					```
				+ mount files
					```

						docker run -v /tmp/hostfile.txt:/mntfile.txt -it ubuntu:16.04
					```
				+ mount a named data volume to a container

					```
	
						docker volume create --name namedvol
						docker run -v namedvol:/MountPoint -it ubuntu:16.04
					```
			+ using Dockerfile
				```

					# Base image is Ubuntu
					FROM ubuntu:16.04
					
					# Author: Dr. Peter
					MAINTAINER Dr. Peter <peterindia@gmail.com>
					
					#Mounting Volume
					VOLUME /MountPointDemo

					# Install apache2 package
					RUN apt-get update && apt-get install -y apache2 && apt-get clean
					
					# Set the log directory PATH
					ENV APACHE_LOG_DIR /var/log/apache2
					
					# Expose host port 8080 and container port 80
					EXPOSE 8080 80
					
					# Launch apache2 server in the foreground
					ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]					
				```
		+ Sharing data between container
			+ using `--volume-from` option of the `docker run` subcommand
	+ Using docker-compose
		+ facilitate the running of a group of Docker containers
		+ A tools for pulling images, building the images, starting the containers in the correct sequence
		+ using `docker-compose.yml` file
			```

			version: "<version>"
			services:
			 <service>:
			 <key>: <value>
			 <key>:
			 - <value>
			 - <value>
			networks:
			 <network>:
			 <key>: <value>
			volumes:
			 <volume>:
			 <key>: <value>

				<version>: This is the version of the docker-compose file. Refer to the preceding version table. Default 3
				<service>: This is the name of the service, You can have more than one service definition
			```
		+ To build the Docker images using the `docker-compose build` command
		+ To launch the containers using the `docker-compose up` command
		+ docker-compose considers
			+ each docker-compose.yml file as a project
			+ it assumes the project name from the docker-compose.yml file's directory
			+ this can be overridden using the `-p` option
		+ sample `docker-compose.yml`
			+ We defined two services in this docker-compose.yml file
				+ The service named web is built using the Dockerfile in the current directory.
				+ The service named redis is instructed to launch a container with the
redis:latest image
			```
				Dockerfile

					# Base image is node.js
					FROM node:latest
	
					# Author: Dr. Peter
					MAINTAINER Dr. Peter <peterindia@gmail.com>
	
					# Install redis driver for node.js
					RUN npm install redis
	
					# Copy the source code to the Docker image
					ADD example.js /myapp/example.js
		
				docker-compose.yml

					version: "3.1"
					services:
					  web:
					    build: .
					    command: node /myapp/example.js
					    depends_on:
					    - redis
					    ports:
					    - 8080:80
					  redis:
					    image: redis:latest

				docker-compose build		#build
				docker-compose pull			#pull
				docker-compose up			#running docker compose

				docker-compose ps			#check services running
			```