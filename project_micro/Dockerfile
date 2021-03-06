# Create base system
FROM scratch
MAINTAINER Igor Zalatov <flyrouter@gmail.com>
ADD debian_jessie_rootfs.tar.xz /

# Disable interactive
ENV DEBIAN_FRONTEND noninteractive

# Set timezone
RUN echo "Europe/Simferopol" > /etc/timezone
RUN dpkg-reconfigure -f noninteractive tzdata

# Resolve problem with execution policy-rc.d
RUN echo "exit 0" > /usr/sbin/policy-rc.d && chmod +x /usr/sbin/policy-rc.d

# Update and upgrade
RUN apt-get -qq -y -u update && apt-get -qq -y -u upgrade

# Install packages
RUN apt-get -qq -y --no-install-recommends install  apt-utils runit
RUN apt-get -qq -y --no-install-recommends install  dropbear sudo mc
RUN apt-get -qq -y --no-install-recommends install  wget iperf netcat
RUN apt-get -qq -y --no-install-recommends install  cron ssmtp bsd-mailx
RUN dpkg --purge postfix

# Expose the SSH port
EXPOSE 22

# Now make sure that runit will launch SSH daemon, via runit.
RUN mkdir -p /etc/service/dropbear /var/run/dropbear
RUN /bin/echo -e '#!/bin/sh' > /etc/service/dropbear/run
RUN /bin/echo -e 'exec 2>&1' >> /etc/service/dropbear/run
RUN /bin/echo -e 'exec /usr/sbin/dropbear -j -k -F' >> /etc/service/dropbear/run

# Make sure our run-script is executable.
RUN chown root.root /etc/service/*/run && chmod 755 /etc/service/*/run

# Setup a simple & stupid root password
RUN echo "root:mypass2016" | chpasswd

# Clean system
RUN apt-get -qq -y clean
RUN rm -rf usr/{{lib,share}/locale} usr/share/{man,doc,info}

# Launch runit
ENTRYPOINT ["/usr/sbin/runsvdir-start"]
