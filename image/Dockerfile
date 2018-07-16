
FROM cirepo/nix:2.0.4-alpine-3.7


USER root


ARG IMAGE_ARG_FILESERVER

# see: http://www.oracle.com/technetwork/java/javase/downloads/jdk10-downloads-4416644.html
# http://download.oracle.com/otn-pub/java/jdk/10.0.1+10/fb4372174a714e6b8c52526dc134031e/jdk-10.0.1_linux-x64_bin.tar.gz
ARG IMAGE_ARG_JAVA10_VERSION
ARG IMAGE_ARG_JAVA10_VERSION_BUILD
ARG IMAGE_ARG_JAVA10_PACKAGE_DIGEST


ENV JDK10_HOME /usr/lib/jvm/java-10-oracle
ENV JRE10_HOME /usr/lib/jvm/java-10-oracle-jre
ENV JAVA_HOME ${JDK10_HOME}
ENV PATH ${JDK10_HOME}/bin:${PATH}


COPY --from=cirepo/glibc:2.25-r0-alpine-3.7-archive /data/root /


COPY --chown=root:root docker /data


RUN set -ex \
  && echo ===== Install JDK10 ===== \
  && JDK_ARCHIVE="jdk-${IMAGE_ARG_JAVA10_VERSION:-10.0.1}_linux-x64_bin.tar.gz" \
  && if [ ! -f /data/${JDK_ARCHIVE} ]; then \
       ${ARIA2C_DOWNLOAD} --header="Cookie: oraclelicense=accept-securebackup-cookie" -d /data \
         -o ${JDK_ARCHIVE} \
         ${IMAGE_ARG_FILESERVER:-http://download.oracle.com}/otn-pub/java/jdk/${IMAGE_ARG_JAVA10_VERSION:-10.0.1}+${IMAGE_ARG_JAVA10_VERSION_BUILD:-10}/${JDK_ARCHIVE}; \
     fi \
  && tar -xzf /data/${JDK_ARCHIVE} -C /data && mkdir -p $(dirname ${JDK10_HOME}) && mv /data/jdk-${IMAGE_ARG_JAVA10_VERSION:-10.0.1} ${JDK10_HOME} \
  && rm -f ${JDK10_HOME}/lib/src.zip \
  && rm -f /data/${JDK_ARCHIVE} && rm -rf /data/jdk* \
  && JAVA_VERSION=$(java -version 2>&1 | awk -F '"' '/version/ {print $2}') \
  && echo OSX style JAVA_HOME is /Library/Java/JavaVirtualMachines/jdk-${JAVA_VERSION}.jdk/Contents/Home \
  && mkdir -p /Library/Java/JavaVirtualMachines/jdk-${JAVA_VERSION}.jdk/Contents \
  && ln -s ${JDK10_HOME} /Library/Java/JavaVirtualMachines/jdk-${JAVA_VERSION}.jdk/Contents/Home \
  && echo AppVeyor style JAVA_HOME \
  && ln -s ${JDK10_HOME} /usr/lib/jvm/java-10-openjdk-amd64 \
  && echo ===== Export jre  ===== \
  && cd $(dirname ${JDK10_HOME}) \
  && ${JDK10_HOME}/bin/jlink -p ${JDK10_HOME}/jmods --add-modules java.base,java.sql,java.naming,java.desktop,java.management,java.security.jgss,java.instrument,java.rmi,java.xml.bind,java.xml.ws,java.xml.ws.annotation --output java-10-oracle-jre \
  && rm -rf /tmp/* /var/cache/apk/* \
  && echo OSX style JAVA_HOME is /Library/Java/JavaVirtualMachines/jre-${JAVA_VERSION}.jre/Contents/Home \
  && mkdir -p /Library/Java/JavaVirtualMachines/jre-${JAVA_VERSION}.jre/Contents \
  && ln -s ${JRE10_HOME} /Library/Java/JavaVirtualMachines/jre-${JAVA_VERSION}.jre/Contents/Home \
  && echo AppVeyor style JAVA_HOME \
  && ln -s ${JRE10_HOME} /usr/lib/jvm/java-10-openjdk-amd64-jre

ENV JAVA_HOME ${JDK10_HOME}


USER alpine
