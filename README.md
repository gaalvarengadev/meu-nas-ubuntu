# 📂 Ubuntu Server Lab: Home Storage (NAS) & Web Server (Apache)

Este repositório documenta a configuração de um servidor multifuncional utilizando **Ubuntu Server**. O projeto foca na centralização de ficheiros (NAS) e na hospedagem de serviços web, priorizando a segurança através de um firewall robusto.

---

## 🚀 Objetivos do Projeto

1. **NAS (Network Attached Storage):** Criar um ponto central de armazenamento acessível por qualquer dispositivo na rede local.  
2. **Web Server:** Hospedar uma página web funcional utilizando Apache2.  
3. **Segurança Avançada:** Implementar uma política de *Default Drop* utilizando Nftables.  

---

## 🛠️ Stack Técnica

- **Sistema Operativo:** Ubuntu Server  
- **Serviços:** Samba (SMB) & Apache2  
- **Segurança:** Nftables (Firewall nativo)  
- **Cliente de Teste:** Linux Mint (Nemo File Manager)  

---

## 🔧 Configuração Passo a Passo

### 1. 🗄️ Servidor de Storage (Samba)

Criamos a estrutura física e configuramos o protocolo SMB para interoperabilidade entre Linux e Windows.

#### Permissões de Sistema

```bash
sudo mkdir -p /home/storage/public
sudo chown -R gabe:gabe /home/storage/public
sudo chmod -R 775 /home/storage/public
```

#### Configuração do Samba (`/etc/samba/smb.conf`)

```ini
[MeuStorage]
   comment = Pasta Publica Ubuntu
   path = /home/storage/public
   read only = no
   browsable = yes
   writable = yes
   guest ok = no
```

---

### 2. 🌐 Servidor Web (Apache2)

Implementação de um servidor web para hospedagem de projetos internos.

#### Estrutura do Site

```bash
sudo mkdir -p /var/www/meu-projeto/public_html
sudo chown -R $USER:$USER /var/www/meu-projeto/public_html
```

#### Virtual Host (`/etc/apache2/sites-available/meu-projeto.conf`)

```apache
<VirtualHost *:80>
    DocumentRoot /var/www/meu-projeto/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

---

### 3. 🔐 Firewall e Segurança (Nftables)

A configuração do firewall é o coração da segurança deste servidor. Utilizamos o Nftables para filtrar apenas o tráfego essencial.

#### Regras aplicadas em `/etc/nftables.conf`

```bash
table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;

        # Manter conexões ativas
        ct state established,related accept
        iif "lo" accept

        # Portas de Storage (Samba)
        tcp dport { 139, 445 } accept
        udp dport { 137, 138 } accept

        # Portas Web (HTTP/HTTPS)
        tcp dport { 80, 443 } accept

        # Acesso Remoto (SSH)
        tcp dport 22 accept
    }
}
```

---

## 💻 Como Aceder (Cliente Linux Mint)

### 📁 Aceder aos Ficheiros

No gestor de ficheiros Nemo, pressione `Ctrl + L` e digite:

```
smb://192.168.217.39/MeuStorage
```

### 🌍 Aceder ao Website

No navegador, digite o endereço IP do servidor:

```
http://192.168.217.39
```

---

## 📊 Resultados e Aprendizagem

- **Administração Linux:** Gestão de utilizadores, permissões de ficheiros e serviços de sistema (systemd).  
- **Networking:** Entendimento do protocolo SMB e funcionamento de Virtual Hosts no Apache.  
- **Segurança:** Configuração de Firewall as Code (FaC) com Nftables, garantindo proteção contra acessos não autorizados.  
