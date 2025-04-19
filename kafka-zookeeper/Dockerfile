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
RUN mkdir -p zookeeper kafka

# Tạo các file YAML cho Zookeeper
COPY zookeeper/ /deployment/zookeeper/

# Tạo các file YAML cho Kafka
COPY kafka/ /deployment/kafka/

# Tạo script triển khai
RUN echo '#!/bin/bash\n\
    echo "==================================================="\n\
    echo "    ZOOKEEPER & KAFKA KUBERNETES DEPLOYMENT"\n\
    echo "==================================================="\n\
    \n\
    # Triển khai Zookeeper\n\
    echo "Deploying Zookeeper resources..."\n\
    cd /deployment/zookeeper\n\
    kubectl apply -f pv-zookeeper.yaml\n\
    kubectl apply -f pvc-zookeeper.yaml\n\
    kubectl apply -f zookeeper-statefulset.yaml\n\
    kubectl apply -f zookeeper-service.yaml\n\
    \n\
    # Triển khai Kafka\n\
    echo "Deploying Kafka resources..."\n\
    cd /deployment/kafka\n\
    kubectl apply -f rbac-kafka.yaml\n\
    kubectl apply -f pv-kafka.yaml\n\
    kubectl apply -f pvc-kafka.yaml\n\
    kubectl apply -f kafka-statefulset.yaml\n\
    kubectl apply -f kafka-service.yaml\n\
    \n\
    echo "Deployment completed successfully!"\n\
    echo "==================================================="\n\
    ' > /deployment/deploy.sh

# Cấp quyền thực thi cho script
RUN chmod +x /deployment/deploy.sh

# Thiết lập entrypoint
ENTRYPOINT ["/deployment/deploy.sh"]