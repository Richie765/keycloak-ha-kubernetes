FROM jboss/keycloak-mysql:3.2.1.Final@sha256:8360d917402dcd2d69523ffb2d6fec11ee732fd932e214e685024d0ad68c4f19

CMD ["-b", "0.0.0.0", "--server-config", "standalone-ha.xml"]

# http://www.keycloak.org/docs/3.2/server_installation/topics/clustering/load-balancer.html
RUN sed -i -e 's/<http-listener /& proxy-address-forwarding="${env.PROXY_ADDRESS_FORWARDING}" /' $JBOSS_HOME/standalone/configuration/standalone-ha.xml

# http://www.keycloak.org/docs/3.2/server_installation/topics/cache/replication.html
ENV CACHE_OWNERS 1
RUN sed -i -e 's!<distributed-cache name="sessions" mode="SYNC" owners="1"/>!<distributed-cache name="sessions" mode="SYNC" owners="${env.CACHE_OWNERS}"/>!; s!<distributed-cache name="loginFailures" mode="SYNC" owners="1"/>!<distributed-cache name="loginFailures" mode="SYNC" owners="${env.CACHE_OWNERS}"/>!' $JBOSS_HOME/standalone/configuration/standalone-ha.xml

# Set welcome theme
# http://www.keycloak.org/docs/3.2/server_development/topics/themes.html
ENV WELCOME_THEME keycloak
RUN sed -i -e 's!<theme>!<theme>\n                <welcomeTheme>${env.WELCOME_THEME}</welcomeTheme>!' $JBOSS_HOME/standalone/configuration/standalone-ha.xml

# Template caching
# http://www.keycloak.org/docs/3.2/server_development/topics/themes.html
ENV STATIC_MAXAGE 2592000
ENV CACHE_THEMES true
ENV CACHE_TEMPLATES true
RUN sed -i -r 's!<staticMaxAge>[[:digit:]]+</staticMaxAge>!<staticMaxAge>${env.STATIC_MAXAGE}</staticMaxAge>!' $JBOSS_HOME/standalone/configuration/standalone-ha.xml
RUN sed -i -r 's!<cacheThemes>[[:alpha:]]+</cacheThemes>!<cacheThemes>${env.CACHE_THEMES}</cacheThemes>!' $JBOSS_HOME/standalone/configuration/standalone-ha.xml
RUN sed -i -r 's!<cacheTemplates>[[:alpha:]]+</cacheTemplates>!<cacheTemplates>${env.CACHE_TEMPLATES}</cacheTemplates>!' $JBOSS_HOME/standalone/configuration/standalone-ha.xml


RUN rm keycloak/standalone/configuration/standalone.xml

ADD xsl-transform.sh /usr/local/bin/xsl-transform

ADD *.xsl /tmp/

RUN xsl-transform /opt/jboss/keycloak/standalone/configuration/standalone-ha.xml /tmp/jgroups-jdbc.xsl \
  && xsl-transform /opt/jboss/keycloak/standalone/configuration/standalone-ha.xml /tmp/node-identifier.xsl
