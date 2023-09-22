FROM maven:3.6.3-jdk-11 as setup


# Environment variables
ENV EXTRA_JAVA_OPTS="-Xms256m -Xmx1g"
ENV CORS_ENABLED=$CORS_ENABLED
ENV CORS_ALLOWED_ORIGINS=$CORS_ALLOWED_ORIGINS
ENV CORS_ALLOWED_METHODS=$CORS_ALLOWED_METHODS
ENV CORS_ALLOWED_HEADERS=$CORS_ALLOWED_HEADERS
ENV DEBIAN_FRONTEND=noninteractive


RUN  apt-get update \
  && apt-get install -y wget \
  && apt-get install -y zip \
  && apt-get install -y curl \
  && apt-get install -y git \
  && rm -rf /var/lib/apt/lists/*


WORKDIR /opt/

# cleanup
RUN apt purge -y  \
&& apt autoremove --purge -y \
&& rm -rf /tmp/*

# 6- Define environmental variables required by Maven, like Maven_Home directory and where the maven repo is located
ENV MAVEN_HOME /usr/share/maven
ENV MAVEN_CONFIG "$USER_HOME_DIR/.m2"

FROM setup as download

WORKDIR /tmp

RUN echo "Download and build the immudb plugin...." \
&& wget https://github.com/taba90/gt-immudb/archive/refs/heads/main.zip \
&& unzip ./main.zip \
&& mvn -f ./gt-immudb-main/pom.xml clean install

RUN echo "Download and build the location-registry app...." \
&& git clone https://github.com/taba90/location-registry.git \
&& mvn -f ./location-registry/pom.xml clean install


FROM download as install

WORKDIR /opt

RUN echo "Installing location-registry"

COPY --from=download /tmp/location-registry/target/location-registry*.jar /opt/location-registry.jar

ENTRYPOINT ["java","-jar","/opt/location-registry.jar"]
