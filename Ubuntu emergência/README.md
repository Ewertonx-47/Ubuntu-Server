RESOLVENDO ESTADO DE EMERGÊNCIA UBUNTU 

Ao iniciar o Ubuntu, foi identificado que o sistema foi aberto como usuário root, o que não é o normal de acontecer. A princípio não foi identificado nenhum problema. 
Porém, na hora de utilizar o Samba, que é o software utilizado para fazer a comunicação entre o Linux e o Windows, o Windows não conseguiu mapear o caminho que foi configurado anteriormente. 
O primeiro problema identificado foi que o Ubuntu não estava na mesma rede que o host. A interface 1 (lo) é a de loopback, a interface 2 (enp0s3) é a que faz a comunicação interna com o pfsense e a interface 3 (enps0s8) estava vazia. Após usar o comando sudo dhclient, a interface 3 passou a receber IP. 

![ENP0S8](../Imagem/j_enp0s8.png)

Mas mesmo a interface obtendo IP, o mapeamento continuou indisponível. O erro que foi mostrado era de “caminho já está sendo utilizado”. Normalmente quando isso acontece, é porque o sistema operacional já tem uma sessão ativa com o mesmo servidor .No Prompt de comando do Windows, foi utilizado alguns comandos para verificar o motivo do não funcionamento do serviço. 

-net use: Vai listar todas as conexões SMB ativas.

A princípio a conexão estava desativada.

-net use * /delete: Desconecte a sessão antiga

Comando usado apenas para validar de fato a desconexão com o servidor. 

Após a execução desses comandos, foi feita a tentativa de mapear o Linux, porém sem sucesso também. 

Com isso, foi feita uma verificação no serviço Samba para ver se ele estava funcionando de fato. Foi utilizado alguns comandos nesse processo.

-systemctl status smbd

Esse comando me mostrou que o serviço não estava rondando 

![START](../Imagem/k_startsmbd.png)
