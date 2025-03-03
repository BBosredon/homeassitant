FROM ubuntu:25.04

# Install packages
RUN apt update && apt install -y --no-install-recommends --no-install-suggests \
    cups \
    cups-pdf \
    inotify-tools \
    python3-cups \
    avahi-daemon \
    hplip \
    hplip-data \
    foomatic-db-compressed-ppds \
    printer-driver-foo2zjs \
    printer-driver-foo2zjs-common \
    whois \
    dbus

# Expose port and volumes
EXPOSE 631/tcp
VOLUME /config
VOLUME /services

# Add files
ADD scripts /scripts

# Add user and disable sudo password checking
RUN useradd \
    --groups sudo,lp,lpadmin \
    --create-home \
    --home /home/print \
    --shell /bin/bash \
    -p $(mkpasswd print) \
    print

# Copy the default configuration file
COPY --chown=root:lp cupsd.conf /etc/cups/cupsd.conf

# Cleanup
RUN apt purge -y whois \
    && apt autoremove --purge -y \
    && apt clean all -y \
    && rm -rf /var/lib/apt/lists/*

# Start D-Bus and Avahi
CMD ["sh", "-c", "service dbus start && /usr/sbin/avahi-daemon --daemonize --no-drop-root && /usr/sbin/cupsd -f"]

