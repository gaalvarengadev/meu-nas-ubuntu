#  Home Storage Server com Ubuntu + Samba + Nftables

Este repositório documenta o processo de transformação de uma máquina com **Ubuntu Server** em um servidor de armazenamento centralizado (NAS) acessível via rede local para clientes Linux e Windows.

## Objetivo
Centralizar arquivos em um servidor dedicado, garantindo interoperabilidade entre sistemas (Linux Mint/Ubuntu) e aplicando regras rigorosas de firewall para segurança dos dados.

---

## Stack Técnica
- **SO:** Ubuntu Server
- **Protocolo:** Samba (SMB)
- **Firewall:** Nftables
- **Cliente:** Linux Mint (Nemo File Manager)

---

## Configuração Passo a Passo

### 1. Preparação da Estrutura de Pastas
Criamos o ponto de montagem central e ajustamos as permissões de proprietário (chown) e permissões de escrita (chmod) para garantir que o usuário de rede possa gerenciar os arquivos.

```bash
sudo mkdir -p /home/storage/public
sudo chown -R gabe:gabe /home/storage/public
sudo chmod -R 775 /home/storage/public
