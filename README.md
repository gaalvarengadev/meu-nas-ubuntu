# Ubuntu Server Lab: NAS 

Este projeto documenta a configuração de um servidor dedicado a **armazenamento em rede (NAS)** utilizando **Ubuntu Server**, com forte ênfase em **segurança de rede através do Nftables**.

O objetivo é criar uma solução robusta, segura e acessível para centralização de ficheiros dentro de uma rede local.

---

## Objetivos do Projeto

1. **NAS (Network Attached Storage):**  
   Centralizar o armazenamento de ficheiros acessíveis por múltiplos dispositivos na rede local.

2. **Controle de Acesso:**  
   Garantir autenticação segura e permissões adequadas no compartilhamento.

3. **Segurança Avançada:**  
   Implementar uma política de firewall baseada em *Default Drop* utilizando Nftables.

---

## Stack Técnica

- **Sistema Operativo:** Ubuntu Server  
- **Serviço Principal:** Samba (SMB/CIFS)  
- **Segurança:** Nftables (Firewall nativo)  
- **Cliente de Teste:** Linux Mint (Nemo File Manager)  

---

## Arquitetura do Projeto

O servidor atua como um **NAS centralizado**, permitindo:

- Compartilhamento de ficheiros via protocolo SMB  
- Acesso controlado por utilizador  
- Proteção de rede via firewall restritivo  

---

## Configuração Passo a Passo

### 1. Estrutura de Armazenamento

```bash
sudo mkdir -p /home/storage/public
sudo chown -R usuario:usuario /home/storage/public
sudo chmod -R 775 /home/storage/public
```

---

### 2. Configuração do Samba

Arquivo: `/etc/samba/smb.conf`

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

### 3. Firewall e Segurança (Nftables)

Arquivo: `/etc/nftables.conf`

```bash
table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;

        ct state established,related accept
        iif "lo" accept

        tcp dport { 139, 445 } accept
        udp dport { 137, 138 } accept

        tcp dport 22 accept
    }
}
```

---

## Acesso ao Servidor

### Acesso aos ficheiros

```bash
smb://Seu Ip Aqui/MeuStorage
```

---

## 📊 Resultados e Aprendizagem

- Administração Linux (permissões e diretórios)  
- Protocolo SMB/CIFS  
- Firewall com Nftables (Default Drop)  

---

## 📌 Conclusão

Este projeto demonstra a criação de um **NAS seguro e eficiente**, focado em armazenamento e proteção de rede.
