Servidor de Mídia com MistServer e ZeroTier VPN Este repositório reúne uma infraestrutura em Docker Compose que integra o MistServer e o ZeroTier, permitindo montar um servidor completo de recepção e distribuição de mídias sem a necessidade de um IP público fixo ou de rotas complexas de redes (NAT/Port Forwarding).

Funcionalidades Mídia e Streaming (MistServer):

Ingestão e Distribuição: Suporte completo a múltiplos protocolos de mídia, incluindo RTMP, SRT, HLS, DASH e WebRTC.

Flexibilidade: Permite receber transmissões de softwares como OBS, câmeras IP ou encoders profissionais e redistribuir para players na web ou outros servidores.

Conectividade Segura (ZeroTier):

Rede Privada P2P: Cria uma rede virtual privada (VPN/SD-WAN) que conecta seu servidor a encoders, operadores e clientes de forma segura.

Bypass de CGNAT / Sem IP Público: Permite acessar o servidor de qualquer lugar, mesmo através de conexões domésticas, 4G/5G ou redes com restrições de porta.

Implantação Rápida (Docker Compose):

Ambientes isolados, portáveis e prontos para execução com poucos comandos.

Como Subir o Ambiente Na pasta raiz onde se encontra o arquivo docker-compose.yml, execute o comando abaixo para compilar e iniciar os contêineres em segundo plano:

docker compose up -d --build (Caso utilize uma versão antiga do Docker CLI, use docker-compose up -d --build)

Comandos Básicos do ZeroTier Com o serviço rodando, utilize o contêiner zerotier_vpn para gerenciar as conexões de rede via CLI.

Nota: Substitua (USER NETWORK CODE) pelo ID de 16 caracteres da sua rede ZeroTier.

Entrar em uma rede docker exec -it zerotier_vpn zerotier-cli join (USER NETWORK CODE)
Exemplo: docker exec -it zerotier_vpn zerotier-cli join 123456789abcdefgh

Listar redes ativas e verificar docker exec -it zerotier_vpn zerotier-cli listnetworks

Sair de uma rede docker exec -it zerotier_vpn zerotier-cli leave (USER NETWORK CODE)

Próximos Passos após Conectar Após rodar o comando join, acesse o painel do ZeroTier Central e autorize o novo nó (marcando a checkbox de autorização).

Anote o IP atribuído ao contêiner na coluna Managed IPs.

Acesse o painel do MistServer ou envie fluxos usando o IP gerenciado do ZeroTier
