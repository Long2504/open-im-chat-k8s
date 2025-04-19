# Sử dụng Alpine Linux làm base image vì nhẹ
FROM alpine:3.18

# Cài đặt kubectl và các công cụ cần thiết
RUN apk add --no-cache curl bash

# Cài đặt kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    chmod +x kubectl && \
    mv kubectl /usr/local/bin/

# Tạo thư mục làm việc
WORKDIR /app

# Sao chép các file cấu hình Kubernetes từ máy host vào container
COPY ./deploy/ ./deploy/

# Thiết lập entrypoint script để thực hiện các lệnh kubectl
RUN echo '#!/bin/bash \n\
    set -e \n\
    \n\
    cd ./deploy/ \n\
    \n\
    echo "Applying secrets..." \n\
    kubectl apply -f redis-secret.yml -f minio-secret.yml -f mongo-secret.yml -f kafka-secret.yml \n\
    \n\
    echo "Applying OpenIM config..." \n\
    kubectl apply -f ./openim-config.yml \n\
    \n\
    echo "Applying cluster role..." \n\
    kubectl apply -f ./clusterRole.yml \n\
    \n\
    echo "Applying OpenIM services and deployments..." \n\
    kubectl apply \\\n\
    -f openim-api-deployment.yml \\\n\
    -f openim-api-service.yml \\\n\
    -f openim-crontask-deployment.yml \\\n\
    -f openim-rpc-user-deployment.yml \\\n\
    -f openim-rpc-user-service.yml \\\n\
    -f openim-msggateway-deployment.yml \\\n\
    -f openim-msggateway-service.yml \\\n\
    -f openim-push-deployment.yml \\\n\
    -f openim-push-service.yml \\\n\
    -f openim-msgtransfer-service.yml \\\n\
    -f openim-msgtransfer-deployment.yml \\\n\
    -f openim-rpc-conversation-deployment.yml \\\n\
    -f openim-rpc-conversation-service.yml \\\n\
    -f openim-rpc-auth-deployment.yml \\\n\
    -f openim-rpc-auth-service.yml \\\n\
    -f openim-rpc-group-deployment.yml \\\n\
    -f openim-rpc-group-service.yml \\\n\
    -f openim-rpc-friend-deployment.yml \\\n\
    -f openim-rpc-friend-service.yml \\\n\
    -f openim-rpc-msg-deployment.yml \\\n\
    -f openim-rpc-msg-service.yml \\\n\
    -f openim-rpc-third-deployment.yml \\\n\
    -f openim-rpc-third-service.yml \n\
    \n\
    echo "OpenIM deployment completed successfully!" \n\
    ' > /app/deploy.sh

RUN chmod +x /app/deploy.sh

# Thiết lập entrypoint
ENTRYPOINT ["/app/deploy.sh"]