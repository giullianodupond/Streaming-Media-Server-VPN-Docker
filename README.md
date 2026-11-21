# 🎥 Servidor de Mídia com MistServer + ZeroTier VPN

Infraestrutura de recepção, gerenciamento e distribuição de transmissões de vídeo utilizando **MistServer** e **ZeroTier**, totalmente conteinerizada com Docker Compose.

Este projeto permite implementar um servidor de mídia acessível por meio de uma rede privada virtual, eliminando a necessidade de possuir um endereço IP público fixo ou configurar manualmente regras complexas de NAT e Port Forwarding.

A solução foi projetada para conectar encoders, câmeras, estações de operação e servidores de mídia em diferentes localidades, proporcionando uma infraestrutura flexível para aplicações de streaming audiovisual.

---

## 🚀 Funcionalidades

### 🎬 Mídia e Streaming — MistServer

O [MistServer](https://mistserver.org/) é o núcleo da infraestrutura, responsável pela recepção, processamento e distribuição dos fluxos de mídia.

**Principais recursos:**

- **Ingestão de transmissões:** recebimento de sinais provenientes de OBS Studio, câmeras IP e encoders profissionais.
- **Distribuição de mídia:** encaminhamento dos fluxos para múltiplos destinos e dispositivos.
- **Suporte a protocolos:** RTMP, SRT, HLS, MPEG-DASH e WebRTC.
- **Flexibilidade:** distribuição para players web, dispositivos compatíveis e outros servidores de mídia.
- **Gerenciamento:** interface web para configuração e administração do servidor.

### 🔐 Conectividade Segura — ZeroTier

O [ZeroTier](https://www.zerotier.com/) estabelece uma rede privada virtual entre o servidor e os dispositivos conectados.

**Principais recursos:**

- **Rede privada P2P:** conexão entre servidores, encoders, operadores e clientes.
- **Acesso remoto:** comunicação entre dispositivos localizados em redes diferentes.
- **Bypass de CGNAT:** conectividade sem depender de um endereço IPv4 público dedicado.
- **Sem Port Forwarding:** dispensa, na configuração usual, a abertura manual de portas no roteador.
- **Comunicação segura:** tráfego protegido pela infraestrutura criptográfica do ZeroTier.

### 🐳 Implantação com Docker Compose

Toda a infraestrutura é organizada em contêineres independentes.

- Implantação simplificada.
- Isolamento dos serviços.
- Persistência de dados por meio de volumes.
- Facilidade de manutenção e atualização.
- Portabilidade entre ambientes Docker compatíveis.

---

## 🏗️ Arquitetura da Infraestrutura

A comunicação acontece por meio de uma rede virtual criada pelo ZeroTier. O MistServer utiliza essa conectividade para receber e distribuir transmissões entre os dispositivos autorizados.

```text
                 INTERNET
                     │
          ┌──────────┴──────────┐
          │                    │
     ENCODER / OBS         OPERADOR
          │                    │
          └──────────┬─────────┘
                     │
              ZERO TIER VPN
              REDE PRIVADA
                     │
          ┌──────────┴──────────┐
          │   SERVIDOR DOCKER   │
          │                     │
          │  ┌───────────────┐  │
          │  │  ZeroTier VPN │  │
          │  └───────┬───────┘  │
          │          │          │
          │  ┌───────┴───────┐  │
          │  │  MistServer  │  │
          │  │              │  │
          │  │ Ingestão     │  │
          │  │ Distribuição │  │
          │  │ Streaming    │  │
          │  └───────────────┘  │
          │                     │
          └──────────┬──────────┘
                     │
              ZERO TIER VPN
                     │
             CLIENTES / PLAYERS
```

---

## 📦 Requisitos

Antes de iniciar, certifique-se de possuir:

- Docker instalado.
- Docker Compose V2.
- Conta no [ZeroTier Central](https://central.zerotier.com/).
- Uma rede criada no ZeroTier.

---

## ⚙️ Instalação e Implantação

### 1. Clonar o repositório

```bash
git clone URL_DO_REPOSITORIO
```

Acesse a pasta do projeto:

```bash
cd NOME_DO_REPOSITORIO
```

### 2. Iniciar a infraestrutura

Na pasta raiz, onde está localizado o arquivo `docker-compose.yml`, execute:

```bash
docker compose up -d --build
```

Esse comando realiza a construção das imagens necessárias e inicia os serviços em segundo plano.

> **Observação:** caso utilize uma versão antiga do Docker Compose, poderá ser necessário utilizar `docker-compose` em vez de `docker compose`.

### 3. Verificar os contêineres

Para verificar se os serviços foram iniciados corretamente:

```bash
docker compose ps
```

Para acompanhar os registros de execução:

```bash
docker compose logs -f
```

---

## 🔐 Configuração do ZeroTier

Com os contêineres em execução, a administração da conexão ZeroTier será realizada diretamente pelo terminal do contêiner `zerotier_vpn`.

### 1. Conectar à rede ZeroTier

Substitua `NETWORK_ID` pelo identificador de 16 caracteres da sua rede.

```bash
docker exec -it zerotier_vpn zerotier-cli join NETWORK_ID
```

Exemplo:

```bash
docker exec -it zerotier_vpn zerotier-cli join 1234567890abcdef
```

### 2. Verificar a conexão

Para consultar as redes às quais o contêiner está conectado:

```bash
docker exec -it zerotier_vpn zerotier-cli listnetworks
```

### 3. Desconectar da rede

Caso seja necessário remover o contêiner de uma rede:

```bash
docker exec -it zerotier_vpn zerotier-cli leave NETWORK_ID
```

---

## 🌐 Autorização e Acesso à Rede

Após executar o comando `join`, é necessário autorizar o dispositivo no painel administrativo do ZeroTier.

1. Acesse o [ZeroTier Central](https://central.zerotier.com/).
2. Entre na rede criada anteriormente.
3. Localize o novo dispositivo na lista de membros.
4. Marque a opção **Authorized** para autorizar a conexão.
5. Aguarde a atribuição de um endereço IP virtual.

Após a autorização, identifique o endereço na coluna **Managed IPs**.

Esse será o endereço utilizado para comunicação com os dispositivos conectados à mesma rede virtual.

---

## 🎛️ Acesso ao MistServer

Com o ZeroTier conectado e autorizado, os dispositivos participantes da rede poderão utilizar o endereço IP virtual do servidor para acessar os serviços disponibilizados pelo MistServer.

O acesso à interface de gerenciamento e o envio de transmissões dependerão das portas e dos protocolos configurados no `docker-compose.yml`.

Exemplos de aplicações:

- Configurar um encoder OBS para transmitir ao servidor.
- Receber sinais SRT de encoders remotos.
- Distribuir transmissões para outros servidores.
- Disponibilizar fluxos para players compatíveis.
- Acessar a interface administrativa remotamente.

---

## 🛠️ Gerenciamento dos Serviços

Comandos úteis para administrar a infraestrutura:

| Operação | Comando |
|---|---|
| Iniciar os serviços | `docker compose up -d` |
| Parar os serviços | `docker compose stop` |
| Reiniciar os serviços | `docker compose restart` |
| Verificar contêineres | `docker compose ps` |
| Visualizar logs | `docker compose logs -f` |
| Atualizar e recriar | `docker compose up -d --build` |
| Encerrar os serviços | `docker compose down` |

> **Atenção:** o comando `docker compose down` remove os contêineres e as redes criadas pelo Compose, mas normalmente preserva os volumes nomeados. Não utilize `-v` sem avaliar previamente os dados persistidos.

---

## 📌 Considerações

Esta infraestrutura oferece uma base para implantação de um servidor de mídia remoto, combinando os recursos de streaming do MistServer com a conectividade privada do ZeroTier.

A utilização de uma rede virtual facilita a comunicação entre equipamentos distribuídos geograficamente, tornando a solução interessante para aplicações audiovisuais, transmissões ao vivo, contribuição de vídeo e distribuição de sinais.

**Projeto desenvolvido para experimentação, integração e implantação de infraestrutura audiovisual baseada em tecnologias de código aberto.**

---

## 📚 Links úteis

- [MistServer — Site oficial](https://mistserver.org/)
- [MistServer — Documentação](https://docs.mistserver.org/)
- [ZeroTier — Site oficial](https://www.zerotier.com/)
- [ZeroTier Central](https://central.zerotier.com/)
- [ZeroTier — Documentação](https://docs.zerotier.com/)
- [Docker — Documentação](https://docs.docker.com/)
