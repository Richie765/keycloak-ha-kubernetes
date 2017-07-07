FROM jboss/keycloak-mysql:3.2.0.Final@sha256:29a391d7ecb6003d7c7caf2196321f6a24dc6430c124a52c598f76a3cba11ecc

CMD ["-b", "0.0.0.0", "--server-config", "standalone-ha.xml"]

RUN rm keycloak/standalone/configuration/standalone.xml

ADD xsl-transform.sh /usr/local/bin/xsl-transform

ADD *.xsl /tmp/

RUN xsl-transform /opt/jboss/keycloak/standalone/configuration/standalone-ha.xml /tmp/jgroups-jdbc.xsl \
  && xsl-transform /opt/jboss/keycloak/standalone/configuration/standalone-ha.xml /tmp/node-identifier.xsl
