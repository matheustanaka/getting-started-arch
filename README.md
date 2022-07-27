# Arch Linux
Aprendendo a Instalar o Arch na VM (virtual machine)

# Colocando display no teclado e aumentando fonte
```bash
# aumentar o tamanho da fonte 
$ setfont ter-224b.psf.gz 
# deixar o layout do teclado em pt br
$ loadkeys br-abnt2 
```

# Checando conexão com a Internet
```bash
# checar se está conectado na rede
$ ping google.com  
```

# Particionamento
```bash
# listar os discos
$ fdisk -l
# Disco para fazer particionamento
$ fdisk  /dev/sda
```
- Selecionar "M" -> para ver o menu e depois verificar:

- Selecionar "G" -> Se o modelo da BIOS for UEFI, selecionar uma tabela GPT

- Selecionar "O" -> Na maquina virtual ele reconhece como BIOS BOOT, logo preciso criar uma tabela DOS

- Selecionar "W" -> para escrever no disco e limpar tudo o que tem no disco (vai formatar o disco inteiro)

```bash
# Cria uma partição no disco
$ cfdisk /dev/sda
```
- Selecionar New com as setas do teclado

### Distribuição de espaço nos discos
- Selecionei 50G para Barra -> Local onde instala os programas
- Selecionei 70G para Home -> local onde instala arquivos pessoais
### Obs: A instalação foi feita no modo Bios Legacy, então só foi criado particionamento para 2 pastas: barra e home
---
### Se tiver usando UEFI, tem que marcar uma partição como BIOS BOOT, precisa ter pelo menos 500 megas para caber o GRUB, no caso, é o boot loader
---
- Para isso selecionar type e bios boot
### OBS: Tabela DOS não precisa disso pois estou usando uma VM

- Selecionar QUIT e sair


# Escolher o sistema de Arquivos
## Ext4
```bash
mkfs.ext4 /dev/sda1 -> make file system -> selecionando o sistema de arquivos no terminal
(fazer pro /dev/sda2 também)
```
## Adicionar os pontos de montagem
```bash
# mnt é a raiz do sistema. O sd1 é o mnt
$ mount /dev/sda1 /mnt 

# criando a pasta home dentro do mnt -> home fica a pasta dos usuários
$ mkdir /mnt/home
# criando a pasta boot dentro do mnt -> fica o boot loader pra guardar informações sobre boot
$ mkdir /mnt/boot 

# Agora precisa adicionar o sd2 no home
# tudo o que tiver na partição home, vai estar no disco sda2
$ mount /dev/sda2 /mnt/home
```
# Baixar os pacotes fundamentais
Precisa passar o diretório /mnt depois do pacstrap, se não vai dar erro
```bash
$ pacstrap /mnt base base-devel linux linux-firmware
```
* Agora precisa gerar o genfstab, é um arquivo de texto comum pra registrar as partições
```bash
$ genfstab -U -p /mnt >> /mnt/etc/fstab
```
# Entrando no sistema
```bash
$ arch-chroot /mnt
```
# Baixando os pacotes do sistema
```bash
$ pacman -S vim grub dosfstools os-prober network-manager-applet networkmanager mtools dialog sudo
```
# Pra instalar o GRUB na BIOS LEGACY (no caso da VM)
```bash
grub-install --target=i386-pc --recheck /dev/sda
```
- OBS: Se a bios for UEFI é de outra forma, procurar na archwiki

## Copiar um arquivo
```bash
cp /usr/share/locale/en@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
```
Se tiver erro ao copiar o arquivo, recomendo sair do usuário mnt e ir até a pasta boot e criar a pasta grub
```bash
# Precisa sair do mnt
$ exit
# Criar a pasta 
$ mkdir /boot/grub
```
## Criar o arquivo de configuração do GRUB
```bash
$ grub-mkconfig -o /boot/grub/grub.cfg
```
# Sincronizar o horário do sistema operacional com o da placa mãe
```bash
$ hwclock --systohc
```
# Arquivos para editar
Precisa editar o locale do sistema com o VIM
```bash
# Você pode encontrar o arquivo com page down no teclado ou utilizando o "j" e "k"
$ vim /etc/locale.gen
# Selecionar com o "i" para descomentar o arquivo pt_BR.UTF-8 UTF-8 (só apagar o #)
# Aperta esc e escreve :wq pra sair salvar e sair
```
Depois inserir no terminal:
```bash
$ locale-gen
```
Próximo arquivo para editar
```bash
$ vim /etc/locale.conf
# Depois Inserir na primeira linha
LANG=pt_BR.UTF-8
```
Outro arquivo para editar
```bash
$ vim /etc/vconsole.conf
# E Inserir no arquivo
KEYMAP=br-abnt2
```

Editar outro arquivo
```bash
$ vim /etc/hostname
# Digitar 
arch
# Esse arquivo vai guardar o nome do computador que vai ficar na rede, no caso, vai ficar como arch
```
Outro arquivo para editar
```bash
vim /etc/hosts
```
Inserir a mesma configuração que a imagem

![edit-hosts](/img/edit-hosts.png)

# Criar uma senha para o usuário root
Guarde essa senha, ela será responsável por fornecer acesso a outros usuários que não são admins, você vai depender dela para fazer downloads também
```bash
$ passwd
```
# Criar um usuário novo 
Usar o root como usuário padrão é perigoso e abre margem para invasão
```bash
$ useradd -m -g users -G wheel yourUser
```

# Editar o arquivo sudoers
O sudoers é o arquivo responsável pelo download de arquivos dos usuários que não são admin
```bash
$ vim /etc/sudoers
```
Inserir na última linha do arquivo o seu usuário, escreva da mesma forma que na imagem, se não estiver com a mesma formatação, pode gerar erros.
![edit-sudoers](/img//edit-sudoers.png)

- OBS: o meu ficou como matheus ALL=(ALL) ALL
- matheus é o meu user

# Próximo passo
Sair do usuário root e dar reboot
```bash
$ exit
```
E digitar
```bash
reboot
```
## Depois Selecionar a opção de rebootar em um sistema operacional existente
Pronto, agora você precisa acessar seu login do root, com a senha que você cadastrou para ele no inicio 

# Próxima etapa, habilitar o NetworkManager
```bash
$ systemctl enable NetworkManager

$ systemctl start NetworkManager

$ systemctl status NetworkManager

# O status precisa estar active Running
```

## Precisa baixar o xorg-server, que é um servidor para exibição de janela
```bash
$ pacman -S xorg-server
```
# Hora de baixar os drivers de vídeo 
por conta de ser na VM, precisa baixar uns pacotes a mais
```bash
$ pacman -S mesa virtualbox-guest-utils virtualbox-host-modules-arch mesa-libgl
```
## Instalar a interface gráfica 
```bash
$ pacman -S gdm
```
- Habilitar o gdm
```bash
systemctl enable gdm
```
## Agora dá reboot na máquina
```bash
$ reboot
```
- Se você tiver quebrado a interface gráfica ou qualquer outra coisa do tipo, precisa acessar o tty

- Pra fazer isso é só digitar, control + alt + f3 que vai te direcionar pro terminal, se quiser voltar é control + alt + f2

## Instalando o tema do GNOME
```bash
$ pacman -Syu gnome gnome-terminal nautilus gnome-tweaks gnome-control-center gnome-backgrounds adwaita-icon-theme
```
## Referências para a Instalação
[Guia Completo de Como Instalar o Arch Linux! - YouTube](https://www.youtube.com/watch?v=MwaQZ4SFleE&t=1933s&ab_channel=MatheusFerro)

[É hoje que você instala o Arch Linux! - Tutorial COMPLETO! - YouTube](https://www.youtube.com/watch?v=4orYC5ARfn8&t=4034s&ab_channel=Diolinux)