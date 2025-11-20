# 1단계: Build Stage
# 컨테이너 안에서 gradle build 한 번 때려서 jar 파일을 만들기

# gradle:8.5-jdk17 기반 이미지(base image) 를 정하고 builder 라고 이름 붙임
FROM gradle:8.5-jdk17 AS builder
# 컨테이너 안에서 작업 디렉터리를 /app으로 설정
WORKDIR /app
# 앞의 . → 도커 빌드 컨텍스트(= 현재 로컬 디렉토리, gateway-service 폴더)의 모든 파일
# 뒤의 . → 컨테이너 내부의 작업 디렉토리(/app)
# 즉 로컬의 gateway-service 안에 있는 src, build.gradle, settings.gradle 등 모든 파일을 컨테이너 안 /app 폴더로 복사
COPY . .
# 프로젝트 빌드(기존 빌드 삭제 후 새로 빌드) & 테스트는 건너뛰기
RUN gradle clean build -x test

# 2단계: Run Stage
# 만들어진 JAR 로 가볍게 실행하는 단계

# 실행 전용 가벼운 Java 17 서버를 하나 새로 띄운다
FROM eclipse-temurin:17-jre
# 실행 서버에서도 /app이라는 폴더에서 돌아가게
WORKDIR /app
# AS builder라고 이름 붙인 이미지에서 /app/build/libs/*.jar 파일을 가져와서 현재 단계 컨테이너의 /app/app.jar로 복사
# 즉 빌드용 컨테이너에서 빌드 결과물(JAR)만 뽑아서 실행용 컨테이너에 들고 와서 app.jar로 저장해라
COPY --from=builder /app/build/libs/*.jar app.jar
# 이 컨테이너가 8081 포트에서 HTTP 요청을 받을 거라는 걸 도커/세상에 알려주는 표시
EXPOSE 8081
# 컨테이너가 실행될 때 항상 실행하는 명령
# 즉 이 이미지를 컨테이너로 띄우면, 스프링 부트 애플리케이션을 바로 실행해라
ENTRYPOINT ["java", "-jar", "app.jar"]
