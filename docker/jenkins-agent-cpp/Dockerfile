FROM jenkins/agent

USER root

RUN apt update && \
    apt install -y g++ unzip

USER jenkins

CMD g++ --version
