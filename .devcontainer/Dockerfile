ARG TAG
ARG VARIANT="jammy"
#FROM buildpack-deps:${VARIANT}-curl
#FROM cs50/codespace:latest
FROM cs50/codespace:00ae65f484da2691d86ddb8e6bb3b844cbb4686d as builder


# Criado e alterado por Edkallenn Lima em 15/03/2024. Se for alterar use sempre o idioma inglês para os comentários, exceto este.

# Unset user
USER root

# [Optional] Uncomment this section to install additional OS packages.
# The first part is the core of distro
#WORKDIR /root
#EXPOSE 1234    
RUN apt-get update &&  apt update --fix-missing && apt upgrade -f -y && apt-get install  -y \    
    #curl \
    #git \
    #vim \
    #nano \
    #gcc \
    #g++ \
    #cmake \
    #automake \     
    build-essential \
    doxygen \     
    iputils-ping \
    cowsay \
    graphviz \
    libboost-dev \
    libboost-regex-dev \   
 && apt-get clean \ 
 && rm -rf /var/lib/apt/lists/* 

# Install glibc sources for debugger
# https://github.com/Microsoft/vscode-cpptools/issues/1123#issuecomment-335867997
 RUN echo "deb-src http://archive.ubuntu.com/ubuntu/ jammy main restricted" > /etc/apt/sources.list.d/_.list && \
    apt update && \
    apt install --no-install-recommends --no-install-suggests --yes \
        dpkg-dev && \
    cd /tmp && \
    apt source glibc && \
    rm --force --recursive *.dsc *.tar.* && \
    mkdir --parents /build/glibc-sMfBJT && \
    tar --create --gzip --file /build/glibc-sMfBJT/glibc.tar.gz glibc*

# Install BFG
RUN wget https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar -P /opt/share


# Install Lua 5.x
# https://www.lua.org/manual/5.4/readme.html
RUN cd /tmp && \
    curl --remote-name https://www.lua.org/ftp/lua-5.4.6.tar.gz && \
    tar xzf lua-5.4.6.tar.gz && \
    rm --force lua-5.4.6.tar.gz && \
    cd lua-5.4.6 && \
    make all install && \
    cd .. && \
    rm --force --recursive /tmp/lua-5.4.6


# Install noVNC (VNC client)
RUN cd /tmp && \
    curl --location --remote-name https://github.com/novnc/noVNC/archive/refs/tags/v1.4.0.zip && \
    unzip v1.4.0.zip && \
    rm --force v1.4.0.zip && \
    cd noVNC-1.4.0/utils && \
    curl --location --remote-name https://github.com/novnc/websockify/archive/refs/heads/master.tar.gz && \
    tar xzf master.tar.gz && \
    mv websockify-master websockify && \
    rm --force master.tar.gz && \
    cd ../.. && \
    mv noVNC-1.4.0 /opt/noVNC


# Install VS Code extensions
RUN npm install --global @vscode/vsce yarn && \
    mkdir --parents /opt/cs50/extensions && \
    cd /tmp && \
    git clone https://github.com/cs50/explain50.vsix.git && \
    cd explain50.vsix && \
    npm install && \
    vsce package && \
    mv explain50-1.0.0.vsix /opt/cs50/extensions && \
    cd /tmp && \
    rm --force --recursive explain50.vsix && \
    git clone https://github.com/cs50/cs50.vsix.git && \
    cd cs50.vsix && \
    npm install && \
    vsce package && \
    mv cs50-0.0.1.vsix /opt/cs50/extensions && \
    mv python-clients/cs50vsix-client /opt/cs50/extensions && \
    cd /tmp && \
    rm --force --recursive cs50.vsix && \
    git clone https://github.com/cs50/ddb50.vsix.git && \
    cd ddb50.vsix && \
    npm install && \
    vsce package && \
    mv ddb50-2.0.0.vsix /opt/cs50/extensions && \
    cd /tmp && \
    rm --force --recursive ddb50.vsix && \
    git clone https://github.com/cs50/phpliteadmin.vsix.git && \
    cd phpliteadmin.vsix && \
    npm install && \
    vsce package && \
    mv phpliteadmin-0.0.1.vsix /opt/cs50/extensions && \
    cd /tmp && \
    rm --force --recursive phpliteadmin.vsix && \
    git clone https://github.com/cs50/style50.vsix.git && \
    cd style50.vsix && \
    npm install && \
    vsce package && \
    mv style50-0.0.1.vsix /opt/cs50/extensions && \
    cd /tmp && \
    rm --force --recursive style50.vsix && \
    npm uninstall --global vsce yarn


# Final stage
#FROM cs50/cli:latest


# Unset user
USER root
ARG DEBIAN_FRONTEND=noninteractive


# Copy files from builder
#COPY --from=builder /build /build
#COPY --from=builder /opt /opt
#COPY --from=builder /usr/local /usr/local
RUN pip3 install --no-cache-dir /opt/cs50/extensions/cs50vsix-client/ && \
    rm --force --recursive /opt/cs50/extensions/cs50vsix-client


# Set virtual display
ENV DISPLAY=":0"


# Install Ubuntu packages
# Install acl for temporarily removing ACLs via opt/cs50/bin/postCreateCommand
# https://github.community/t/bug-umask-does-not-seem-to-be-respected/129638/9
RUN apt update && \
    apt install --no-install-recommends --no-install-suggests --yes \
        acl \
        dwarfdump \
        jq \
        openbox \
        mysql-client \
        php-cli \
        php-mbstring \
        php-sqlite3 \
        xvfb \
        x11vnc && \
    apt clean


# Install Python packages
RUN pip3 install --no-cache-dir \
        black \
        clang-format \
        djhtml \
        matplotlib \
        "pydantic<2" \
        pytz \
        setuptools


# Copy files to image
#COPY ./etc /etc
#COPY ./opt /opt
#RUN chmod a+rx /opt/cs50/bin/* && \
#    chmod a+rx /opt/cs50/phpliteadmin/bin/phpliteadmin && \
#    ln --symbolic /opt/cs50/phpliteadmin/bin/phpliteadmin /opt/cs50/bin/phpliteadmin


# Enforce login shell
RUN echo "\nshopt -q login_shell || exec bash --login -i" >> /etc/bash.bashrc


# installs cs50lib
RUN touch instala-config-cs50.sh && \
    curl -s https://raw.githubusercontent.com/ed1rac/FerramentasProgramacao/master/script.deb.sh > instala-config-cs50.sh && \ 
    chmod +x instala-config-cs50.sh && \
    ./instala-config-cs50.sh && \
    curl -s https://ohmyposh.dev/install.sh | bash -s && \
   apt install libcs50


#export some enviroment variables
RUN echo "export CC=\"clang\"" >> ~/.bashrc
RUN echo "export CFLAGS=\"-ferror-limit=1 -gdwarf-4 -ggdb3 -O0 -std=c11 -Wall -Werror -Wextra -Wno-gnu-folding-constant -Wno-sign-compare -Wno-unused-parameter -Wno-unused-variable -Wno-unused-but-set-variable -Wshadow\"" >> ~/.bashrc
RUN echo "export LDLIBS=\"-lcrypt -lcs50 -lm\"" >> ~/.bashrc

RUN echo "export LIBRARY_PATH=/usr/local/lib" >> ~/.bashrc
RUN echo "export C_INCLUDE_PATH=/usr/local/include" >> ~/.bashrc
RUN echo "export LD_LIBRARY_PATH=/usr/local/lib" >> ~/.bashrc
RUN echo "export GLOBAL_MAKEFILE_PATH=~" >> ~/.bashrc

# instala algumas configuracoes. Basta dar enter no prompt
RUN touch instala-config-ed.sh && \
    curl https://raw.githubusercontent.com/ed1rac/FerramentasProgramacao/master/instala-config-codespace.sh > instala-config-ed.sh && \
    chmod +x instala-config-ed.sh && \
    ./instala-config-ed.sh

# Set user
USER ubuntu