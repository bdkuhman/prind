## Get Code and Build venv
# FROM python:3-bookworm as build

# ARG REPO=https://github.com/Arksine/moonraker
# ARG VERSION=master

# WORKDIR /opt

# RUN git clone ${REPO} moonraker \
#  && cd moonraker \
#  && git checkout ${VERSION} \
#  && rm -rf .git

# RUN python -m venv venv \
#  && venv/bin/pip install -r moonraker/scripts/moonraker-requirements.txt

## Runtime Image
FROM python:3-slim-bookworm as run

RUN apt update \
 && apt install -y \
      libopenjp2-7 \
      python3-libgpiod \
      curl \
      libcurl4 \
      libssl3 \
      liblmdb0 \
      libsodium23 \
      libjpeg62-turbo \
      libtiff6 \
      libxcb1 \
      zlib1g \
      iproute2 \
      systemd \
      sudo \
      git \
 && apt clean

WORKDIR /opt

RUN git clone https://github.com/Arksine/moonraker.git

WORKDIR /opt

RUN mkdir "/opt/venv"
ENV MOONRAKER_VENV=/opt/venv

RUN groupadd moonraker --gid 1000 \
 && useradd moonraker --uid 1000 --gid moonraker \
 && usermod moonraker --append --groups dialout
RUN mkdir -p printer_data/run printer_data/gcodes printer_data/logs printer_data/database printer_data/config \
 && chown -R moonraker:moonraker /opt/*

# COPY --chown=moonraker:moonraker --from=build /opt/moonraker ./moonraker
# COPY --chown=moonraker:moonraker --from=build /opt/venv ./venv

# RUN GET_PIP="/opt/venv/get-pip.py"
# RUN curl https://bootstrap.pypa.io/pip/3.6/get-pip.py -o ${GET_PIP}
# RUN python ${GET_PIP}
# RUN rm ${GET_PIP}

RUN mkdir /opt/venv/bin
RUN ln -s `which pip` /opt/venv/bin/pip

RUN ln -s /bin/systemctl /sbin/shutdown \
 && ln -s /bin/systemctl /sbin/reboot \
 && echo -n "moonraker ALL = NOPASSWD: /bin/systemctl, /sbin/shutdown, /sbin/reboot, /usr/bin/apt-get" > /etc/sudoers.d/moonraker


## Start Moonraker
USER moonraker

WORKDIR /opt/moonraker/scripts
RUN ./install-moonraker.sh -d /opt/printer_data

EXPOSE 7125
VOLUME ["/opt/printer_data/run", "/opt/printer_data/gcodes", "/opt/printer_data/logs", "/opt/printer_data/database", "/opt/printer_data/config"]
WORKDIR /opt
ENTRYPOINT ["/opt/venv/bin/python", "moonraker/moonraker/moonraker.py"]
CMD ["-d", "/opt/printer_data"]

