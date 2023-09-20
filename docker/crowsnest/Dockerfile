FROM balenalib/raspberrypi3-debian:latest

RUN install_packages git make

WORKDIR /home/pi

RUN git clone https://github.com/mainsail-crew/crowsnest.git

RUN cd ~/crowsnest

RUN echo -n "moonraker ALL = NOPASSWD: /bin/systemctl, /sbin/shutdown, /sbin/reboot, `which make`" > /etc/sudoers.d/moonraker

USER pi

RUN sudo make install