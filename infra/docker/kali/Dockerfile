FROM kalilinux/kali-rolling:latest

RUN apt update && \
    DEBIAN_FRONTEND=noninteractive apt install -y kali-desktop-xfce locales sudo

RUN apt update && \
    DEBIAN_FRONTEND=noninteractive apt install -y xrdp tightvncserver && \
    adduser xrdp ssl-cert && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=en_US.UTF-8

ARG USER=user
ARG PASS=password

RUN useradd -m $USER -p $(openssl passwd $PASS) && \
    usermod -aG sudo $USER && \
    chsh -s /bin/bash $USER

RUN echo "#!/bin/sh\n\
    export XDG_SESSION_DESKTOP=xfce\n\
    export XDG_SESSION_TYPE=x11\n\
    export XDG_CURRENT_DESKTOP=XFCE\n\
    export XDG_CONFIG_DIRS=/etc/xdg/xdg-xfce:/etc/xdg\n\
    exec dbus-run-session -- xfce4-session" > /xstartup && chmod +x /xstartup

RUN mkdir /home/$USER/.vnc && \
    echo $PASS | tightvncpasswd -f > /home/$USER/.vnc/passwd && \
    chmod 0600 /home/$USER/.vnc/passwd && \
    chown -R $USER:$USER /home/$USER/.vnc

RUN cp -f /xstartup /etc/xrdp/startwm.sh && \
    cp -f /xstartup /home/$USER/.vnc/xstartup

RUN echo "#!/bin/sh\n\
    sudo -u $USER -g $USER -- tightvncserver :1" > /startvnc && chmod +x /startvnc
