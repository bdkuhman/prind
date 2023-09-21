FROM balenalib/raspberrypi3-debian:latest as base

#ARG REPO=https://github.com/mainsail-crew/crowsnest.git
#ARG VERSION=master

RUN install_packages git make

WORKDIR /opt

RUN git clone https://github.com/bdkuhman/crowsnest.git crowsnest \
  && cd crowsnest \
  && git checkout sysctl_skip \
  && rm -rf .git

WORKDIR /opt

RUN groupadd crowsnest --gid 1000 \
 && useradd crowsnest --uid 1000 --gid crowsnest \
 && usermod crowsnest --append --groups dialout
RUN mkdir -p printer_data/run printer_data/gcodes printer_data/logs printer_data/database printer_data/config \
 && chown -R crowsnest:crowsnest /opt/*

# COPY --chown=crowsnest:crowsnest --from=build /opt/moonraker ./moonraker
# COPY --chown=crowsnest:crowsnest --from=build /opt/venv ./venv



RUN ln -s /bin/systemctl /sbin/shutdown \
 && ln -s /bin/systemctl /sbin/reboot \
 && echo -n "crowsnest ALL = NOPASSWD: /bin/systemctl, /sbin/shutdown, /sbin/reboot, `which make`" > /etc/sudoers.d/crowsnest

#detect rpi
RUN touch /boot/config.txt
RUN touch /etc/rpi-issue


## Start Moonraker
USER crowsnest

WORKDIR /opt/crowsnest


RUN touch tools/.config
RUN echo "CROWSNEST_UNATTENDED=1" >> tools/.config
RUN echo "CROWSNEST_CONFIG_PATH=/opt/printer_data/config/crowsnest.config" >> tools/.config
RUN echo "CN_CONFIG_ROOTPATH=/opt/printer_data" >> tools/.config
RUN echo "CROWSNEST_LOG_PATH=/opt/printer_data/logs/crowsnest.log" >> tools/.config
RUN echo "CROWSNEST_ENV_PATH=/opt/printer_data/systemd" >> tools/.config
RUN echo "CROWSNEST_USTREAMER_REPO_SHIP=https://github.com/pikvm/ustreamer.git" >> tools/.config
RUN echo "CROWSNEST_USTREAMER_REPO_BRANCH=master"  >> tools/.config
RUN echo "CROWSNEST_CAMERA_STREAMER_REPO_SHIP=https://github.com/ayufan/camera-streamer.git"  >> tools/.config
RUN echo "CROWSNEST_CAMERA_STREAMER_REPO_BRANCH=master"  >> tools/.config
RUN echo "SYSCTL_SKIP=1" >> tools/.config

RUN cat tools/.config


RUN sudo make install


EXPOSE 8080
VOLUME ["/opt/printer_data/run", "/opt/printer_data/gcodes", "/opt/printer_data/logs", "/opt/printer_data/database", "/opt/printer_data/config"]
ENTRYPOINT ["/opt/crowsnest/crowsnest"]
CMD ["-c", "/opt/printer_data/config/"]

