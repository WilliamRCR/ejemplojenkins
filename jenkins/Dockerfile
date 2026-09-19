FROM jenkins/jenkins:lts-jdk17

USER root
RUN apt-get update \
    && apt-get install -y --no-install-recommends python3 python3-pip python3-venv git \
    && rm -rf /var/lib/apt/lists/*

USER jenkins
# Plugins mínimos para el ejemplo
RUN jenkins-plugin-cli --plugins git workflow-aggregator junit timestamper ws-cleanup pipeline-stage-view
