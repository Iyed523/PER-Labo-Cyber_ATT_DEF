FROM kalilinux/kali-rolling

# On met à jour et on installe les outils AVANT que le conteneur ne soit isolé
RUN apt-get update && apt-get install -y \
    iputils-ping \
    net-tools \
    nmap \
    curl \
    && rm -rf /var/lib/apt/lists/*

CMD ["/bin/bash"]
