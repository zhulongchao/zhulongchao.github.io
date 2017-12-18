docker run -d -p 4000:4000 -p 1026:22 --name desktop -e PASSWORD=password -e USER=user --cap-add=SYS_PTRACE cesarandreslopez/docker-ubuntu-mate-desktop-nomachine

libmpg123-0 libopenal1

nvidia-docker run -d --device=/dev/dri:/dev/dri 10.4.65.226/library/vnc-nvidia:1.0.0 /root/run-nv

x11docker

nvidia-docker run -it  --rm --env="DISPLAY"  --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw"  --volume="/usr/lib/x86_64-linux-gnu/libXv.so.1:/usr/lib/x86_64-linux-gnu/libXv.so.1"  plumbee/nvidia-virtualgl:2.5.2 vglrun bash

nvidia-docker run --volume="/tmp/.X11-unix/X0:/tmp/.X11-unix/X0:rw" --volume="/usr/lib/x86_64-linux-gnu/libXv.so.1:/usr/lib/x86_64-linux-gnu/libXv.so.1" -p 3101:3101/udp -p 5931:5901 -p 5801:5801 -ti -rm bn2302/torcs

安装vnc
http://www.liliangqi.com/2017/10/17/vnc-multiuser/

vnc和nomachine 双配置

iptables -t nat -A  DOCKER -p tcp --dport 5919 -j DNAT --to-destination 172.17.0.8:5901

docker commit a059c9d028f6 10.4.65.226/library/ubuntu-nxserver:1.0.1

 wget http://download.nomachine.com/download/4.6/Linux/nomachine-cloud-server-evaluation_4.6.4_14_x86_64.tar.gz -P /tmp \
	&& tar -zxvf /tmp/nomachine-cloud-server-evaluation_4.6.4_14_x86_64.tar.gz -C /usr \
	&& /usr/NX/nxserver --install \

/usr/NX/scripts/init/nxserver start

adduser username


ENV VIRTUALGL_VERSION 2.5.2

# install VirtualGL
RUN apt-get update && apt-get install -y --no-install-recommends \
    libglu1-mesa-dev mesa-utils curl ca-certificates xterm && \
    curl -sSL https://downloads.sourceforge.net/project/virtualgl/"${VIRTUALGL_VERSION}"/virtualgl_"${VIRTUALGL_VERSION}"_amd64.deb -o virtualgl_"${VIRTUALGL_VERSION}"_amd64.deb && \
    dpkg -i virtualgl_*_amd64.deb && \
    /opt/VirtualGL/bin/vglserver_config -config +s +f -t && \
    rm virtualgl_*_amd64.deb && \
    apt-get clean && \
    apt-get remove -y curl ca-certificates && \
    rm -rf /var/lib/apt/lists/*
