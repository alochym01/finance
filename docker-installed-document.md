Requirements
- OS CentOS 7
- Install Docker
	+ yum install -y yum-utils device-mapper-persistent-data lvm2
	+ yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
	+ yum install docker-ce docker-ce-cli containerd.io
	+ check version of docker
		+ yum list docker-ce --showduplicates | sort -r
	+ install specific docker version
		+ yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
- Starting Docker
	+ systemctl start docker
- Verify that Docker CE is installed correctly
	+ docker run hello-world
- Installed Docker by script
	+ sudo curl -sSL https://get.docker.io/ | sh