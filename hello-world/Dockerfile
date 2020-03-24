# Builder image
FROM openjdk:8 as builder

WORKDIR /builder
ADD . /builder

RUN ./gradlew build --stacktrace

# Runtime image
FROM openjdk:8-jre-alpine

WORKDIR /app

COPY --from=builder /builder/build/libs/hello-world-0.0.1-SNAPSHOT.jar .

EXPOSE 8080

CMD java -jar hello-world-0.0.1-SNAPSHOT.jar
