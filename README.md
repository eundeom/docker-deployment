# 💌 docker-deployment 

## ⋱ Docker deployment 실습
이 레포지토리는 **Spring Boot** 애플리케이션을 **Docker**를 사용하여 컨테이너화 하고, Docker Hub에 업로드하여 배포하는 방법을 다룬다.
이번 실습에서는 OpenJDK 기반의 Docker 이미지를 사용하여 Spring Boot 애플리케이션을 실행한 후 **Docker Hub**에 이미지를 푸시하여 어디서든 실행할 수 있도록 한다.

<br>

## 🌐 필요한 도구 및 환경
- Docker
- Spring Boot application .jar file
- Docker Hub ∙ account (이미지 업로드용)

<br>

## 🔍 실습 개요
1. Spring Boot 어플리케이션을 Docker 컨테이너로 빌드하기
2. Docker Hub에 이미지를 업로드하여 배포하기
3. Docker 컨테이너에서 Spring Boot 애플리케이션 실행하기

<br>

## 🚶‍♂️ 실습 단계
### 1) Dockerfile 작성 및 설정
Dockerfile을 작성해서 스프링 부트 애플리케이션을 Docker 이미지로 만들기 위한 설정을 정의해준다. <br>

&nbsp; **1-1)** 작업 디렉토리 05.myImage 생성 후 **Dockerfile** 생성 <br>

    /05.myImage
    ├── Dockerfile
    └── step01_basic-0.0.1-SNAPSHOT.jar


&nbsp; **1-2)** 로컬에서 빌드한 **`.jar` 파일**을 컨테이너로 복사하기 <br>

    $cp step01_basic-0.0.1-SNAPSHOT.jar 05.myImage/step01_basic-0.0.1-SNAPSHOT. 

<br>

**Dockerfile:**

    # 1. 기본 이미지를 설정 (OpenJDK 기반 이미지)
    FROM openjdk:17-jdk-slim
    
    # 2. 작업 디렉터리 설정
    WORKDIR /app
    
    # 3. 로컬에서 빌드한 `.jar` 파일을 Docker 이미지로 복사
    COPY ./step01_basic-0.0.1-SNAPSHOT.jar app.jar
    
    # 4. Spring Boot 애플리케이션 실행
    ENTRYPOINT ["java", "-jar", "/app/app.jar"]
    
    # 5. 컨테이너가 사용할 포트 설정 (Spring Boot 기본 포트 8080)
    EXPOSE 8080

<br>

### 2) Docker 이미지 빌드
&nbsp; **2-1)** **docker build** 명령어로 mystringboot라는 이름으로 Docker 이미지를 빌드한다. (string boot (x) spring boot (o) 오타 😋) 
<br>

    docker build -t mystringboot . 

### 3) Docker 이미지 태그 지정
&nbsp; **3-1)** Docker Hub에 업로드할 때 이미지를 태그(latest)로 지정한다.  

    docker tag mystringboot eundeom/mystringboot:latest 

### 4) Docker Hub에 이미지 업로드
&nbsp; **4-1)** Docker Hub에 로그인한 후, docker push 명령어로 이미지를 업로드한다.
    
     docker push eundeom/mystringboot:latest 
    
&nbsp; **4-2) Docker Hub Repository**에서 이미지 업로드된 거 확인 가능
<br>
  <img src="https://github.com/user-attachments/assets/18bebad7-c3f6-4fb4-898a-677fc8bfc435" width="700">

### 5) Docker 컨테이너 실행
&nbsp; **5-1)** **docker run** 명령어로 Docker 컨테이너를 실행하여 Spring Boot 애플리케이션을 실행한다. 
<br>

`-p` 옵션으로 포트를 매핑해서 호스트에서 **8080 포트**를 통해 애플리케이션에 접근할 수 있다!  <br>

    docker run -p 8080:80 mystringboot 

<br>

## ✅ 배포 후 결과 확인
### [1] 브라우저 정상 실행 확인
브라우저에서 http://localhost:8080/woori/fisa로 접속하여 Spring Boot 애플리케이션이 정상적으로 실행되는지 확인 가능하다.
<br>

<img src="https://github.com/user-attachments/assets/f2085efe-820d-4eb1-b415-487ddd919dd1" width=600>

### [2] command로 정상 실행 확인
`curl http://localhost:8080/woori/fisa` 실행해서 확인 가능
<br>

<img src="https://github.com/user-attachments/assets/ad5bc0f2-693d-4ad6-b9ea-fcd44b59b51c" width=700>


<br>
<br>

## 🔄 후속 작업
### Docker 이미지 크기 최적화 (JDK ➡ JRE 변경)
### JDK vs JRE

| **항목**           | **JDK (Java Development Kit)**                            | **JRE (Java Runtime Environment)**                             |
|--------------------|-----------------------------------------------------------|---------------------------------------------------------------|
| **목적**           | Java 애플리케이션을 **개발**하는 데 필요한 도구들 (컴파일러, 디버거 등) | Java 애플리케이션을 **실행**하는 데 필요한 환경만 제공           |
| **포함된 도구**    | 컴파일러 (javac), 디버거 (jdb), 실행 환경 (JVM), 라이브러리 등  | Java 애플리케이션을 실행하는 데 필요한 JVM 및 기본 라이브러리   |
| **크기**           | 크기가 크다 (개발 도구 포함)                                 | 크기가 작다 (실행 환경만 포함)                                 |

### 수정된 Dockerfile
```
# 1. JDK에서 JRE로 변경 (최소한의 JRE 기반 이미지 사용)
FROM eclipse-temurin:17-jre-alpine
```

### JRE로 변경 후 이미지 크기 확인
`docker images`로 이미지 크기 확인 가능하다.
초반 크기 약 **400MB** → 이미지 수정 후 **200MB**로 줄어든 것 확인!

<img src="https://github.com/user-attachments/assets/99d61ddc-444d-4f22-8cd9-878cd19880fa" width=800>

   
