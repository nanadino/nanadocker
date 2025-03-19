
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

<br> 
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
멀티 스테이지 빌드는 빌드 환경과 실행 환경을 분리하여 최종 이미지 크기를 최소화할 수 있다. 
실행 환경에서는 애플리케이션을 실행하는 데 필요한 최소한의 구성만 포함된다.


<br>

✅ **최적화 방법 2**
<br>

alpine 추가 부분

![image](https://github.com/user-attachments/assets/7c0a0a2c-adfc-467a-ac4e-1bdb8b240f39)

기존에는 OpenJDK 기반 이미지를 사용하였으나 alpine 기반 이미지를 사용해 jdk와 jre 이미지 크기를 줄였다.


<br>

✅ **최적화 방법 3(추가예정)**

<br>
add --no-cache bash 로 패키지 설치 시 캐시를 저장하지 않게하여 이미지 크기를 줄일 수 있다.

<br>
<br>

✅ **결과**

![image (7)](https://github.com/user-attachments/assets/872bc12b-4f7b-4865-a320-2926764c4530)

결과적으로 Dockerfile 수정을 통해 최적화를 진행한 mongsil159357/myapp 에 해당하는 이미지 파일의 용량이 절반이상 감소하였다.


<br>
<br>



## 4️⃣ Trouble Shooting

<br>
<br>



<img src="https://capsule-render.vercel.app/api?type=waving&color=C1F0FF&height=150&section=footer" width="1000" />
