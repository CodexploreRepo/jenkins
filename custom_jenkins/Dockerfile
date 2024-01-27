# Ref: https://hackmamba.io/blog/2022/04/running-docker-in-a-jenkins-container/
FROM jenkins/jenkins:lts
USER root
# Install docker CLI
RUN curl -fsSL https://get.docker.com -o get-docker.sh \
    && chmod 777 get-docker.sh \
    && ./get-docker.sh

USER jenkins