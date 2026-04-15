# Ubuntu Server Lab: NAS

Este projeto documenta a configuração de um servidor dedicado a **armazenamento em rede (NAS)** utilizando **Ubuntu Server**, com forte ênfase em **segurança de rede através do Nftables**.

O objetivo é criar uma solução robusta, segura e acessível para centralização de ficheiros dentro de uma rede local.

---

## Objetivos do Projeto

- **NAS (Network Attached Storage):**  
  Centralizar o armazenamento de ficheiros acessíveis por múltiplos dispositivos na rede local.

- **Controle de Acesso:**  
  Garantir autenticação segura e permissões adequadas no compartilhamento.

- **Segurança Avançada:**  
  Implementar uma política de firewall baseada em *Default Drop* utilizando Nftables.

---

## Stack Técnica

| Componente            | Tecnologia                  |
|----------------------|----------------------------|
| Sistema Operativo    | Ubuntu Server              |
| Serviço Principal    | Samba (SMB/CIFS)           |
| Segurança            | Nftables (Firewall nativo) |
| Cliente de Teste     | Linux Mint (Nemo File Manager) |

---

## Arquitetura do Projeto

O servidor atua como um **NAS centralizado**, permitindo:

- Compartilhamento de ficheiros via protocolo SMB  
- Acesso controlado por utilizador  
- Proteção de rede via firewall restritivo  

---

## Hardware: Implementação em Servidor Físico

Ao invés de utilizar máquinas virtuais, todo o projeto foi implementado diretamente em hardware real. Isso trouxe um nível maior de controle sobre o ambiente e exigiu uma preparação completa da máquina.

- **Preparação do equipamento:**  
  Foi feita a limpeza interna dos componentes e substituição de peças essenciais, incluindo instalação de processador e um HD, garantindo estabilidade para uso contínuo.

- **Instalação direta do sistema:**  
  O Ubuntu Server foi instalado diretamente no disco físico, com particionamento manual e configuração real de dispositivos (como `/dev/sda`), sem abstrações de virtualização.

- **Aproveitamento total de recursos:**  
  Sem camada de hypervisor, o servidor utiliza diretamente CPU e memória, o que reduz latência e melhora o desempenho no acesso aos ficheiros via rede.

---

## Configuração Passo a Passo

### 1. Estrutura de Armazenamento

```bash
sudo mkdir -p /home/storage/public
sudo chown -R seuUser:seuUser /home/storage/public
sudo chmod -R 775 /home/storage/public
```

---

### 2. Configuração do Samba

**Arquivo:** `/etc/samba/smb.conf`

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

**Arquivo:** `/etc/nftables.conf`

```conf
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

```text
smb://Seu Ip Aqui/MeuStorage
```

---

## Evidências da Implementação

### Acesso ao Servidor via SSH

<p align="center">
  <img src="img/acesso_ssh.jpeg" width="700">
</p>

**Descrição:** Nesta imagem é demonstrado o acesso remoto ao servidor utilizando SSH, incluindo informações do sistema como uso de recursos e IP.

---

### Estrutura de Diretórios do NAS

<p align="center">
  <img src="img/TesteStorage.jpeg" width="700">
</p>

**Descrição:** Exibição da estrutura de diretórios criada para o armazenamento (`/home/storage/public`), essencial para organização dos dados compartilhados.

---

### Configuração do Firewall com Nftables

<p align="center">
  <img src="img/nftables_config.jpeg" width="700">
</p>

**Descrição:** Configuração do arquivo `/etc/nftables.conf`, demonstrando a política de segurança baseada em *default drop* com liberação apenas de portas essenciais.

---

## Resultados e Aprendizagem

- Administração Linux (permissões e diretórios)  
- Protocolo SMB/CIFS  
- Firewall com Nftables (*Default Drop*)  

---

## Conclusão

Este projeto demonstra a criação de um NAS seguro e eficiente, focado em armazenamento e proteção de rede.