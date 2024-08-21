FROM jenkins/jenkins:2.473
# 2.473 is latest - as of date: 21/08/2024
ENV JAVA_OPTS -Djenkins.install.runSetupWizard=false
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli -f /usr/share/jenkins/ref/plugins.txt
USER root


