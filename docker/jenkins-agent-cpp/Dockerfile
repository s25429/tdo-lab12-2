FROM jenkins/agent

USER root

RUN apt update && \
    apt install -y g++

USER jenkins

CMD g++ --version
