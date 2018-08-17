FROM jenkins/jenkins:lts

# install Maven
USER root
RUN apt-get update && apt-get install -y maven
USER jenkins

RUN /usr/local/bin/install-plugins.sh jdk-tool
RUN /usr/local/bin/install-plugins.sh ssh-slaves
RUN /usr/local/bin/install-plugins.sh greenballs
RUN /usr/local/bin/install-plugins.sh simple-theme-plugin
RUN /usr/local/bin/install-plugins.sh kubernetes
RUN /usr/local/bin/install-plugins.sh blueocean

ENV JAVA_OPTS="-Djenkins.install.runSetupWizard=false"
