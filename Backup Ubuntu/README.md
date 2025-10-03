FAZENDO BACKUP DA PARTIÇÃO DO UBUNTU

Com o veeam agent instalado no linux, é possível fazer os backups do sistema operacional conforme a necessidade do usuario. Pode ser feito o backup do SO por completo, de um diretório ou partição. No homelab em questão, será feito o backup da partição sd3 que contém um volume logico para o armazenamento dos arquivos utilizando o serviço samba para criar a comunicação com o host (Windows) e a vm (Linux) no virtualbox. Para começar iniciar todo esse processo, primeiro é criado um job de backup.

-sudo veeam

(foto)


