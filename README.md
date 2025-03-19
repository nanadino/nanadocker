
<img src="https://capsule-render.vercel.app/api?type=waving&color=C1F0FF&height=300&section=header&text=🐳Docker🐳&fontSize=70&fontColor=FFFFFF&animation=fadeIn&width=1200" width="1200" />


<br>



<br>

## 📍 Outline
- [1️⃣ Contributors](#1%EF%B8%8F⃣-contributors)
- [2️⃣ Contents](#2%EF%B8%8F⃣-contents)
- [3️⃣ Performance Optimization](#3%EF%B8%8F⃣-performance-optimization)
- [4️⃣ Trouble Shooting](#4%EF%B8%8F⃣-trouble-shooting)
- [5️⃣ Retrospective](#5%EF%B8%8F⃣-retrospective)

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



<br>


## 4️⃣ Trouble Shooting

<br>
<br>

## 5️⃣ Retrospective

<br>
<br>
