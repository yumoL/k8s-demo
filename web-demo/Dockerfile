FROM yumol/tomcat:8.0.51-alpine

COPY target/ROOT /usr/local/tomcat/webapps/ROOT

COPY start.sh /usr/local/tomcat/bin/start.sh

ENTRYPOINT ["sh", "/usr/local/tomcat/bin/start.sh"]