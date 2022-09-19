# This Dockerfile is used to build an image containing basic stuff to be used as a Jenkins slave build node.
FROM ubuntu:16.04
#MAINTAINER am <trini@konsulko.com>
LABEL Description=" This image is for building U-Boot inside a container"

# Make sure apt is happy
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update

# Install a basic SSH server
RUN apt-get install -y openssh-server
RUN mkdir -p /var/run/sshd

# Install JDK 11 from local file
#COPY openjdk-11+28_linux-x64_bin.tar.gz .
#RUN tar zxvf openjdk-11+28_linux-x64_bin.tar.gz 
RUN wget https://download.java.net/java/ga/jdk11/openjdk-11_linux-x64_bin.tar.gz
RUN tar zxvf openjdk-11_linux-x64_bin.tar.gz 
RUN mv jdk-11* /usr/local/
RUN export JAVA_HOME=/usr/local/jdk-11
RUN export PATH=$PATH:$JAVA_HOME/bin
RUN rm openjdk-11_linux-x64_bin.tar.gz 

# Install utilities
RUN apt-get install -y git
RUN apt-get install -y wget
RUN apt-get install -y build-essential
RUN apt-get install -y zip
RUN apt-get install -y curl

# Add user jenkins to the image
RUN adduser --quiet jenkins
RUN adduser jenkins dialout
RUN adduser jenkins plugdev

# Create the buildman config file
RUN /bin/echo -e "[toolchain]\nroot = /usr" > ~jenkins/.buildman
RUN /bin/echo -e "kernelorg = /opt/gcc-7.3.0-nolibc/*" >> ~jenkins/.buildman
RUN /bin/echo -e "arc = /opt/arc_gnu_2017.09_prebuilt_uclibc_le_archs_linux_install" >> ~jenkins/.buildman
RUN /bin/echo -e "\n[toolchain-alias]\nsh = sh4" >> ~jenkins/.buildman
RUN /bin/echo -e "\nriscv = riscv64" >> ~jenkins/.buildman
RUN /bin/echo -e "\nsandbox = x86_64" >> ~jenkins/.buildman
RUN chown jenkins:jenkins ~jenkins/.buildman

# Add user jenkins to sudoers with NOPASSWD
RUN echo "jenkins ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Set password for the jenkins user (you may want to alter this).
RUN echo "jenkins:jenkins" | chpasswd

# Setting for sshd
RUN sed -i 's|session    required     pam_loginuid.so|session    optional     pam_loginuid.so|g' /etc/pam.d/sshd  

# Standard SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]