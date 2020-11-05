FROM golang:1.14
ARG GOARCH=amd64
WORKDIR /go/src/github.com/GoogleContainerTools/kaniko

# Add .docker config dir
RUN mkdir -p /kaniko/.docker

COPY . .
RUN make GOARCH=${GOARCH}

FROM centos:8

COPY --from=0 /go/src/github.com/GoogleContainerTools/kaniko/out/executor /kaniko/executor
COPY files/ca-certificates.crt /kaniko/ssl/certs/
COPY --from=0 /kaniko/.docker /kaniko/.docker
COPY files/nsswitch.conf /etc/nsswitch.conf
ENV HOME /root
ENV USER root
ENV PATH /usr/local/bin:/usr/bin:/usr/local/bin:/kaniko
ENV SSL_CERT_DIR=/kaniko/ssl/certs
ENV DOCKER_CONFIG /kaniko/.docker/
COPY config.json /kaniko/.docker/ 


#Install Java
RUN yum install -y java-1.8.0-openjdk-devel.x86_64
ENV JAVA_HOME /usr/lib/jvm/java

#Install Git
RUN yum install git -y

#Install Unzip
RUN yum install unzip -y

#Install OpenSSH Client
RUN yum install openssh-clients -y

#Install Maven
RUN mkdir -p /usr/share/maven && curl -fsSL -o /tmp/apache-maven.tar.gz https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz \
    && tar -xzf /tmp/apache-maven.tar.gz -C /usr/share/maven --strip-components=1 && rm -f /tmp/apache-maven.tar.gz && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn
ENV M2_HOME /usr/share/maven
#Copy maven settings.xml file (This is optional and comment it if not needed)
#COPY settings.xml /root/.m2/

#Install SonarScanner Cli (https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.5.0.2216.zip)
RUN  mkdir -p /usr/share/sonar-scanner-cli && curl -fsSL -o /tmp/sonar-scanner-cli.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.5.0.2216.zip \
     && unzip /tmp/sonar-scanner-cli.zip -d /tmp && mv /tmp/sonar-scanner-4.5.0.2216/*  /usr/share/sonar-scanner-cli && rm -f /tmp/sonar-scanner-cli.zip \
  && ln -s /usr/share/sonar-scanner-cli/bin/sonar-scanner /usr/bin/sonar-scanner  
  
# Download agent.jar from https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/
ARG VERSION=4.5
ARG user=root
ARG AGENT_WORKDIR=${user}/agent
RUN curl --create-dirs -fsSLo /usr/share/jenkins/agent.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${VERSION}/remoting-${VERSION}.jar
RUN chmod 755 /usr/share/jenkins \
     && chmod 644 /usr/share/jenkins/agent.jar \
      && ln -sf /usr/share/jenkins/agent.jar /usr/share/jenkins/slave.jar
USER ${user}
ENV AGENT_WORKDIR=${AGENT_WORKDIR}
RUN mkdir ${user}/.jenkins && mkdir -p ${AGENT_WORKDIR}

VOLUME ${user}/.jenkins
VOLUME ${AGENT_WORKDIR}
WORKDIR ${user}

#Now run the jenkins-agent script (which has  {java -jar agent.jar} command)
COPY jenkins-agent /usr/local/bin/jenkins-agent
RUN chmod +x /usr/local/bin/jenkins-agent &&\
    ln -s /usr/local/bin/jenkins-agent /usr/local/bin/jenkins-slave

ENTRYPOINT ["jenkins-agent"]
