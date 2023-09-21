FROM balenalib/raspberrypi3-debian:latest as base

#ARG REPO=https://github.com/mainsail-crew/crowsnest.git
#ARG VERSION=master

RUN install_packages git make

WORKDIR /opt

RUN git clone https://github.com/bdkuhman/crowsnest.git crowsnest \
  && cd crowsnest \
  && git checkout master \
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
 && echo -n "moonraker ALL = NOPASSWD: /bin/systemctl, /sbin/shutdown, /sbin/reboot, `which make`" > /etc/sudoers.d/moonraker

## Start Moonraker
USER crowsnest

WORKDIR /opt/crowsnest
RUN sudo make install


EXPOSE 8080
VOLUME ["/opt/printer_data/run", "/opt/printer_data/gcodes", "/opt/printer_data/logs", "/opt/printer_data/database", "/opt/printer_data/config"]
ENTRYPOINT ["/opt/crowsnest/crowsnest"]
CMD ["-c", "/opt/printer_data/config/"]

