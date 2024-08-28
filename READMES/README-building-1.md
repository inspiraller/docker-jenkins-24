# Credits and references:
- ref: https://medium.com/@eloufirhatim/install-jenkins-using-docker-e76f41f79682
- ref2: https://www.digitalocean.com/community/tutorials/how-to-automate-jenkins-setup-with-docker-and-jenkins-configuration-as-code
- ref3: https://plugins.jenkins.io/configuration-as-code/
- ref4: https://www.eficode.com/blog/start-jenkins-config-as-code
- ref5: https://www.apoehlmann.com/blog/setting-up-jenkins-docker-with-jenkins-configuration-as-code-plugin/
- ref6: https://verifa.io/blog/getting-started-with-jenkins-config-as-code/
- ref7: https://piotrminkowski.wordpress.com/tag/continuous-integration/
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
1. `cd setup-docker-jenkins`
2. create password file:
**.secrets/JENKINS_ADMIN_PASSWORD**
```sh
mypassword
```

3. `mkdir jenkins_home`
4. `docker-compose up --build`
OR
### Note: docker run does not work because of some bug with setting: 
`--env CASC_JENKINS_CONFIG="/var/jenkins_conf"`
it thinks its an invalid path!

41. `docker build -t docker-jenkins:1 .`
42.
```sh
docker run --name docker-jenkins --rm --env JENKINS_ADMIN_ID=admin --env CASC_JENKINS_CONFIG="/var/jenkins_conf" \
-v /${PWD}/jenkins_home:/var/jenkins_home -v /${PWD}/.casc_configs:/var/jenkins_conf -v /${PWD}/.secrets:/run/secrets:ro -p 8080:8080 -p 50000:50000  docker-jenkins:1
```
> troubleshoot: --env CASC_JENKINS_CONFIG="/var/jenkins_conf" invalid path 
- https://github.com/jenkinsci/configuration-as-code-plugin/issues/2322
- https://github.com/jenkinsci/configuration-as-code-plugin/issues/1426
- https://stackoverflow.com/questions/75256386/jenkins-helm-chart-cannot-update-casc-jenkins-config-environment-variable
- https://github.com/jenkinsci/configuration-as-code-plugin/blob/master/README.md
Instead of setting the CASC_JENKINS_CONFIG environment variable, you can also define using the casc.jenkins.config Java property. This is useful when installing Jenkins via a package management tool and can't set an environment variable outside of a package-managed file, which could be overwritten by an update. For RHEL/CentOS systems, you can append the following to the JENKINS_JAVA_OPTIONS entry in /etc/sysconfig/jenkins

-Dcasc.jenkins.config=/jenkins/casc_configs

If you do not set the CASC_JENKINS_CONFIG environment variable or the casc.jenkins.config Java property, the plugin will default to looking for a single config file in $JENKINS_HOME/jenkins.yaml.


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
- docker volume prune
- docker system prune
- rm -rf jenkins_home/* jenkins_home/.*

