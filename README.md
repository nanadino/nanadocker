
<img src="https://capsule-render.vercel.app/api?type=waving&color=C1F0FF&height=300&section=header&text=🐳Docker🐳&fontSize=70&fontColor=FFFFFF&animation=fadeIn&width=1200" width="1200" />


<br>

## 📍 Outline
- [1️⃣ Contributors](#1%EF%B8%8F⃣-contributors)
- [2️⃣ Contents](#2%EF%B8%8F⃣-contents)
- [3️⃣ Performance Optimization](#3%EF%B8%8F⃣-performance-optimization)
- [4️⃣ Trouble Shooting](#4%EF%B8%8F⃣-trouble-shooting)

<br>
<br>

## 1️⃣ Contributors
<br>

| <img src="https://avatars.githubusercontent.com/u/87555330?v=4" width="200px"> | <img src="https://avatars.githubusercontent.com/u/82265395?v=4" width="200px"> | <img src="https://github.com/JaeHee-devSpace.png" width="200px"> | <img src="https://avatars.githubusercontent.com/u/115103394?v=4" width="200px"> |
| :---: | :---: | :---: | :---: |
| [김민성](https://github.com/minsung159357) | [구민지](https://github.com/minjee83) | [박재희](https://github.com/JaeHee-devSpace) | [이현정](https://github.com/nanahj) |


<br>

## 2️⃣ Contents

### - Dockerfile 이미지 생성

```
# 1. 공식 OpenJDK 기반 이미지 사용
FROM openjdk:17

# 2. 작업 디렉토리 설정
WORKDIR /app

# 3. 빌드된 JAR 파일을 컨테이너로 복사
COPY ./myapp.jar myapp.jar

# 4. 실행 명령 설정 (JAR 파일 실행)
CMD ["java", "-jar", "myapp.jar"]
```

### - ```$docker build -t ...``` 명령어를 통한 이미지 생성
![image (9)](https://github.com/user-attachments/assets/c2fc85e7-7d6e-4d58-9319-d9ed77d8f6a3)

### - ```docker images``` 명령어로 이미지 생성 확인
![image (10)](https://github.com/user-attachments/assets/bbcff9d9-f569-4ef5-80ff-33fac8b03f1c)

### - ```docker hub``` 에 이미지 업로드
```
$ docker push nanahj/jarjar:1.0

$ docker pull nanahj/jarjar:1.0

$ docker run -d -p 8088:8088 --name mytwix nanahj/jarjar:1.0
```

![image (14)](https://github.com/user-attachments/assets/5a10cde2-e1e5-42e4-8eb7-2576c68145e3)

### - ```localhost:8088``` 에서 실행 확인
![image (13)](https://github.com/user-attachments/assets/9df054a7-b450-4aea-8347-ffec5e92364f)




<br>


## 3️⃣ Performance Optimization

<br>

⚠️ **원본 코드**

```
# 1. 공식 OpenJDK 기반 이미지 사용 (JDK 17)
FROM openjdk:17

# 2. 작업 디렉토리 설정
WORKDIR /app

# 3. 빌드된 JAR 파일을 컨테이너로 복사
COPY ./myapp.jar myapp.jar

# 4. 실행 명령 설정 (JAR 파일 실행)
CMD ["java", "-jar", "myapp.jar"]
```


<br>
<br>


✅ **최적화 방법 1 : 스테이지 분리**
<br>

```
# 빌드 스테이지
FROM eclipse-temurin:17-jdk-alpine AS builder
WORKDIR /app
COPY myapp.jar app.jar

# 실행 스테이지 (최적화)
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/app.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```
멀티 스테이지 빌드는 빌드 환경과 실행 환경을 분리하여 **최종 이미지 크기를 최소화**할 수 있다. 
실행 환경에서는 애플리케이션을 실행하는 데 필요한 최소한의 구성만 포함된다.


<br>

✅ **최적화 방법 2**
<br>

- alpine 추가 부분

![image](https://github.com/user-attachments/assets/7c0a0a2c-adfc-467a-ac4e-1bdb8b240f39)

기존에는 OpenJDK 기반 이미지를 사용하였으나 **alpine 기반 이미지**를 사용해 jdk와 jre 이미지 크기를 줄였다.


<br>

✅ **최적화 방법 3(추가예정)**

<br>
add --no-cache bash 로 패키지 설치 시 캐시를 저장하지 않게하여 이미지 크기를 줄일 수 있다.

<br>
<br>

❗ **결과**

![image (7)](https://github.com/user-attachments/assets/872bc12b-4f7b-4865-a320-2926764c4530)

결과적으로 Dockerfile 수정을 통해 최적화를 진행한 mongsil159357/myapp 에 해당하는 이미지 파일의 용량이 **50% 이상 감소**하였다.


<br>
<br>


## 4️⃣ Trouble Shooting

## ❶ **문제 발생 상황 🚨**  
![image](https://github.com/user-attachments/assets/a9438d04-c4c1-4b8d-b753-6c817afa87a8)

- `docker run -p 8080:8080 <contaniner_name>` 으로 실행했음에도 **웹페이지 접속 및 curl 요청이 실패함**  
- 하지만 `docker run --net=host <contaniner_name>` 실행 후에는 curl 요청이 정상 작동  

→ 즉, **기본 bridge 네트워크 모드에서는 작동하지 않았고, host 네트워크 모드에서는 정상 동작**  

---

## ❷ **문제 원인 분석**  
### **🔍 원인 1: Spring Boot 애플리케이션의 바인딩 주소 문제**  
컨테이너에서 실행된 애플리케이션이 **`localhost (127.0.0.1)`에만 바인딩** 되어 있으면,  
기본적인 Docker **포트 매핑(`-p 8080:8080`)이 제대로 동작하지 않음**.  

#### **🛠 확인 방법**
컨테이너 내부에서 확인:
```bash
docker exec -it <contaniner_name> /bin/sh
netstat -tulnp | grep 8080
```
출력 결과가:
```
tcp  0  0 127.0.0.1:8080  0.0.0.0:*  LISTEN  1/java
```
➡ **애플리케이션이 `127.0.0.1:8080` 에 바인딩되어 있어서, 외부에서 접근할 수 없음**  

#### **🛠 해결 방법**
Spring Boot 설정 파일 수정 (`application.properties` 또는 `application.yml`):  
```properties
server.address=0.0.0.0
server.port=8080
```
이렇게 설정하면 컨테이너 내부에서 **모든 네트워크 인터페이스(`0.0.0.0`)에서 요청을 받을 수 있음**.  

---

### **🔍 원인 2: Docker의 bridge 네트워크와 호스트 네트워크의 차이**
기본적으로 Docker는 컨테이너를 **bridge 네트워크**에 연결함.  
- `-p 8080:8080` 옵션을 주면, **호스트의 8080 포트와 컨테이너의 8080 포트를 연결**  
- 하지만 **애플리케이션이 `127.0.0.1:8080` 에 바인딩되어 있으면, bridge 네트워크를 통해 접근 불가**  

➡ **해결책:**  
- `server.address=0.0.0.0` 으로 변경  
- 또는 **host 네트워크 모드(`--net=host`) 사용** → 이 모드를 사용하면 컨테이너가 **호스트와 동일한 네트워크 환경을 사용**하므로 `127.0.0.1` 바인딩 문제를 회피 가능
```
docker stop <contaniner_name>
docker rm <contaniner_name>
docker run --net=host --name <contaniner_name> <docker_id>/<image_name>
```
<br>


### ✔️ 정상 실행 확인
![image](https://github.com/user-attachments/assets/c9c411d2-53b1-4fc1-94ff-1d085866ddde)

<br>

---

## ❸ **왜 `--net=host`를 사용했을까?**
- 기본적으로 **Docker bridge 네트워크는 NAT를 사용하여 포트 매핑**  
- 하지만 Spring Boot가 `127.0.0.1` 만 리스닝하면, **bridge 네트워크를 통해 접근 불가**  
- `--net=host`를 사용하면 **호스트와 동일한 네트워크를 사용**하므로, `127.0.0.1` 바인딩 문제가 해결됨  

<br>
<br>



<img src="https://capsule-render.vercel.app/api?type=waving&color=C1F0FF&height=150&section=footer" width="1000" />
