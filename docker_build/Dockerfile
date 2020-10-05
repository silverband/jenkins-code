# Based on:
# https://technologyconversations.com/2017/06/16/automating-jenkins-docker-setup/
#
FROM jenkins/jenkins:lts
#FROM jenkinsci/blueocean:1.4.2

ENV JAVA_OPTS="-Djenkins.install.runSetupWizard=false"

# Next, we’re copying the configuration files into /usr/share/jenkins/ref/init.groovy.d/ directory.
# Jenkins, when initialized, will execute all the Groovy scripts located in that directory. 
# If you create additional setup scripts, that is the place you should place them. 
COPY config/set_admin_user.groovy /usr/share/jenkins/ref/init.groovy.d/set_admin_user.groovy
COPY config/csrf_protection.groovy /usr/share/jenkins/ref/init.groovy.d/csrf_protection.groovy
COPY config/disable_cli.groovy /usr/share/jenkins/ref/init.groovy.d/disable_cli.groovy

# All the plugins we want installed before we start
COPY config/plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt

# Install Docker which we need for building
USER root
RUN apt-get -y update && \
  apt-get -y install \
  apt-transport-https \
  ca-certificates \
  curl \
  gnupg2 \
  software-properties-common

RUN curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | apt-key add -

RUN add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
  $(lsb_release -cs) \
  stable"

RUN apt-get -y update && \
  apt-get -y install docker-ce

# 1. Ensures that /run/docker.sock exists
# 2. Make sure docker is group 994 (the external VM has docker group 994) *** WARNING Nasty hack
# 3. Changes the ownership of /run/docker.sock
# 4. Gives jenkins user permissions to access /run/docker.sock
# 5. Use /var/backups for jenkins config backups.
RUN touch /run/docker.sock && \
    groupmod -g 994 docker && \
    usermod -a -G docker jenkins && \
    chown root:docker /run/docker.sock && \
    chown jenkins:jenkins /var/backups

# Copying from USER in jenkins image... double check if things break in future... can pass ARG to children
USER jenkins

