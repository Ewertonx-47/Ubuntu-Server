*VINCULANDO SERVIÇO VEEAM COM UBUNTU NO VIRTUALBOX*

O Veeam é uma ferramenta poderosa para backup, mas para ele "enxergar" o Ubuntu dentro do VirtualBox, é preciso de um componente adicional, pois o Veeam que está no host (windows) não consegue acessar o disco da máquina virtual diretamente de forma gerenciada. A chave para a conexão é o Veeam Agent for Linux. É um software que deve ser instalado dentro do sistema operacional convidado (o Ubuntu). Ele funciona como um "tradutor" ou "agente" que se comunica com o servidor Veeam no host, permitindo que o Veeam realize e gerencie os backups.

**PROCESSO BÁSICO DE COMUNICAÇÃO**

**Baixar o Agente:** Acesse o site da Veeam e baixe o instalador do Veeam Agent for Linux. Provavelmente vai precisar do arquivo .deb para o Ubuntu.
**Transferir o Arquivo:** Passar o arquivo .deb para a máquina virtual Ubuntu. Pode usar uma pasta compartilhada no VirtualBox, um pen drive ou até mesmo o navegador da VM para fazer o download direto. Nesse caso, será usado a pasta compartilhada.
**Instalar o Agente:** Dentro do seu Ubuntu, instalar o pacote usando o terminal.

inicialmente, é necessário criar um pasta compartilhada entre o host e o Ubuntu. O caminho para isso é:

clicar na VM->Configurações->Pasta compartilha

Após criar mapeamento de qual pasta será compartilhada, de um nome para a pasta e salve.

(foto)

Depois de criar o mapeamento, se faz necessário instalar um pacote de software que faz essa ponte entre a VM e o host, para que a VM consiga enxergar a pasta compartilhada. Esse pacote é o Guest Addition. Esse pacote é uma ISO que fica na pasta de instalação do host, e já é inserida na VM na hora da instalação do sistema operacional. Essa ISO fica no drive virtual de DVD/CD. O Linux enxerga todos os dispositivos como arquivos, e o drive onde está o Guest Addition é o arquivo **dev/cdrom**.

Entendendo todo o funcionamento das questões acima, é preciso montar o drive **cdrom** em uma pasta vazia para seja possível enxergar o conteúdo que ali se encontra e poder executar os comandos necessários para instalar o Guest Addition. 

-sudo mkdir -p /mnt/virtualbox #Criando uma pasta vazia dentro do diretório /mnt
-sudo mount /dev/cdrom /mnt/virtualbox #Montando o drive cdrom em virtualbox 





