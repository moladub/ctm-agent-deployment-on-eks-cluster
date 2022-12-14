#------------------------------------
# Control-M/Agent docker container
#------------------------------------

FROM centos:7


ARG AGENT_IMAGE_NAME
ARG USER
ARG PASSWORD

LABEL Description="This is a Control-M/ Agent image that will be used in a kubernetes enviroment"

RUN  yum -y install wget \
        && yum -y install telnet \
        && yum -y install unzip \
        && yum -y install sudo \
        && yum -y install net-tools \
        && yum -y install which \
        && yum -y install zlib-devel \
        && yum -y install libffi-devel \
		&& yum -y install compat-libstdc++-33.x86_64 \
		&& yum -y install psmisc \
		&& cd /usr/src \
# install nodejs
    && curl -k --silent --location https://rpm.nodesource.com/setup_12.x | bash - \
	&& yum -y install nodejs \
	&& node -v \
	&& npm -v \
# add controlm user and root to soduers list
	&& useradd -d /home/controlm -p controlm -m controlm \
	&& echo 'root ALL=(ALL) ALL' >> /etc/sudoers \
	&& echo 'controlm ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers \
# install ctm-automation-api kit
    && mkdir /root/ctm-automation-api \
	&& cd /root/ctm-automation-api \
	&& curl --silent -k -O $AAPI_END_POINTctm-cli.tgz \
	&& npm install -g ctm-cli.tgz \
	&& ctm -v \
	&& yum clean all \
    && rm -rf /var/cache/yum

# add controlm endpoint
USER controlm
# copy run and register controlm agent script to container
COPY run_register_controlm.sh /tmp
COPY decommission_controlm.sh /tmp

RUN ctm env add endpoint $AAPI_END_POINT $USER $PASSWORD \
	&& ctm env set endpoint \
# install java 8 
	&& sudo yum -y install java-11-openjdk \ 
# provision controlm agent image
	&& cd \
    && ctm provision images Linux \
	&& ctm provision image $AGENT_IMAGE_NAME && echo installation ended successfully \
# enable controlm agent utilities
	&& echo "source /home/controlm/.bash_profile" >> /home/controlm/.bashrc \
    && cp /tmp/run_register_controlm.sh /home/controlm \
	&& cp /tmp/decommission_controlm.sh /home/controlm \
	&& chmod +x run_register_controlm.sh \
	&& chmod +x decommission_controlm.sh \
# uninstall java 8 
#	&& sudo yum -y autoremove java-11-openjdk \
	&& sudo yum clean all \
    && sudo rm -rf /var/cache/yum

EXPOSE 7000-8000

CMD ["/home/controlm/run_register_controlm.sh"]