# On part d'une Ubuntu standard
FROM ubuntu:latest

# Éviter les questions interactives durant l'installation
ENV DEBIAN_FRONTEND=noninteractive

# 1. Installation des pré-requis et de Samba
RUN apt-get update && apt-get install -y \
    curl \
    gnupg \
    apt-transport-https \
    lsb-release \
    samba \
    iputils-ping \
    && rm -rf /var/lib/apt/lists/*

# 2. Ajout du dépôt officiel Wazuh
RUN curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
RUN echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

# 3. Installation de l'agent Wazuh (Version 4.9.0 spécifique !)
# On configure directement l'adresse du manager ici pour éviter le "sed" plus tard
RUN apt-get update && WAZUH_MANAGER="wazuh.manager" WAZUH_AGENT_VERSION="4.9.0-1" apt-get install -y wazuh-agent=4.9.0-1

# 4. Configuration de la vulnérabilité Samba (Partage public)
# On crée un dossier secret
RUN mkdir /srv/private_share && chmod 777 /srv/private_share
RUN echo "Secret Data" > /srv/private_share/secret.txt

# On ajoute la config vulnérable à la fin du fichier smb.conf
RUN echo "[public]" >> /etc/samba/smb.conf && \
    echo "   path = /srv/private_share" >> /etc/samba/smb.conf && \
    echo "   browseable = yes" >> /etc/samba/smb.conf && \
    echo "   read only = no" >> /etc/samba/smb.conf && \
    echo "   guest ok = yes" >> /etc/samba/smb.conf

# 5. Script de démarrage (Lance Samba + Wazuh Agent)
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
