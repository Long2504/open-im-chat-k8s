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
COPY . ./minio/

# Thiết lập entrypoint script để thực hiện các lệnh kubectl
RUN echo '#!/bin/bash \n\
    set -e \n\
    \n\
    cd ./minio/ \n\
    \n\
    echo "Applying MinIO Persistent Volume..." \n\
    kubectl apply -f minio-pv.yaml \n\
    \n\
    echo "Applying MinIO Persistent Volume Claim..." \n\
    kubectl apply -f minio-pvc.yaml \n\
    \n\
    echo "Applying MinIO Secret..." \n\
    kubectl apply -f minio-secret.yaml \n\
    \n\
    echo "Deploying MinIO..." \n\
    kubectl apply -f minio-deployment.yaml \n\
    \n\
    echo "Creating MinIO Service..." \n\
    kubectl apply -f minio-service.yaml \n\
    \n\
    echo "MinIO deployment completed successfully!" \n\
    ' > /app/deploy-minio.sh

RUN chmod +x /app/deploy-minio.sh

# Thiết lập entrypoint
ENTRYPOINT ["/app/deploy-minio.sh"]