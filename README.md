# Treinamento - Highbyte

Este repositório tem como objetivo compartilhar um ambiente para testes com o Highbyte. A configuração foi realizada utilizando Docker Compose para criar um ambiente de simulação que inclui uma base de dados TimescaleDB, um servidor OPC PLC, e o Highbyte. Abaixo estão as instruções para iniciar e utilizar este ambiente.

## Pré-requisitos

Antes de começar, você precisa garantir que tenha o Docker e o Docker Compose instalados em sua máquina. Se não tiver, siga as instruções de instalação nas páginas oficiais:

- [Docker para Ubuntu](https://docs.docker.com/engine/install/ubuntu/) (No caso de possuir um Windows, pode-se optar por usar [WSL](https://documentation.ubuntu.com/wsl/en/latest/guides/install-ubuntu-wsl2/))
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)

## Serviços

O ambiente contém os seguintes serviços:

### 1. TimescaleDB
- **Imagem**: `timescale/timescaledb:latest-pg14`
- **Porta**: `5432:5432`
- **Usuário padrão**: `admin`
- **Senha**: `1234`
- **Volumes**: Os dados são persistidos no diretório `./volumes/timescale/data` no host.
- **Função**: Base de dados PostgreSQL com extensão TimescaleDB para armazenamento de séries temporais.

### 2. Servidor OPC PLC (OPC UA)
- **Imagem**: `mcr.microsoft.com/iotedge/opc-plc:latest`
- **Portas**: 
  - OPC UA: `50000:50000`
  - Interface Web: `8080:8080`
- **Parâmetros de inicialização**:
  - `--pn=50000`: Define a porta para o servidor OPC UA.
  - `--autoaccept`: Aceita automaticamente certificados OPC UA.
  - `--sph`: Habilita políticas de segurança.
  - `--sn=5`: Número de nós estáticos.
  - `--sr=10`: Taxa de amostragem (ms) para nós estáticos.
  - `--st=uint`: Tipo de dado dos nós estáticos.
  - `--fn=5`: Número de nós com alteração rápida.
  - `--fr=1`: Intervalo de amostragem para nós rápidos (ms).
  - `--ft=uint`: Tipo de dado dos nós rápidos.
- **Função**: Servidor de simulação OPC UA, ideal para testes de comunicação com sistemas industriais.

### 3. Highbyte
- **Imagem**: `highbyte:3.4.1`
- **Portas**: 
  - Interface Principal: `45245:45245`
  - MQTT: `1885:1885`
  - Outros: `8885:8885`
- **Volumes**: O diretório de dados do Highbyte é mapeado para `./volumes/highbyte` no host.
- **Função**: Plataforma Highbyte para integração de dados industriais.

## Como utilizar

1. **Clone este repositório**:
   ```bash
   git clone https://github.com/seuusuario/highbyte-training.git
   cd highbyte-training
   ```

2. **Suba os containers com o Docker Compose**:
   ```bash
   docker-compose up -d
   ```

   Isso iniciará os serviços TimescaleDB, OPC PLC e Highbyte.

3. **Acessando os serviços**:
   - **Highbyte**: Acesse a interface via navegador em `http://localhost:45245`.
   - **Servidor OPC PLC**: O servidor OPC UA estará disponível em `opc.tcp://localhost:50000`. A interface web de monitoramento estará disponível em `http://localhost:8080`.

4. **Parar os containers**:
   Para parar todos os serviços, execute:
   ```bash
   docker-compose down
   ```

## Estrutura de diretórios

- `./volumes/timescale/data`: Armazena os dados do TimescaleDB.
- `./volumes/highbyte`: Armazena os dados de configuração e persistência do Highbyte.
