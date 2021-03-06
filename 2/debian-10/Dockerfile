FROM debian:buster

# jenkins version being bundled in this docker image
ARG JENKINS_VERSION
ENV JENKINS_VERSION ${JENKINS_VERSION:-2.249.1}

# jenkins.war checksum, download will be validated using it
ARG JENKINS_SHA=a38ca485b3e2bae6ec69a34b6b34b674f4dd4baf04f20f8dce2d838d08ed5bd6
                
# Can be used to customize where jenkins.war get downloaded from
ARG JENKINS_URL=https://repo.jenkins-ci.org/public/org/jenkins-ci/main/jenkins-war/${JENKINS_VERSION}/jenkins-war-${JENKINS_VERSION}.war

ENV BITNAMI_PKG_CHMOD="-R g+rwX" \
    HOME="/" \
    PATH="/opt/bitnami/java/bin:/opt/bitnami/git/bin:/opt/bitnami/common/bin:/opt/bitnami/nami/bin:$PATH"

COPY prebuildfs /
# Install required system packages and dependencies
RUN install_packages ca-certificates curl fontconfig fonts-dejavu-extra gzip libc6 libcom-err2 libcurl4 libffi6 libgcc1 libgcrypt20 libgmp10 libgnutls30 libgpg-error0 libgssapi-krb5-2 libhogweed4 libidn2-0 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 libldap-2.4-2 libnettle6 libnghttp2-14 libp11-kit0 libpsl5 librtmp1 libsasl2-2 libssh2-1 libssl1.1 libtasn1-6 libunistring2 openssh-client procps sudo tar unzip zlib1g
RUN /build/bitnami-user.sh
RUN /build/install-nami.sh

RUN apt-get update && apt-get upgrade -y

# RUN bitnami-pkg install java-1.8.265-0 --checksum 6c85e8df954fca444f2471195524db015ec24a67e42f43579401f2adb5189cb8
RUN apt-get install -y default-jdk

# RUN bitnami-pkg install git-2.28.0-0 --checksum bda714065995df169ab17f11b3a638a106aec83b00bcbfb325b36b51f29d1704
RUN apt-get install -y git

# RUN bitnami-pkg install tini-0.19.0-0 --checksum 9a8ae20be31a518f042fcec359f2cf35bfdb4e2a56f2fa8ff9ef2ecaf45da80c
RUN apt-get install -y tini

# RUN bitnami-pkg unpack jenkins-2.249.1-0 --checksum c3a1d8e29cdf0aeeaef8fc908d38eb74f3976dae1d655ae344f0a23d8735f901

# could use ADD but this one does not check Last-Modified header neither does it allow to control checksum
# see https://github.com/docker/docker/issues/8331
RUN mkdir -p /opt/bitnami/jenkins/
RUN curl -fsSL ${JENKINS_URL} -o /opt/bitnami/jenkins/jenkins.war \
  && echo "${JENKINS_SHA}  /opt/bitnami/jenkins/jenkins.war" | sha256sum -c -

# RUN bitnami-pkg install gosu-1.12.0-2 --checksum 4d858ac600c38af8de454c27b7f65c0074ec3069880cb16d259a6e40a46bbc50
RUN apt-get install -y gosu

RUN rm -r /var/lib/apt/lists /var/cache/apt/archives

RUN mkdir -p /usr/share/jenkins/ref

COPY rootfs /

# Add jenkins user
RUN cat /opt/bitnami/jenkins/nss_group | tee /etc/group
RUN cat /opt/bitnami/jenkins/nss_passwd | tee /etc/passwd
RUN chown 1001:0 /opt/bitnami/jenkins/ && chmod 777 /opt/bitnami/jenkins/

RUN ln -sf /install-plugins.sh /usr/local/bin/install-plugins.sh
ENV BITNAMI_APP_NAME="jenkins" \
    BITNAMI_IMAGE_VERSION="2.249.1" \
    DISABLE_JENKINS_INITIALIZATION="no" \
    JAVA_OPTS="" \
    JENKINS_HOME="/opt/bitnami/jenkins/jenkins_home" \
    JENKINS_PASSWORD="bitnami" \
    JENKINS_USERNAME="user" \
    LNAME="jenkins" \
    NAMI_PREFIX="/.nami" \
    NSS_WRAPPER_GROUP="/opt/bitnami/jenkins/nss_group" \
    NSS_WRAPPER_PASSWD="/opt/bitnami/jenkins/nss_passwd" \
    OS_ARCH="amd64" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="linux"

EXPOSE 8080 8443 50000

USER 1001
ENTRYPOINT [ "/app-entrypoint.sh" ]
CMD [ "/run.sh" ]
