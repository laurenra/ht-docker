FROM mysql
MAINTAINER Lauren Anderson <laurenra@byu.edu>
#EXPOSE 8009
WORKDIR /opt/mysql

# Set timezone to Mountain Time (Denver)
# Need to add apk, then remove it?
#RUN apk add -U tzdata && cp /usr/share/zoneinfo/America/Denver /etc/localtime

ENV MYSQL_ROOT_PASSWORD=changeme

# COPY [ "README.md", "/opt/tomcat/"]

# Tomcat setup files. These point to files external to the container for sensitive setup information
#COPY ["conf/setenv.sh", "/opt/tomcat/bin/"]
#COPY ["conf/catalina.properties", "conf/context.xml", "conf/server.xml", "conf/tomcat-users.xml", "/opt/tomcat/conf/"]
#COPY ["jdbc/ojdbc6.jar", "/opt/tomcat/lib/"]
#COPY ["wars/*.jar", "/opt/tomcat/shared/lib/"]

# Convenient scripts.
COPY ["scripts/setprompt", "scripts/setll", "/usr/local/bin/"]

#RUN mkdir -p /opt/mysql/private

VOLUME ["/opt/mysql/private"]
#VOLUME ["/opt/tomcat/shared/lib"]
#VOLUME ["/usr/share/wars"]
#VOLUME ["/opt/tomcat/webapps"]
