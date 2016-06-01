FROM tispr/gocd-server

RUN apt-get update && apt-get install -y yum-utils

ADD cruise-config.xml /etc/go/
RUN mkdir -p /home/go/plugins
RUN curl -fSL "https://github.com/gocd-contrib/script-executor-task/releases/download/0.2/script-executor-0.2.jar" \
    -o /home/go/plugins/script-executor-plugin.jar
RUN chown -R go:go /etc/go/*
RUN chown -R go:go /home/go/plugins/