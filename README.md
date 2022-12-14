# mattermost_in_aws
install mattermost in aws linux

1. Create t3.micro instance with  Amazon Linux 2 AMI Kernal 5.1
2. Switch on “Allow HTTPS” and “Allow HTTP”
4. Launch instance
5. Connect to the instance
6. Update AWS linux
```
	sudo yum update
```
7. Install git 
```
	sudo yum install -y git
```
8. Install docker
```
	sudo yum install -y docker
```
9. Download Mattermost docker image
```
	git clone https://github.com/mattermost/docker && cd docker
```
10. Copy default  configure
```
	cp env.example .env
```
11. edit .env and change the DOMAIN into your domain
```
	DOMAIN=XXX.abc.com
```
12. edit .env and change the CERT and KEY path
```
	#CERT_PATH=./volumes/web/cert/cert.pem
	#KEY_PATH=./volumes/web/cert/key-no-password.pem
	#GITLAB_PKI_CHAIN_PATH=<path_to_your_gitlab_pki>/pki_chain.pem
	CERT_PATH=./certs/etc/letsencrypt/live/${DOMAIN}/fullchain.pem
	KEY_PATH=./certs/etc/letsencrypt/live/${DOMAIN}/privkey.pem
```
13. Create the required directories and set their permissions.
```
	mkdir -p ./volumes/app/mattermost/{config,data,logs,plugins,client/plugins,bleve-indexes}
	sudo chown -R 2000:2000 ./volumes/app/mattermost
```
14. Enable and start docker service
```
	sudo systemctl enable docker.service
	sudo systemctl start docker.service
```
15. Go to your dns server and add A record and point it into your server 's IP.

16. Configure TLS for NGINX (using Let’s Encrypt to create free https cert) and check the success message.
```
	bash scripts/issue-certificate.sh -d XXX.abc.com  -o ${PWD}/certs
```

17. Install docker-compost
```
    sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
    sudo chmod +x /usr/local/bin/docker-compose
    docker-compose version	
```

18. Startup the mattermost server
```
	sudo /usr/local/bin/docker-compose -f docker-compose.yml -f docker-compose.nginx.yml up -d
```

19. You can access the Mattermost website now


ps. stop the mattermost server 
```
	sudo /usr/local/bin/docker-compose -f docker-compose.yml -f docker-compose.nginx.yml stop
```
