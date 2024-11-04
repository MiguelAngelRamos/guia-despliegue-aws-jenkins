```yml
services:
  jenkins:
    restart: always
    user: root
    image: docker/jenkins
    ports:
      - 8080:8080
      - 50000:50000
    container_name: jenkinsdocker
    volumes:
      - /home/vboxuser/Desktop/jenkins_docker/jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - net

  postgres:
    image: postgres:14
    networks:
      - net
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonarpasswd
    volumes:
      - /home/vboxuser/Desktop/jenkins_docker/postgres-data:/var/lib/postgresql/data

  sonarqube:
    image: sonarqube:lts
    platform: linux/amd64
    ports:
      - 9000:9000
      - 9092:9092
    networks:
      - net
    environment:
      SONARQUBE_JDBC_USERNAME: sonar
      SONARQUBE_JDBC_PASSWORD: sonarpasswd
      SONARQUBE_JDBC_URL: "jdbc:postgresql://postgres:5432/sonar"
    depends_on: 
      - postgres

  prometheus:
    image: prom/prometheus
    container_name: prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      - net

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - 3000:3000
    depends_on:
      - prometheus
    networks:
      - net

networks:
  net:
    driver: bridge


```
