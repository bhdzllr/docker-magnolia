FROM openjdk:8-slim

# Environment
ARG CONF_CONTEXT_FILE
ARG CONF_PROPERTIES_FILE
ENV TOMCAT_DOWNLOAD_URL https://www.apache.org/dist/tomcat/tomcat-9/v9.0.43/bin/apache-tomcat-9.0.43.tar.gz
ENV CATALINA_HOME /opt/tomcat
ENV CATALINA_TMPDIR /tmp/tomcat
ENV DEPLOYMENT_DIR $CATALINA_HOME/webapps
ENV MGNL_WAR https://nexus.magnolia-cms.com/content/repositories/magnolia.public.releases//info/magnolia/bundle/magnolia-community-webapp/6.2.6/magnolia-community-webapp-6.2.6.war

# Create directories
RUN mkdir /data && \
	mkdir /data/download && \
	mkdir /data/magnolia

# Map volume out of container
VOLUME /data/magnolia

# Install wget and curl
RUN apt-get update && apt-get install -y --no-install-recommends \
	wget \
	curl

# Install Tomcat
RUN wget -q ${TOMCAT_DOWNLOAD_URL} -O /data/download/apache-tomcat.tar.gz && \
    tar -xf /data/download/apache-tomcat.tar.gz && \
    mv apache-tomcat* ${CATALINA_HOME}
COPY ${CONF_CONTEXT_FILE} ${CATALINA_HOME}/conf/context.xml
RUN chmod +x ${CATALINA_HOME}/bin/*sh

# Fix for JVM core dump
RUN ulimit -c unlimited

# Make sure that the temporary directory exists
RUN mkdir -p ${CATALINA_TMPDIR}

# Remove all webapps from the default Tomcat installation
RUN rm -rf ${DEPLOYMENT_DIR}/*

# JVM parameters
ENV CATALINA_OPTS -Xms64M -Xmx2048M -Djava.awt.headless=true

# Copy Magnolia properties
COPY ${CONF_PROPERTIES_FILE} ${CATALINA_HOME}/conf/

# Install magnolia WAR files and remove download folder
RUN wget -q ${MGNL_WAR} -O /data/download/magnolia.war && \
	cp /data/download/magnolia.war ${DEPLOYMENT_DIR}/ROOT.war
RUN rm -rf /data/download

# Start Tomcat
ENTRYPOINT ["/opt/tomcat/bin/catalina.sh", "run"]
CMD [""]

# Expose port
EXPOSE 8080
