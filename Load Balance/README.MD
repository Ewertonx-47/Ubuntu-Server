Load Balance com Site Armazenado no Apache

-Objetivo

Neste laboratório será configurado um balanceamento de carga (Load Balance) utilizando o Nginx como balanceador e dois servidores Apache hospedando o mesmo site.
Além disso, haverá uma VM cliente responsável pelos testes de acesso e validação do balanceamento.

-Estrutura do Laboratório

Máquinas Virtuais Utilizadas:

Função	Sistema	IP	Descrição:
--Servidor Apache 1:	Ubuntu	192.168.100.50 - Site principal
--Servidor Apache 2:	Ubuntu	192.168.100.52 - Clone do site principal
--Balanceador:	Nginx	192.168.100.53 - Responsável pelo Load Balance
--Cliente:	Lubuntu	192.168.100.51 - Usado para validação de funcionamento

Redes configuradas no VirtualBox:

--Rede Interna: teste

--Rede NAT: saída para a internet (para atualizações e downloads)

Cada VM possui duas interfaces de rede:

enp0s3 — Rede interna (192.168.100.0/24)

enp0s8 — Rede NAT (acesso externo)

-Etapas de Configuração

 1. Criação das VMs

Foi criada uma VM Ubuntu com o Apache e o site local configurado (já existe uma documentação sobre esse lab, mas ainda não está no github).
Em seguida, essa VM será clonada para servir como o segundo servidor Apache.

Também foram criadas mais duas VMs:

--Uma para o Nginx (Load Balance)

--Outra para o cliente (Lubuntu)

Após isso, todas as VMs foram inicializadas no VirtualBox.

(foto da topologia sugerida aqui)

 2. Configuração de Rede VM clone

Inicialmente, foi configurada a interface de rede interna nas quatro VMs, permitindo a comunicação entre elas.
Também foi adicionada uma interface NAT na VM clone para permitir acesso à internet (para atualizações e instalação de pacotes).

IPs definidos manualmente via Netplan:

# Exemplo do arquivo /etc/netplan/01-network.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      dhcp4: no
      addresses:
        - 192.168.100.52/24

Problemas Encontrados

Durante a configuração da VM clonada (Apache 2), foram observados problemas de conectividade.

Falha 1 — Interface NAT sem IP

Mesmo com o arquivo YAML configurado, a interface NAT (enp0s8) não recebia IP automaticamente.
Foi necessário atribuir manualmente um IP com:

sudo dhclient enp0s3

O IP obtido foi 10.0.3.15.

(foto do comando e retorno)

Falha 2 — Sem acesso à internet

Mesmo após receber o IP manualmente, a máquina não conseguia sair para a internet.

Testes de ping:

ping 8.8.8.8

(foto do teste de ping falho)

Análises e Diagnóstico

O VirtualBox normalmente atribui o NAT à primeira interface de rede.
Por isso, foi necessário inverter a ordem das interfaces (pois a interface com NAT era o adaptador 2 e a interface de rede interna era o adptador 1), nas configurações da VM e ajustar o arquivo YAML correspondente.

(foto das interfaces invertidas no VirtualBox)
(foto do novo arquivo YAML)

Mesmo após o ajuste, ao verificar com:

cat /etc/netplan/*.yaml

O systemd-networkd ainda aplicava apenas a configuração da primeira interface (a estática).

Verificação:

(foto mostrando apenas enp0s3 configurada)

Causa Raiz

Foi identificado que o gateway configurado manualmente no arquivo YAML estava conflitando com o DHCP da interface NAT.

O arquivo possuía algo como:

gateway4: 192.168.100.5

Esse gateway não existia, e isso causava confusão ao systemd-networkd, impedindo a aplicação correta das configurações.

Solução Aplicada

A solução foi remover o gateway manual e deixar o DHCP fornecer automaticamente o IP e gateway padrão.
Após isso, as configurações foram aplicadas corretamente.

(foto do arquivo corrigido sem gateway manual)

Erro ao aplicar Netplan

Ao executar:

sudo netplan apply

Foi exibido o erro:

Error in network definition: line 9 column 6

Isso ocorreu porque as duas interfaces estavam sendo declaradas no mesmo arquivo YAML, gerando conflito de identação.

Correção

Foram criados dois arquivos separados:

/etc/netplan/02-enp0s3.yaml → Interface NAT (DHCP automático)

/etc/netplan/01-enp0s8.yaml → Interface de rede interna (IP estático)

Após dividir os arquivos, as configurações foram aplicadas com sucesso e o acesso à internet foi restaurado.

(foto dos dois arquivos e saída do netplan apply com sucesso)

🧾 Resultado Final

Conectividade interna entre as VMs funcionando.

Conectividade externa via NAT funcional.

Sistema pronto para configuração do Nginx como Load Balancer nas próximas etapas.

🗂️ Estrutura de Arquivos Netplan (Exemplo Final)
/etc/netplan/
├── 01-enp0s8.yaml   # Rede interna - IP estático (192.168.100.52)
└── 02-enp0s3.yaml   # Rede NAT - DHCP automático
