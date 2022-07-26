# getting-started-arch
setfont ter-224b.psf.gz => aumentar o tamanho da fonte

loadkeys br-abnt2 => deixar o layout do teclado em pt br

ping [google.com](http://google.com/) => checar se está conectado na rede

fdisk -l => listar os discos, normalmente o disco com o path /dev/sda é o disco que você vai fazer partição

fdisk  /dev/sda

**seleciona "m" para ver o menu e depois verificar:**

**Selecionar G -> Se o modelo da BIOS for UEFI, selecionar uma tabela GPT**

**Selecionar O -> Na maquina virtual ele reconhece como BIOS BOOT, logo preciso criar uma tabela DOS**

**Selecionar W para escrever no disco e limpar tudo o que tem no disco**

**cfdisk /dev/sda -> cria uma partição no disco**

**Selecionar New com as setas**

**Particionamento**
Selecionei 50G para Barra -> Local onde instala os programas
Selecionei 70G para Home -> local onde instala arquivos pessoais

**Se tiver usando UEFI tem que marcar uma partição como BIOS BOOT, precisa ter pelo menos 500 megas, para caber o GRUB, no caso o boot loader**

**Para isso selecionar type e bios boot**

**OBS: Tabela DOS não precisa disso pois estou usando uma VM**

**Selecionar QUIT e sair**

**Escolher o sistema de Arquivos para usar -> EXT4**

mkfs.ext4 /dev/sda1 -> make file system -> selecionando o sistema de arquivos no terminal
(fazer pro /dev/sda2 também)

**Adicionar os pontos de montagem**

mount /dev/sda1 /mnt -> mnt é a raiz do sistema. O sd1 é o mnt

mkdir /mnt/home -> criando a pasta home dentro do mnt -> home fica a pasta dos usuários
mkdir /mnt/boot -> criando a pasta boot dentro do mnt -> fica o boot loader pra guardar informações sobre boot

**Agora precisa adicionar o sd2 no home**
mount /dev/sda2 /mnt/home -> tudo o que tiver na partição home, vai estar no disco sda2

**Baixar os comandos fundamentais**

pacstrap /mnt base base-devel linux linux-firmware

**Agora precisa gerar o genfstab, é um arquivo de texto comum pra registrar as partições**
genfstab -U -p /mnt >> /mnt/etc/fstab

**Agora precisa entrar no sistema**
arch-chroot /mnt

**Agora é hora de baixar os pacotes do sistema**

pacman -S vim grub dosfstools os-prober network-manager-applet networkmanager mtools dialog sudo

**Pra instalar o GRUB na BIOS LEGACY (no caso da VM), usar este comando:**
grub-install --target=i386-pc --recheck /dev/sda
OBS: Se a bios for UEFI é de outra forma, procurar na archwiki

**Copiar um arquivo**
cp /usr/share/locale/en@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo

**Criar o arquivo de configuração do GRUB**
grub-mkconfig -o /boot/grub/grub.cfg

**Sincronizar o horário do sistema operacional com o da placa mãe**
hwclock --systohc

**Precisa editar o locale do sistema com o VIM**
vim /etc/locale.gen

Selecionar com o "i" o arquivo pt_BR.UTF-8 UTF-8
Aperta esc e escreve :wq pra sair salvar e sair

**Depois inserir no terminal:**
locale-gen

**Editar o arquivo**
vim /etc/locale.conf
**Depois Inserir**
LANG=pt_BR.UTF-8

**Agora precisa editar outro arquivo**
vim /etc/vconsole.conf
**E Inserir**
KEYMAP=br-abnt2

**Editar outro arquivo**
vim /etc/hostname
Digitar **arch**
(Esse arquivo vai guardar o nome do computador que vai ficar na rede, no caso arch)

**Editar outro arquivo**
vim /etc/hosts

**Inserir**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e09fdcbb-f579-4a7a-8d28-ff542e2700cd/Untitled.png)

**Criar uma senha para o usuário root**

passwd

coloquei a do windows mesmo 

**Criar um usuário novo (usar o root como usuário padrão é perigoso e abre margem para invasão)**

useradd -m -g users -G wheel matheus

**Editar o arquivo sudoers** 

vim /etc/sudoers

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/420904af-6ef6-488a-be32-1bc3ac9e9319/Untitled.png)

OBS: o meu ficou como matheus ALL(ALL) ALL

parei nos 35:15