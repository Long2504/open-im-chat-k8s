# Base image với kubectl đã được cài đặt
FROM bitnami/kubectl:latest

# Chuyển sang user root để cài đặt các gói cần thiết
USER root

# Cài đặt các công cụ cần thiết
RUN apt-get update && \
    apt-get install -y curl bash && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Tạo thư mục làm việc
WORKDIR /deployment

# Tạo cấu trúc thư mục
RUN mkdir -p mongodb redis

# Sao chép file storage-class.yaml vào thư mục gốc
COPY storage-class.yaml /deployment/

# Sao chép các file MongoDB
COPY mongodb/ /deployment/mongodb/

# Sao chép các file Redis
COPY redis/ /deployment/redis/

# Tạo script triển khai
RUN echo '#!/bin/bash\n\
    echo "==================================================="\n\
    echo "    MONGODB & REDIS KUBERNETES DEPLOYMENT"\n\
    echo "==================================================="\n\
    \n\
    # Triển khai StorageClass\n\
    echo "Applying StorageClass..."\n\
    cd /deployment\n\
    kubectl apply -f storage-class.yaml\n\
    \n\
    # Triển khai MongoDB\n\
    echo "Deploying MongoDB resources..."\n\
    cd /deployment/mongodb\n\
    kubectl apply -f mongodb-pv.yaml\n\
    kubectl apply -f mongodb.yaml\n\
    \n\
    # Triển khai Redis\n\
    echo "Deploying Redis resources..."\n\
    cd /deployment/redis\n\
    kubectl apply -f redis-pv.yaml\n\
    kubectl apply -f redis.yaml\n\
    \n\
    echo "Deployment completed successfully!"\n\
    echo "==================================================="\n\
    \n\
    # Hiển thị thông tin về các resource đã được triển khai\n\
    echo "Checking deployment status:"\n\
    echo "MongoDB pods:"\n\
    kubectl get pods -l app=mongodb\n\
    echo "\nRedis pods:"\n\
    kubectl get pods -l app=redis\n\
    echo "\nPersistent Volumes:"\n\
    kubectl get pv\n\
    echo "\nPersistent Volume Claims:"\n\
    kubectl get pvc\n\
    ' > /deployment/deploy.sh

# Cấp quyền thực thi cho script
RUN chmod +x /deployment/deploy.sh

# Thiết lập entrypoint
ENTRYPOINT ["/deployment/deploy.sh"]