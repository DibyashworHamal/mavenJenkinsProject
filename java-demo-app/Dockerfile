FROM maven:3.9-eclipse-temurin-21 AS builder
WORKDIR /app
COPY /java-demo-app/pom.xml /app
RUN mvn dependency:go-offline
COPY /java-demo-app/src/ ./src
RUN mvn clean package

FROM eclipse-temurin:21-jre-alpine
LABEL maintainer="Divyashwor Hamal" \
        version="1.0.0" \
        description="Java Demo App"
RUN addgroup -S demogroup && adduser -S demouser -G demogroup
WORKDIR /app
COPY --from=builder --chown=demouser:demogroup /app/target/*.war demo-app.war
ENV PORT=8085
USER demouser
EXPOSE $PORT
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget --spider -q http://localhost:$PORT/ || exit 1
ENTRYPOINT ["java","-jar"]
CMD ["demo-app.war"]

