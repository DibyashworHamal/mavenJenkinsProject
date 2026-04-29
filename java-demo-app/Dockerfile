FROM tomcat:9.0-jdk11-openjdk-slim
ENV PORT=8085
WORKDIR /usr/local/tomcat/webapps/
RUN rm -rf ./ROOT && rm -rf ./examples
ADD */*/*.war ./ROOT.war
LABEL maintainer="Divyashwor Hamal" \
        version="1.0.0" \
        description="Java Demo App"
RUN addgroup -r demogroup && adduser -S -G demogroup demouser && \
    chown -R demouser:demogroup /usr/local/tomcat
USER demouser
EXPOSE $PORT
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget --spider -q http://localhost:$PORT/ || exit 1
CMD ["catalina.sh","run"]
