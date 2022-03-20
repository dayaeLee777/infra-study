# Docker 적용

## 목차
1. [**FrontEnd**](#1)
2. [**BackEnd**](#2)
3. [**Database**](#3)

<br/>

<div id="1"></div>

## 1️⃣ FrontEnd Setting

### 💡 DockerFile로 이미지 만들기
---
DockerFile : 도커만의 특별한 DSL로 이미지를 정의하는 파일

Nginx 로 작성

```docker
# build stage
FROM node:lts-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
RUN npm run build

# production stage
FROM nginx:stable-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 💡 도커 이미지 빌드
---
```bash
docker build . -t front:0.1
# -t : 도커 이미지 Tag명 -> 버전 관리 용도
```

### 💡 이미지 태그 추가 및 도커 허브 추가
---
```bash
# 태그에 버전 작성
docker tag front:0.1 front:latest

# 사용자 name으로 tag 작성
docker tag front:latest <DOCKER_HUB_ID>/front:latest

# 이미지 푸시
docker push <DOCKER_HUB_ID>/front:latest
```

### 💡 도커 실행
---
```
docker run --name front -d -p 80:80 <DOCKER_HUB_ID>/front
```

<br/>

<div id="2"></div>

## 2️⃣ BackEnd Setting

### 💡 application.properties
---
```
# ===============================
# = DATA SOURCE
# ===============================
server.address=0.0.0.0

spring.datasource.jdbc-url=jdbc:mysql://[주소]:3306/[DB명]?allowPublicKeyRetrieval=true&useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Seoul&zeroDateTimeBehavior=convertToNull&rewriteBatchedStatements=true
spring.datasource.username=
spring.datasource.password=
```

### 💡 DockerFile로 이미지 만들기
---
```
FROM openjdk:11-jdk

ARG JAR_FILE=target/*.jar

COPY ${JAR_FILE} app.jar

EXPOSE 8080 # 포트번호

ENTRYPOINT ["java","-jar","/app.jar"]
```

### 💡 도커 이미지 빌드
---
```
docker build . -t back:0.1
# -t : 도커 이미지 Tag명 -> 버전 관리 용도
```

### 💡 이미지 태그 추가 및 도커 허브 추가
---
```
# 태그에 버전 작성
docker tag back:0.1 back:latest

# 사용자 name으로 tag 작성
docker tag back:latest <DOCKER_HUB_ID>/back:latest

# 이미지 푸시
docker push <DOCKER_HUB_ID>/back:latest
```

### 💡 도커 실행
---
```
docker run --name back -d -p 8080:8080 <DOCKER_HUB_ID>/back
```


<br/>

<div id="3"></div>

## 3️⃣ Database Setting

### 💡 MySQL 이미지 다운로드
---
```bash
docker pull mysql:5.7.37
```

### 💡 ****MySQL 컨테이너 실행****
---
```bash
docker run --name mysql -e MYSQL_ROOT_PASSWORD=<password> -d -p 3306:3306 mysql:5.7.37 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
# -e: 환경변수를 설정하여 컨테이너를 실행할 수 있음

# mysql 클라이언트 명령어를 실행하여 DB 접속 테스트
docker exec -it mysql mysql -u root -p
```
