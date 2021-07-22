
<h1>(WEBDEV) AMBIENTE COMPLETO PARA DESENVOLVIMENTO WEB COM: [SSH / SMB / GIT / VAGRANT / HOMESTEAD / BIND / DNS]</h1>


<hr />

<h3>Sobre:</h3>

<p>Esse artigo tem como objetivo demonstrar passo a passo um caminho alternativo para a contrução de um ambiente completo para desenvolvimento de aplicações web com um servidor local e cliente qualquer maquina que estiver na mesma rede.</p>
 
<hr />

<h3>Requisitos:</h3>

<ul>
	<li>Vagrant</li>
	<li>Virtualbox</li>
	<li>Laravel</li>
	<li>Homestead</li>
	<li>Bind9</li>
	<li>DNS Utils</li>
	<li>Openssh</li>
</ul>

<hr />

<h3>Instalando Ambiente:</h3>


<h4>Instalar e configurar o serviço de SSH (OPENSSH SERVER)</h4>

<pre>
sudo apt-get install openssh-server
</pre>


<h4>Configurar o compartilhamento de pastas entre sitemas Linux</h4>

<pre>
Para compartilhar pastas entre sitemas Linux, basta clicar com o botão direito na pasta que deseja compartilhar e escolher "Compartilhamento de rede local".
</pre>


<h4>Instalando o GIT</h4>

<pre>
git --version
sudo apt install git
git config --global user.name "Your Name"
git config --global user.email "youremail@domain.com"
git config --list
</pre>

<h4>Instalar o VAGRANT</h4>
<pre>
sudo apt-get install composer
sudo apt-get install vagrant
</pre>

<pre>
vagrant box add laravel/homestead
2) virtualbox (provider)
==> box: Successfully added box 'laravel/homestead' (v11.0.0) for 'virtualbox'!
/home/{{{USUARIO}}}/Homestead/ "(nada nessa pasta)"
/home/{{{USUARIO}}}/Vagrant/homestead/ "(nada nessa pasta)"
/home/{{{USUARIO}}}/VirtualBox VMs/ "(nada nessa pasta)"
/home/{{{USUARIO}}}/.vagrant.d/boxes/laravel-VAGRANTSLASH-homestead/11.0.0/virtualbox/ "(local da imagem baixada com vagrant: 1.8GB)"
</pre>

<h5>Clonar o projeto HOMESTEAD da laravel e configurar o ambiente</h5>
<h6>NOTA: Repare que o local de instalação do projeto é na home do usuário</h6>
<pre>git clone https://github.com/laravel/homestead.git ~/Homestead</pre>

<h5>Acessar a pasta do projeto</h5>
<pre>cd ~/Homestead/</pre>

<h5>Mudar para a branch release do repositorio laravel/homestead</h5>
<pre>git checkout release</pre>

<h5>Inicializar o homestead</h5>
<h6>NOTA: Esse procedimento gera um arquivo Homestead.yaml na raiz do projeto</h6>
<pre>bash init.sh</pre>

<h5>Gerar a chave publica para o ambiente homestead</h5>
<pre>
ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/{{{USUARIO}}}/.ssh/id_rsa): /home/{{{USUARIO}}}/.ssh/id_rsa_homestead
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/{{{USUARIO}}}/.ssh/id_rsa_homestead
Your public key has been saved in /home/{{{USUARIO}}}/.ssh/id_rsa_homestead.pub
The key fingerprint is:
SHA256:vSlzwxINgpXBSD123456YpLDGVUn35E+2+DdyKMaaU4 {{{USUARIO}}}@{{{USUARIO}}}-ubuntu-desktop
The keys randomart image is:
+---[RSA 3072]----+
|.+.++Oo+ ..      |
|+o= =o* ...      |
|*+xytp..o.       |
|oo     . B       |
|        S X o    |
|        .= O .   |
|       E+ B .    |
|      + .* .     |
|       o.        |
+----[SHA256]-----+
{{{USUARIO}}}@{{{USUARIO}}}-ubuntu-desktop:~/Homestead$ ls -ltr ~/.ssh/
total 24
-rw-r--r-- 1 {{{USUARIO}}} {{{USUARIO}}}  573 jan  9 17:36 id_rsa_ubuntu_desktop_devel.pub
-rw------- 1 {{{USUARIO}}} {{{USUARIO}}} 2655 jan  9 17:36 id_rsa_ubuntu_desktop_devel
-rw-r--r-- 1 {{{USUARIO}}} {{{USUARIO}}}  270 jan  9 17:39 config
-rw-r--r-- 1 {{{USUARIO}}} {{{USUARIO}}} 3316 fev 11 22:46 known_hosts
-rw-r--r-- 1 {{{USUARIO}}} {{{USUARIO}}}  588 fev 12 20:20 id_rsa_homestead.pub
-rw------- 1 {{{USUARIO}}} {{{USUARIO}}} 2622 fev 12 20:20 id_rsa_homestead
</pre>

<h5>Configurar arquivo</h5>
<h6>NOTA: Use um editor de texto qualquer</h6>
<pre>sublime-text.subl Homestead.yaml</pre>
<pre>
    ---
    ip: "192.168.10.10"
    memory: 2048
    cpus: 2
    provider: virtualbox
    
    authorize: ~/.ssh/id_rsa_homestead.pub
    
    keys:
        - ~/.ssh/id_rsa_homestead
    
    folders:
        - map: ~/Devel
          to: /home/vagrant/code
    
    sites:
        - map: site1.local
          to: /home/vagrant/code/site1
    
        - map: site2.local
          to: /home/vagrant/code/site2/public
    
    databases:
        - homestead
    
    features:
        - mysql: false
        - mariadb: false
        - postgresql: false
        - ohmyzsh: false
        - webdriver: false
    
    networks:
      - type: "public_network"
        ip: "192.168.15.100" #Exemplo
    
    #services:
    #    - enabled:
    #        - "postgresql@12-main"
    #    - disabled:
    #        - "postgresql@11-main"
    
    # ports:
    #     - send: 50000
    #       to: 5000
    #     - send: 7777
    #       to: 777
    #       protocol: udp
</pre>

<h5>Definir resolução de nomes para os sites/projetos dentro do homestead</h5>
<h6>ATENÇÃO: Essa configuração deve ser feito no GUEST/HOST que esta hospedando o homestead atraves do hipervisor vagrant e não dentro da instancia vagrant/homestead !</h6>
<pre>
configurar /etc/hosts
</pre>
<pre>
127.0.0.1	localhost

# Sites
192.168.10.10	site1.local
192.168.10.10	site2.local
</pre>

<h5>Inicializar o ambiente com o vagrant</h5>
<h6>NOTA: Nesse ponto a imagem baixada pelo vagrant é incorporada ao virtualbox</h6>
<pre>
vagrant up --provision
</pre>

<strong>
!IMPORTANTE: Pode ser que em algum momento o vagrant questionar sobre qual placa de rede deve ser utilizada como bridge, conforme exemplo abaixo:
</strong>
<small>Veja que a placa escolhida foi a opção 1) enp4s0</small>

<pre>
==> homestead: Available bridged network interfaces:
1) enp4s0
2) docker0
3) br-961d95a56c4c
4) br-c75d8ef33fcc
==> homestead: When choosing an interface, it is usually the one that is
==> homestead: being used to connect to the internet.
    homestead: Which interface should the network bridge to? 1
==> homestead: Preparing network interfaces based on configuration...
    homestead: Adapter 1: nat
    homestead: Adapter 2: hostonly
    homestead: Adapter 3: bridged
</pre>

<pre>
# local onde ficam os arquivos e configurações da maquina
/home/{{{USUARIO}}}/Homestead/.vagrant/machines/homestead/virtualbox/

# local da imagem baixada com vagrant: 1.8GB
/home/{{{USUARIO}}}/.vagrant.d/boxes/laravel-VAGRANTSLASH-homestead/11.0.0/virtualbox/

# local da VM no Virtual Box: 6.4GB
/home/{{{USUARIO}}}/VirtualBox VMs/homestead/
</pre>

<h5>EXTRAS: Comandos para auxiliar no vagrant</h5>
<pre>
vagrant halt  => "Desligar VM"
vagrant ssh ou ssh vagrant@192.168.10.10 => "Acesso via ssh na VM"
vagrant resume => "Continuar VM pausada"
vagrant reload --provision => "Reinciiar VM"
vagrant destroy ff40a74 (id) => "Remover VM"
vagrant global-status => "Verificar status e ids das VMs"
</pre>

* Configuração Do Ambiente Homestead Finalizado!


<h4>Configurar VAGRANT e HOMESTEAD para acesso via rede</h4>

<strong>NOTA: Esse procedimento ira permitir que computadores que estão na mesma rede possam acessar a aplicação contida no Vagrant/Homestead !</strong>

<h5>Adicionar as linhas abaixo no arquivo Homestead.yaml</h5>
<pre>
networks:
  - type: "public_network"
    ip: "192.168.15.100"
</pre>

<strong>!IMPORTANTE: Ter o cuidado de fixar e setar o endereço de IP, pois ele sempre será servido no mesmo endereço.</strong>

<h5>Adicionar o bloco de código abaixo, caso não existam no arquivo homestead.rb</h5>
<h6>O arquivo fica em: /home/{{{USUARIO}}}/Homestead/scripts</h6>
<pre>
# Configure A Private Network IP
if settings['ip'] != 'autonetwork'
  config.vm.network :private_network, ip: settings['ip'] ||= '192.168.10.10'
else
  config.vm.network :private_network, ip: '0.0.0.0', auto_network: true
end

//Adicionar essas linhas nessa posição (caso não exista)
# Configure Additional Networks
if settings.has_key?('networks')
  settings['networks'].each do |network|
    config.vm.network network['type'], ip: network['ip'], mac: network['mac'], bridge: network['bridge'] ||= nil, netmask: network['netmask'] ||= '255.255.255.0'
  end
end
</pre>


<h4>Instalar e configurar o serviço de DNS</h4>

<h5>BIND9</h5>
<pre>
sudo apt-get install bind9
</pre>

<h5>DNSUTILS</h5>
<pre>
sudo apt-get install dnsutils
</pre>


<h4>Configurar o serviço de DNS</h4>

<p>Após instalar o bind9 e o dnsutils, as seguintes configurações serão necessárias</p>

<h5>Editar o arquivo named.conf.local</h5>
<h6>Adicionar as zonas(sites) que serão ouvidos pelo servidor DNS, abaixo um modelo de como o arquivo ficaria para dois dominios.</h6>
<pre>
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";


//Dominio forward
zone "site1.local" {
	type master;
	file "/etc/bind/db.site1";
};

zone "site2.local" {
	type master;
	file "/etc/bind/db.site2";
};

//Dominio reverse
zone "15.168.192.in-addr.arpa" {
	type master;
	file "/etc/bind/db.192";
	
};
</pre>

<h5>Copiar um arquivo de exemplo em /etc/bind:</h5>
<pre>
cp db.local db.site1
cp db.local db.site2
</pre>

<h5>Editar o arquivo copiado /etc/bind/db.site1, conforme exemplo abaixo:</h5>
<small>O exemplo abaixo deve ser usado para as demais zonas, no lugar de localhost colocar o dominio(zona) do site</small>
<pre>
;
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	site1.local. root.site1.local. (
			      2		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	site1.local.
@	IN	A	192.168.15.100
@	IN	AAAA	::1

</pre>

<h5>Editar o arquivo /etc/resolv.conf</h5>
<strong>Cuidado: Esse arquivo é utilizado pelo sistema operacional para resolução de nomes e direcionamento diversos na internet</strong>
<pre>
# This file is managed by man:systemd-resolved(8). Do not edit.
#
# This is a dynamic resolv.conf file for connecting local clients to the
# internal DNS stub resolver of systemd-resolved. This file lists all
# configured search domains.
#
# Run "resolvectl status" to see details about the uplink DNS servers
# currently in use.
#
# Third party programs must not access this file directly, but only through the
# symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,
# replace this symlink by a static file or a different symlink.
#
# See man:systemd-resolved.service(8) for details about the supported modes of
# operation for /etc/resolv.conf.

nameserver 127.0.0.53
options edns0 trust-ad
search br

nameserver 192.168.15.101 #Inserir um nameserver pertinente a rede em questão

</pre>

<h5>Criar e Configurar o arquivo de Reverse IP nslookup</h5>
<pre>
cp db.127 db.192 //192 referente ao IP em questão usado na rede
</pre>

<pre>
;
; BIND reverse data file for local loopback interface
;
$TTL	604800
@	IN	SOA	site1.local. root.site1.local. (
			      1		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	site1.local.
1.0.0	IN	PTR	site1.local.
</pre>

<strong>Reiniciar o serviço do bind9</strong>
<pre>
sudo service bind9 restart
sudo service bind9 status
</pre>


<h4>Configurar arquivo /etc/hosts no cliente que ira consumir os websites</h4>

<strong>Exemplo: REPARE O IP É O MESMO USADO NO ARQUIVO Homestead.yaml para disponibilizar acesso a rede em modo bridge</strong>
<pre>
127.0.0.1	localhost
127.0.1.1	ubuntu-desktop

# Homestead
192.168.15.100	site1.local
192.168.15.100	site2.local

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
</pre>


<h4>Configurar DNS no cliente</h4>

<p>
Vá até configurações de rede, procure por IPv4, DNS, e coloque o DNS (nameserver) que foi criado mais acima nesse artigo, por exemplo:
</p>
<p>Linux:</p>
<pre>192.168.15.101,8.8.8.8,4.4.4.4</pre>
<p>Windows:</p>
<pre>
DNS Primario: 192.168.15.101
DNS Alternativo: 8.8.8.8
</pre>


<h4>Testar ambiente</h4>

<p>
-Para acessar a aplicação use um navegar e informe o nome do dominio local na barra de endereço: http://site1.local.
</p>
<p>
-Se tudo estiver correto, o redirecionamento e resolução de nomes deve ocorrer corretamente.
</p>
