FROM centos:7

ENV DOCKER=true
RUN yum -y --setopt="tsflags=nodocs" install epel-release && \
    yum -y --setopt="tsflags=nodocs" upgrade && \
    yum -y --setopt="tsflags=nodocs" install bc \
                                             coreutils \
                                             epel-release \
                                             expect \
                                             findutils \
                                             git \
                                             gnupg \
                                             mock \
                                             rpm-build \
                                             rpm-sign \
                                             spectool \
                                             sudo \
                                             yum-utils && \
    yum clean all && \
    rm -rf /var/cache/yum/

RUN adduser -u 1000 -G mock -U -m centos && \
    echo 'centos ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    mkdir -p /home/centos/cache /home/centos/rpmbuild/{BUILD,RPMS,SOURCES,SPECS,SRPMS} && \
    echo '%_topdir %(echo $HOME)/rpmbuild' > ~/.rpmmacros && \
    chmod g+w /etc/mock/*.cfg && \
    echo "config_opts['use_nspawn'] = False" >> /etc/mock/site-defaults.cfg && \
    echo "config_opts['cache_topdir'] = '/home/centos/cache/mock'" >> /etc/mock/site-defaults.cfg

WORKDIR /home/centos/rpmbuild
VOLUME /tmp/repository
VOLUME /home/centos/cache

COPY ./vendor/manage /home/centos/rpmbuild/manage
COPY ./.manage.yml /home/centos/rpmbuild/.manage.yml
COPY ./scripts /home/centos/rpmbuild/scripts

RUN chown centos:centos -R /home/centos && install -g mock -m 2775 -d /home/centos/cache/mock

USER centos
RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-key 13F26F82E955B8B8CE469054F29CCEBC83FD4525
ENTRYPOINT ["/home/centos/rpmbuild/manage/manage"]
CMD ["build"]