FROM httpd:alpine
#FROM mybyu/apache/jdk7
#FROM httpd
MAINTAINER Lauren Anderson <laurenra@byu.edu>
#EXPOSE 80 443
WORKDIR /usr/local/apache2

#ENV JAVA_HOME /usr/lib/jvm/default

# Set timezone to Mountain Time (Denver)
RUN apk add -U tzdata && cp /usr/share/zoneinfo/America/Denver /etc/localtime

# Apache setup files.
COPY ["conf/httpd.conf", "/usr/local/apache2/conf/"]
COPY ["conf/extra/httpd-ssl.conf", "/usr/local/apache2/conf/extra/"]
COPY ["conf/balancer.d/ht-balancer.conf", "/usr/local/apache2/conf/balancer.d/"]
COPY ["conf/vhost.d/ht-vhost.conf", "/usr/local/apache2/conf/vhost.d/"]

# Convenient scripts.
COPY ["scripts/setprompt", "scripts/setll", "/usr/local/bin/"]

#RUN mkdir -p /opt/tomcat/shared/lib
#RUN mkdir -p /usr/share/wars

VOLUME ["/usr/local/apache2/conf/certs"]
VOLUME ["/usr/local/apache2/htdocs"]
