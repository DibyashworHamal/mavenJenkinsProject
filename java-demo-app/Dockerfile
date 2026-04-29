FROM maven:3.9-eclipse-temurin-21 AS builder
WORKDIR /app
ADD */*/*.war /app
LABEL maintainer="Divyashwor Hamal" \
        version="1.0.0" \
        description="Java Demo App"
RUN addgroup -S demogroup && adduser -S demouser -G demogroup
ENV PORT=8085
USER demouser
EXPOSE $PORT
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget --spider -q http://localhost:$PORT/ || exit 1
ENTRYPOINT ["java","-jar"]
CMD ["demo-app.war"]

