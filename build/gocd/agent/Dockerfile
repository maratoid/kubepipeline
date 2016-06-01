
FROM tispr/gocd-agent-dind
ADD autoregister.properties /var/lib/go-agent/config/

# Allow communication with an insecure registry
ENV DOCKER_DAEMON_ARGS="--insecure-registry=docker-registry:5000"

# kubectl
ENV SVC_ROOT=/usr/local/bin
ENV KUBE_LATEST_VERSION="v1.2.2"
ENV KUBE_LATEST_URL="https://github.com/kubernetes/kubernetes/releases/download/${KUBE_LATEST_VERSION}/kubernetes.tar.gz"

WORKDIR ${SVC_ROOT}

RUN wget -O ${SVC_ROOT}/kubernetes-latest.tar.gz $KUBE_LATEST_URL && tar xzvf ${SVC_ROOT}/kubernetes-latest.tar.gz && rm -f ${SVC_ROOT}/kubernetes-latest.tar.gz && tar xzvf ${SVC_ROOT}/kubernetes/server/kubernetes-server-linux-amd64.tar.gz && cp ${SVC_ROOT}/kubernetes/server/bin/kubectl ${SVC_ROOT} && rm -rf ${SVC_ROOT}/kubernetes

