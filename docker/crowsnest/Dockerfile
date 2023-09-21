FROM balenalib/raspberrypi3-debian:latest as base

#ARG REPO=https://github.com/mainsail-crew/crowsnest.git
#ARG VERSION=master

RUN install_packages git make

WORKDIR /home/pi

RUN git clone https://github.com/mainsail-crew/crowsnest.git

# RUN cd crowsnest

# RUN echo -n "pi ALL = NOPASSWD: /bin/systemctl, /sbin/shutdown, /sbin/reboot, `which make`" > /etc/sudoers.d/pi

# RUN cat /etc/passwd
WORKDIR /home/pi/crowsnest
RUN sudo make install