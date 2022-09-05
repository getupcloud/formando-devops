0. Clonando o repositório:

    - mkdir Getup
    - git init
    - git clone https://github.com/getupcloud/formando-devops.git
    - cd formando-devops/desafio-linux
    - vagrant up
    
    *localectl set-keymap br-abnt2    

1. Kernel e Boot loader

	O usuário vagrant está sem permissão para executar comandos root usando sudo. Sua tarefa consiste em reativar a permissão no sudo para esse usuário.

	Dica: lembre-se que você possui acesso "físico" ao host.

		Opção (e) na seleção de boot
		Mudar de ro para rw na linha do kernel
		init=/sysroot/bin/sh -> logo a frente da opção rw
		chroot /sysroot -> muda o direotorio root para sysroot (ctrl + x)
		chroot /sysroot
		passwd root
		senha:p53kwpko
		touch /.autorelabel -> tornar o diretório confiável para o SELinux
		exit -> sair do chroot
		reboot	
	
		passwd vagrant [v53kwpko] -> para ser possível alternar entre usuários		
		usermod -aG wheel vagrant						
		
2. Usuários

	2.1 Criação de usuários
	
	Crie um usuário com as seguintes características:
	     ->	username: getup (UID=1111)
	     ->	grupos: getup (principal, GID=2222) e bin
	     ->	permissão sudo para todos os comandos, sem solicitação de senha.  	             
		     	               	             
		    adduser --uid 1111 getup
		    groupmod --gid 2222 getup
		    usermod -aG bin, wheel getup
		    passwd getup [g53kwpko]

		    vim /etc/sudoers (para não pedir senha ao usar o sudo)
		    %wheel ALL=NOPASSWD:ALL 

3. SSH

	3.1 Autenticação confiável
			
	O servidor SSH está configurado para aceitar autenticação por senha. No entanto esse método é desencorajado pois apresenta alto nivel de fragilidade.
	Voce deve desativar autenticação por senhas e permitir apenas o uso de par de chaves:
		
		vim /etc/ssh/ssh_config
		PermitRootLogin como 'no'
		PasswordAuthentication como 'no'			
	
	3.2 Criação de chaves

	Crie uma chave SSH do tipo ECDSA (qualquer tamanho) para o usuário vagrant. Em seguida, use essa mesma chave para acessar a VM:
		
		cd ~/.ssh
		ssh-keygen -t ecdsa -b 521
		ssh-copy-id host@localremoto
			
	*Obs: Num primeiro momento tentei fazer a parte da configuração do ssh sem consultar nenhum material,
	mas acabei impedindo a máquina do desafio de efetuar ssh e resolvi investigar o problema a fim de aprender
	troubleshooting (importante: todos os passos também foram feitos na outra máquina e não encontrei nada nos
	arquivos de log	que pudesse deixar mais claro o que estava acontecendo).
						
		Comecei checando o arquivo de configuração, mas aparentemente estava sem erros e idêntico
		a um outro arquivo numa outra máquina, sendo que essa outra máquina conseguia acessar por ssh
		a máquina do desafio, mas o inverso não acontecia.	

		Possíveis causas (da mais provável pra menos provável):

		-> Verifiquei o status do daemon do ssh
		-> Consegui realizar a conexão ssh via localhost na outra máquina
		-> Habilitei temporariamente o login com senha
		-> Chequei se a VM estava com placa diferente de modo bridge			
		-> Copiei manualmente as chaves de autenticação para o diretório de chaves autorizadas
		-> Removi certificados pré-configurados e limpei os hosts conhecidos
		-> Conferi as permissoes recomendadas para os diretórios onde armazenei as chaves e para
		   as chaves em si
		-> Procurei regras no Firewall que pudessem estar impedindo a comunicação via ssh
		-> Reinstalei a máquina e tentei habilitar o ssh normalmente (o que resolveu o problema)

		Log do erro em questão (na máquina remota): Connection closed by authenticating user
		guilherme 192.168.0.25 port 57950 [preauth]

		Depois de todo esse processo descobri a causa do problema: quando você marca a opção
		PasswordAuthentication como 'no' isso se aplica também a senha da chave em si, e eu
		realmente tinha colocado um passphrase nas chaves (o que me leva a crer que em algum
		momento não fiz algum dos passos do troubleshooting corretamente).					
						
	3.3 Análise de logs e configurações ssh

	Utilizando a chave do arquivos id_rsa-desafio-devel.gz.b64 deste repositório, acesso a VM com o usuário devel:
		
		passwd devel [d53kwpko]
		usermod -aG wheel devel
		yum install -y tmux

		tela 1 como usuário devel:
		
		vim /etc/ssh/sshd_config
		PasswordAuthentication 'yes'
		systemctl restart sshd
		ssh-copy-id devel@192.168.0.26
		ssh devel@192.168.0.26

		tela 2 como root:

		tail -f /var/log/audit/audit.log		
		
	*Obs: Nesse ponto tente usar o que aprendi com o problema anterior, mas como as senhas das chaves do usuário devel
	já haviam sido geradas, seria impossível obter a passphrase antiga (old) a fim de configurar uma nova senha vazia,
	e resolvi gerar novas chaves (até esse ponto todas as saídas da tela 2 foram idênticas as do problema anterior).
		
		rm -f id_rsa
		rm -f id_rsa.pub	
		vim authorized_keys (dG - para limpar o arquivo)
		
		vim /etc/ssh/sshd_config
		PasswordAuthentication 'yes'
		systemctl restart sshd		
		ssh-keygen -b 2048		
		ssh-copy-id devel@192.168.0.26
		
		vim /etc/ssh/sshd_config
		PasswordAuthentication 'no'
		systemctl restart sshd
		ssh devel@192.168.0.26
		
	Isso acabou não resolvendo o problema de logar com a chave do usuário devel, mas trocar as permissões dos arquivos
	foi a resposta.	Uma explicação possível seria que dar permissões a essas chaves para outros usuários (p.ex.
	o grupo do usuário) acaba novamente por pedir login com senha, que vem sendo desabilitado por questões de segurança:
		
		chmod 600 id_rsa
		chmod 600 id_rsa.pub
		chmod 600 authorized_keys	
		
4. Systemd

	Identifique e corrija os erros na inicialização do servico nginx. Em seguida, execute o comando abaixo (exatamente como está) e apresente o resultado. Note que o comando não deve falhar.
	
	curl http://127.0.0.1
	
		Apesar de ter seguido o erro apresentado pelo comando "systemctl status nginx.service" e ter corrigido
		a sintaxe do arquivo /etc/nginx/nginx.conf (o que pude atestar depois com o comando "nginx -t", o serviço ainda
		apresentava erros, no caso agora o binário /usr/sbin/nginx, e portanto tive que reinstalar o serviço:
		
					     yum remove nginx
					     yum install nginx
					     systemctl start nginx				     
					     
			Saída abreviada do comando curl http://127.0.0.1:
			
				<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

				<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
				    <head>
					<title>Test Page for the Nginx HTTP Server on Red Hat Enterprise Linux</title>
					<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
					.......................................................................
					.......................................................................
					.......................................................................
				    </head>

				    <body>
					<h1>Welcome to <strong>nginx</strong> on Red Hat Enterprise Linux!</h1>

					.......................................................................
					.......................................................................
					.......................................................................

					    <div class="logos">
						<a href="http://nginx.net/"><img
						    src="nginx-logo.png" 
						    alt="[ Powered by nginx ]"
						    width="121" height="32" /></a>
						<a href="http://www.redhat.com/"><img
						    src="poweredby.png"
						    alt="[ Powered by Red Hat Enterprise Linux ]"
						    width="88" height="31" /></a>
					    </div>
					</div>
				    </body>
				</html>		

5. SSL

	5.1 Criação de certificados

	Utilizando o comando de sua preferencia (openssl, cfssl, etc...) crie uma autoridade certificadora (CA) para o hostname desafio.local. Em seguida, utilizando esse CA para assinar, crie um certificado de web server para o hostname www.desafio.local.
	
		iptables -A INPUT -p tcp --dport 443 -j ACCEPT
		firewall-cmd --permanent --add-service=http
		firewall-cmd --permanent --add-service=https
		firewall-cmd --permanent --zone=public --add-port=443/tcp
		firewall-cmd --reload
		
		chmod -R 755 /usr/share/nginx
		yum install mod_ssl
		yum install openssl openssl-perl		
		mkdir /root/CA
		
		CA.pl -newca
		passphrase [c53kwpko]
		parâmetros : BR, SaoPaulo, SaoPaulo, Getup, FormandoDevops, desafio.local, desafio_local@getup.com
		cakey.pem [c53kwpko]
		
		CA.pl -newrq
		parâmetros: BR, SaoPaulo, SaoPaulo, Getup, FormandoDevops, www.desafio.local, desafio_local2@getup.com
		challenge password [vazio]
		
		CA.pl -sign
	
	5.2 Uso de certificados	

	Utilizando os certificados criados anteriormente, instale-os no serviço nginx de forma que este responda na porta 443 para o endereço www.desafio.local. Certifique-se que o comando abaixo executa com sucesso e responde o mesmo que o desafio 4. Voce pode inserir flags no comando abaixo para utilizar seu CA.

	curl https://www.desafio.local
	
		cp newcert.pem /etc/pki/ngix/
		cp newkey.pem /etc/pki/nginx/private/
		
		vim /etc/nginx/nginx.conf
		# descomentar a seção do Servidor TLS		
		ssl_certificate "/etc/pki/nginx/newcert.pem";
		ssl_certificate_key "/etc/pki/nginx/private/newkey.pem";
		sysmtemctl restart nginx
		
		! importante: no processo de criação da chave é exigido uma passphrase, mas o nginx não vai conseguir
		subir desse modo, e o que pode ser feito é remover a passphrase depois que a chave foi gerada:
		
		openssl rsa -in newkey.pem -out newkey.pem
		
		! outra informação importante é sobre o curl, que não aceitava a CA que eu havia criado, mas foi possível
		contornar isso usando a flag -k (que ignora esse problema). No entanto, tentei encontrar uma maneira de usar
		o comando sem a flag, e apesar de não ter conseguido, seguem os passos que eu tentei:
		
		cat newcert.pem newreq.pem > desafio.ssl		
		vim /etc/nginx/nginx.conf
		ssl_certificate "/etc/pki/nginx/desafio.ssl"
		systemctl restart nginx
		
6. Rede

	6.1 Firewall

	Faço o comando abaixo funcionar:

	ping 8.8.8.8

		O comando já estava funcionando na máquina do desafio, talvez por eu ter alterado algumas regras do firewall
		previamente, mas caso não estivesse funcionando uma solução seria acrescentar essa regra no firewall:

		iptables -A INPUT -p icmp --icmp-type 8 -s $WAN -j ACCEPT

		Onde:
		s -> indica network
		i -> interface de entrada
		o -> interface de saida

	6.2 HTTP

	Apresente a resposta completa, com headers, da URL https://httpbin.org/response-headers?hello=world

		curl -i https://httpbin.org/response-headers?hello=world

		HTTP/2 200 
		date: Sun, 04 Sep 2022 07:23:14 GMT
		content-type: application/json
		content-length: 89
		server: gunicorn/19.9.0
		hello: world
		access-control-allow-origin: *
		access-control-allow-credentials: true

		{
		  "Content-Length": "89", 
		  "Content-Type": "application/json", 
		  "hello": "world"
		}

Logs

Configure o logrotate para rotacionar arquivos do diretório /var/log/nginx
	
		O log rotate já estava devidamente configurado, mas caso contrário os seguintes passos seriam necessários:
		
		yum install logrotate
		vim /etc/logrotate.conf (verificar se /etc/logrotate.d está na sessão "include")
		vim /etc/logrotate.d./nginx
		
			var/log/nginx/*.log {
			daily
			missingok
			rotate 10
			compress
			delaycompress
			notifempty
			create 0640 www-data adm
			sharedscripts
			postrotate
				invoke-rc.d nginx rotate >/dev/null 2>&1
			endscript
		}
		
		onde:
			/var/log/nginx/*.log: rotacionar todos os arquivos de log no diretório
			daily: rotacionar diariamente
			missingok: erros não podem ser registros escritos
			notifempty: logs não serão rotacionados se estiverem vazios
			rotate 10: mantem os últimos 10 arquivos de log 
			prerotate/postrotate: diz ao logrotate que o script a ser executado começa na próxima linha
			endscript: indica o fim do script
			
		testando a configuração do logrotate:
			
			logrotate -d /etc/logrotate.conf -> força a execução do logrotate
			cat /var/lib/logrotate/logrotate.status -> mostra os arquivos rotacionados			
		

7. Filesystem
	
	7.1 Expandir partição LVM

	Aumente a partição LVM sdb1 para 5Gi e expanda o filesystem para o tamanho máximo.
	
	7.2 Criar partição LVM

	Crie uma partição LVM sdb2 com 5Gi e formate com o filesystem ext4.
	
		Nessa tarefa procurei aproveitar a partição que já iria criar no 7.2 para poder estender
		a partição LVM sdb1 para 5G, que tinha apenas 1G de tamanho e que já estava sem espaço.	
			
		Comandos utilizados para identificar o mapeamento entre volumes e partições
		e também para checagem dos comandos e do resultado: 
	
			df -hT, fdisk -l, pvs, vgs, lvs, pvdisplay, vgdisplay, lvdisplay
	
		fdisk /dev/sdb -> para criar uma partição extendida /dev/sdb2		
		mkfs.ext4 /dev/sdb2
		pvcreate /dev/sdb2
		vgextend data_vg /dev/sdb2
		lvextend -L 5G /dev/data_vg/data_lv
		umount /data
		e2fsck /dev/data_vg/data_lv
		resize2fs /dev/data_vg/data_lv
		mount /data

	7.3 Criar partição XFS

	Utilizando o disco sdc em sua todalidade (sem particionamento), formate com o filesystem xfs.
	
		yum install xfsprogs
		mkfs.xfs /dev/sdc
