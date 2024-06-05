---
title: "Vagrant Descomplicado: Um Guia Prático"
date: 2024-05-20 00:00:00:0000
categories: [IaC]
tags: [IaC]
---

# Vagrant Descomplicado: Um Guia Prático
A minha ideia é te ensinar a usar as principais funcionalidades do Vagrant em apenas um artigo, de forma simplificada e rápida. Você vai aprender o que é, para que serve, quais as suas funcionalidades e vai subir uma VM comigo no decorrer do artigo!

## Tópicos
1. [Introdução](#introdução)
    - [O que é IaC?](#o-que-é-iac)
    - [O que é um Hypervisor?](#o-que-é-um-hypervisor)
    - [O que é uma VM?](#o-que-é-uma-vm-virtual-machine)
    - [O que é o Vagrant?](#o-que-é-o-vagrant)
2. [Instalando o Ambiente](#instalando-o-ambiente)
3. [Iniciando com uma Box](#iniciando-com-uma-box)
4. [Gerenciando uma Box](#gerenciando-uma-box)
    - [Criando um VagrantFile](#criando-um-vagrantfile)
    - [Comandos para Manipular uma VM](#comandos-para-manipular-uma-vm)
    - [Configurando Redes, Hostname e Liberando Porta na VM](#configurando-redes-hostname-e-liberando-porta-na-vm)
    - [Scripts Shell para Automatizar Tarefas](#scripts-shell-para-automatizar-tarefas)
5. [Criando uma BOX](#criando-uma-box)

## Introdução

### O que é IaC?
IaC, ou Infraestrutura como Código, é uma abordagem na qual a infraestrutura de TI é gerenciada e provisionada usando código, em vez de configurações manuais. Sendo assim, facilita a automação e gestão ágil dos ambientes, chega de "na minha máquina funciona".

### O que é um Hypervisor?
Um hypervisor é um software ou firmware que permite a execução de máquinas virtuais (VMs) em um único hardware físico. Ele age como uma camada de virtualização entre o hardware físico do computador e os sistemas operacionais convidados, permitindo que vários sistemas operacionais compartilhem o mesmo hardware simultaneamente. Nós iremos utilizar VirtualBox para subir as nossas VMs com o Vagrant.

### O que é uma VM (Virtual Machine)?
É um software que simula um ambiente de computador físico e permite a execução de sistemas operacionais e aplicativos como se estivessem em hardware físico real.

### O que é o Vagrant?
É uma ferramenta de linha de comando utilizada para agilizar todo o WorkFlow de uma VM, também, controlar todo o LifeCycle da VM. Nós manipulamos um Hypervisor por meio de comandos Vagrant, ele traduz os comandos enviados para os comandos do Hypervisor utilizado. O Vagrant abstrai detalhes específicos do hypervisor, proporcionando uma abordagem consistente e portátil para configuração de ambientes. O uso de arquivos de configuração (Vagrantfiles) permite uma configuração fácil e rápida de ambientes.

Imagina que você subiu um ambiente de Dev com tudo, quer adicionar esse ambiente em um outro servidor ou enviar para uma pessoa? Compartilhe o VagrantFile.

## Instalando o Ambiente
Você precisa apenas fazer o download e instalar. Depois, podemos avançar para os próximos tópicos.
- [Hypervisor que utilizo: VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Vagrant](https://developer.hashicorp.com/vagrant/downloads)

## Iniciando com uma Box
Uma Box é basicamente o sistema operacional que você vai virtualizar. Ou seja, uma Box é uma VM empacotada em um arquivo chamado Box. O Vagrant tem uma comunidade onde as pessoas compartilham as suas Boxes, chamada Vagrant Cloud. Mas você também pode criar uma Box própria.

Imagine que no seu ambiente você utiliza o SQL Server, Power BI e outras ferramentas. Com o Vagrant, você poderia baixar uma Box com apenas o sistema operacional Windows, instalar o que você precisa e, depois, fechar uma Box sua. Assim, **você iria compartilhar uma Box pronta para os ambientes ou compartilhar com a comunidade!**
- [Vagrant Cloud](https://app.vagrantup.com/boxes/search)


### Comandos:
Vamos imaginar que você quer baixar o Box do windows no seu servidor
```sh
vagrant box add gusztavvargadr/windows-server-2019-standard
```

Baixou algumas Boxes? Vamos lista-las
```sh
vagrant box list
```

Listar opções de comando do vagrant box (Você pode usar o -h para todos os comandos)
```sh
vagrant box -h (Geral)

vagrant box list -h  (Específico)
```

Quer verificar se existe alguma Box desatualizada no seu servidor?
```sh
vagrant box outdated (No próprio diretório)

vagrant box outdated -- global (Vai verificar todas que tenho no servidor)
```

Quer remover a que está desatualizada?
```sh
vagrant box prune (Vai remover todas as desatualizadas)
```

Ou... atualizar as mesmas sem danificar?
```sh
vagrant box update
```

Caso você queira remover apenas uma Box específica.
```sh
vagrant box remove vm/name
```

Esses são os comandos principais para você utilizar e baixar uma Box, vou deixar o meu cmd como exemplo:
- ![example_cmd](/assets/images/2024-05-20-basic-start-vagrant/example_cmd.png)

## Gerenciando uma Box
O VagrantFile é a magia do Vagrant, do que adianta ter uma VM se você não consegue manipular a mesma? Bom, isso acabou...

O VagrantFIle é o local em que adicionaremos/modificaremos todas as configurações necessárias para subir a nossa VM. Você pode configurar opções de rede, hardware e até mesmo Shell Scripts para automatizar instalações. 

### Criando um VagrantFile
Essa é a etapa que interessa, é aqui que vamos iniciar a primeira etapa com o Vagrant.

#### Comandos:
Para criar um VagrantFile, utilize o comando no seu repositório:
```sh
vagrant init vm/name
```

Ele vai verificar se a Box já foi instalada (Instalamos com o vagrant box add) Se ela não estiver sido instalada, ele irá instalar.
- ![init_cmd_example](/assets/images/2024-05-20-basic-start-vagrant/init_cmd_example.png)

Esse é um exemplo do **VagrantFile** criado (Ele vem cheio de comentários, eu removi e deixei só o que importa nesse primeiro momento): 
```sh
Vagrant.configure("2") do |config|
  config.vm.box = "peru/windows-server-2019-standard-x64-eval" # Box utilizada 
  config.vm.provider "virtualbox" do |vb| # Hypervisor utilizado 
      vb.memory = "1024" # Memória disponibilizada
      vb.cpus = 10  # Configuração de CPUs
      vb.name = "Article Test 01" # Nome da VM no Hypervisor
  end
end
```

Pronta para subir a mesma? Só rodar o seguinte comando no diretório:
```sh
vagrant up
```

E aguardar...
- ![up_vagrant](/assets/images/2024-05-20-basic-start-vagrant/up_vagrant.png)

Mas em poucos instantes, sua VM está no ar!
- ![vm](/assets/images/2024-05-20-basic-start-vagrant/vm.png)

### Comandos para Manipular uma VM
Vou listar alguns dos comandos principais que você precisa saber para manipular a sua VM no ar.

#### Comandos:
Verificar o Status da VM
```sh
vagrant status
```

Desligar a VM
```sh
vagrant halt
```

Subir a VM (Se existir - Liga/Se não - Cria)
```sh
vagrant up
```

Suspende a VM
```sh
vagrant suspend
```

Reiniciar a VM
```sh
vagrant reload
```

Destruir ou Remover a VM
```sh
vagrant destroy (y/n?)
```

### Configurando Redes, Hostname e Liberando Porta na VM

Para configurar as redes, nós temos duas opções: **NAT ou BRIDGE.**

- **NAT -** É uma tecnologia usada para compartilhar uma conexão de internet com vários dispositivos em uma rede local. O roteador recebe um endereço IP público da internet e atribui um endereço privado a cada dispositivo na rede local. Assim, o roteador traduz os endereços IPs privados para os público, permitindo que dispositivos na rede acessem a internet.

- **BRIDGE -** É uma tecnologia utilizada para conectar duas ou mais redes locais em uma única rede. Uma ponte (Bridge) é um dispositivo que conecta duas redes e permite que os dispositivos em uma rede se conectem aos dispositivos de outra rede, como se estivessem na mesma rede.

No meu exemplo, eu vou escolher o NAT.

**Exemplos:**
Conexão NAT - DHCP.
```sh
Vagrant.configure("2") do |config| 
  config.vm.network "private_network", type: "dhcp" 

  config.vm.box = "peru/windows-server-2019-standard-x64-eval" # Box utilizada

end
```

Conexão NAT - IP Fixo
```sh
Vagrant.configure("2") do |config| 
    config.vm.network "private_network", ip: "192.168.25.10" 

    config.vm.box = "peru/windows-server-2019-standard-x64-eval" # Box utilizada
end
```

Após definirmos o tipo da rede, **podemos fornecer um exemplo de porta.** 

Suponha que a máquina virtual (VM) que você está iniciando seja responsável por um servidor SQL, e que seja necessário estabelecer uma conexão com o SQL Server. Para realizar isso, **é necessário liberar a porta associada ao SQL Server - por padrão é a 1433.**
```sh
Vagrant.configure("2") do |config| 
  config.vm.network "forwarded_port", guest: 1433, host: 1433
  
  config.vm.box = "peru/windows-server-2019-standard-x64-eval" # Box utilizada 
end
```

- **`guest: 1433`**: Especifica que a porta no ambiente da máquina virtual (o "convidado") é a 1433.

- **`host: 1433`**: Indica que a porta no ambiente do host (a máquina física que está executando a VM) é também 1433.

O Hostname é a última etapa que quero abordar neste capítulo, com ele vamos especificar um nome para a conexão em nossa rede local. Por exemplo, nós criamos a nossa rede em NAT, para conectar na máquina remotamente, precisaremos nos conectar pelo IP. O problema disso é: **nosso IP não é fixo!**

Quando utilizamos o hostname, isso não é necessário. **Podemos nos conectar pelo hostname!**
```sh
Vagrant.configure("2") do |config|   
  config.vm.box = "peru/windows-server-2019-standard-x64-eval" # Box utilizada

  config.vm.hostname = "VMEXARTICLE"

end
```

Assim ficou o nosso VagrantFile após os aprendizados deste capítulo:
```sh
Vagrant.configure("2") do |config|
  config.vm.box = "peru/windows-server-2019-standard-x64-eval"

  config.vm.network "forwarded_port", guest: 1433, host: 1433 
  config.vm.network "private_network", type: "dhcp"

  config.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 10
      vb.name = "Article Test 01" # Nome da VM no Hypervisor
  end

  config.vm.hostname = "VMEXARTICLE"

end
```

Você pode reiniciar a VM para aplicar as alterações. Para testar o hostname, faça um **PING VMEXARTICLE**.

### Scripts Shell para Automatizar Tarefas
Isso sem sombra de dúvidas é o que mais amo no Vagrant, você pode rodar Scripts Shell (Ou Bash Scripts se for Linux), de forma totalmente automatizada.

Nesse exemplo eu vou liberar o Firewall da máquina, você pode fazer de duas maneiras.

Especificando o comando direto no VagrantFile 
```sh
 config.vm.provision "shell", inline: <<-SHELL
    netsh advfirewall set allprofiles state off
  SHELL
```

Especificando um path para o comando (Ele precisa estar no mesmo diretório do VagrantFile)
```sh
config.vm.provision "shell", path: "disable_firewall.ps1"
```

Quero que **você dê uma pausa aqui e pense...** quantas coisas é possível fazer com essa automatização?

Caso a sua máquina tenha subido, não é necessário reiniciar para que os scripts Shell rodem. Você pode realizar o seguinte comando:
```sh
vagrant provision
```

Ele vai identificar qual parte no VagrantFile possue o "config.vm.provision" e rodar todos. Segue exemplo:
- ![vagrant_provision](/assets/images/2024-05-20-basic-start-vagrant/vagrant_provision.png)

**VagrantFile final:**
```sh
Vagrant.configure("2") do |config|
  config.vm.box = "peru/windows-server-2019-standard-x64-eval"

  config.vm.network "forwarded_port", guest: 1433, host: 14330

  config.vm.network "private_network", type: "dhcp"

  config.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 10
      vb.name = "Article Test 01" 
  end

  config.vm.hostname = "VMEXARTICLE"

  config.vm.provision "shell", inline: <<-SHELL
    netsh advfirewall set allprofiles state off
  SHELL

end
```

***Simples, não? ;)***

## Criando uma BOX
Imagine esse cenário agora: Você instalou tudo que era necessário para o ambiente do projeto e deseja **escalar o mesmo para o servidor de produção.**

O que podemos fazer? Nós podemos **fechar a NOSSA Box - vamos empacotar a Box em um arquivo .box**

#### Exemplo:
Criando o arquivo .box da nossa VM
```sh
vagrant package --output article_test.box
```
- **`vagrant package`**: é o comando, você pode adicionar o -h para ver as outras opções do mesmo
- **`--output`**: Complemento do comando para exportar a Box
- **`article_test.box`**: Nome dado para a nossa Box
- ![box_cmd](/assets/images/2024-05-20-basic-start-vagrant/box_cmd.png)

Após criar o arquivo .box, **você precisa adicionar ela em sua lista de Boxes.** Igual fizemos no primeiro exemplo, quando puxamos a nossa VM do Vagrant Cloud. Porém, agora vamos buscar localmente!

Adicionando a nossa Box na lista de Boxes
```sh
vagrant box add --name article-test article_test.box
```
- **`vagrant box add`**: Comando para criar a Box
- **`-- name article-test`**: Nome que você vai dar para a Box
- **`article_test.box`**: Nome que foi dado para o arquivo .Box

Pronto! Você tem uma infra como código agora...

Se te interessou, pesquise mais sobre o assunto, o Vagrant tem muito mais a oferecer. Garanto que não vai se arrepender!

---

<center>

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
</center>
