FROM centos:centos6

MAINTAINER soonwoong.lee@gmail.com

# base system upgrade and system dependencies
RUN yum upgrade -y && \
    yum install -y epel-release && \
    yum install -y \
      gcc-c++ automake make \
      apr-devel expat-devel rrdtool-devel zlib-devel \
      httpd pop rsync wget tar git && \
    yum clean all

RUN yum install -y tar unzip openssh-server openssh-clients && \
    mkdir /var/run/sshd && \
    echo 'root:root' |chpasswd && \
    sed -ri 's/^PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config

RUN yum install -y ganglia-devel ganglia-gmetad ganglia-gmond ganglia-gmond-python ganglia-web

COPY ganglia-web-3.5.10.tar /usr/src/ganglia-web-3.5.10.tar
RUN cd /usr/src && \
    tar xf ./ganglia-web-3.5.10.tar && \
    mv ./ganglia-web-3.5.10 /var/www/html/ganglia && \
    rm ./ganglia-web-3.5.10.tar && \
    cd /var/www/html/ganglia && \
    make install

# ganglia python plugin for Nvidia GPU Monitoring
RUN cd /root && \
    git clone https://github.com/ganglia/gmond_python_modules.git && \
    cd ./gmond_python_modules/gpu/nvidia && \
    cp ./python_modules/nvidia.py /usr/lib64/ganglia/python_modules/ && \
    cp ./conf.d/nvidia.pyconf /etc/ganglia/conf.d/ && \
    cp ./graph.d/* /var/www/html/ganglia/graph.d/

# add the ganglia user and group
# RUN useradd -r -U -d /var/lib/ganglia -s /bin/false ganglia

# create the default gmond config file, with the default gmetad cluster name
RUN gmond -t \
    | sed 's/name = "unspecified"/name = "my cluster"/' \
    > /etc/ganglia/gmond.conf

# add the start script
ADD start.sh start.sh

EXPOSE 22
# CMD ["/usr/sbin/sshd", "-D"]

# entrypoint is the start script
# ENTRYPOINT ["bash","start.sh"]

# default is with gmond for seeing something
# CMD ["--with-gmond"]
