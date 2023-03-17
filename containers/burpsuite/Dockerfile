from alexandreoda/burpsuite

run sudo apt-get update

# Tools
run sudo apt-get -y install openssh-server
run sudo apt-get -y install x11-apps
run sudo apt-get -y install xauth

# Install private key to Burp
run mkdir /home/burpsuite/.ssh
run chmod 700 /home/burpsuite/.ssh
COPY ./config/authorized_keys /home/burpsuite/.ssh/authorized_keys
run sudo chown -R burpsuite /home/burpsuite/.ssh/authorized_keys
run chmod 600 /home/burpsuite/.ssh/authorized_keys
run sudo mkdir /var/run/sshd

# run script
COPY ./config/runme.sh /home/burpsuite/burpsuite.sh
run sudo chmod o+rx /home/burpsuite/burpsuite.sh

## SSH
COPY ./config/sshd_config /etc/ssh/sshd_config

# HTTP
EXPOSE 443

# SSH
EXPOSE 22

# Run ssh service
CMD ["sudo","/usr/sbin/sshd","-D"]
