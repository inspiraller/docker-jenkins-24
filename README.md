# Credits and references:
- ref: https://medium.com/@eloufirhatim/install-jenkins-using-docker-e76f41f79682
- ref2: https://www.digitalocean.com/community/tutorials/how-to-automate-jenkins-setup-with-docker-and-jenkins-configuration-as-code
- ref3: https://plugins.jenkins.io/configuration-as-code/

# Fixes
1. Problems with copying casc - instead place casc inside separate volume inside .casc_configs
2. Provide admin password in terminal - use a secrets file inside .secrets/
3. Problems running old installation file for plugins. Use new format
```sh
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli -f /usr/share/jenkins/ref/plugins.txt
```
4. Problems with environment variables not working in docker-compose
Place en variable inside Dockerfile
5. Problems with build failing
Stop and remove container and explicitly remove docker image and then rebuild (to ensure not using old cache, if updating casc.yaml file)


# Installation steps
1. create password file:
**.secrets/JENKINS_ADMIN_PASSWORD**
```sh
mypassword
```
2. `cd setup-docker-jenkins`
3. `mkdir jenkins_home`
4. `docker-compose up --build`
OR 
```sh
docker build -t docker-jenkins:1 .
docker run docker-jenkins:1
```
5. Look inside docker container: `docker exec -it docker-jenkins sh`
6. load browser: localhost:8080
7. login:
- username: admin
- pass: mypassword
done!

# Kill
- cd setup-docker-jenkins
- docker-compose down
- docker image rm docker-jenkins:1
- docker system prune
- rm -rf jenkins_home/* jenkins_home/.*

