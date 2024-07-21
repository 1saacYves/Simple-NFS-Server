# Simple NFS Server

## Crie duas máquinas virtuais
Crie 2 máquinas virtuais seguindo meu passo a passo neste repositório: [https://github.com/1saacYves/Linux-Server-on-VirtualBox](https://github.com/1saacYves/Linux-on-VIrtualBox)

## Configurar IP Fixo
**Depois de iniciar a máquina faça login com seu usuário e senha**

- Verifique o IP que a máquina está utilizando com o comando:

```
ip address
```

- Depois teste a rede usando o comando:

```
ping -c5 8.8.8.8
```

### Precisamos editar o arquivo de configuração da interface de rede
- Primeiro vamos buscar o nome desse arquivo:
Use o comando:

```
ls /etc/sysconfig/network-scripts/
```

- Agora que sabemos o nome do arquivo vamos edita-lo:
Use o comando, para entrar no modo de edição do vim (Pode ser pedido a sua senha de usuário):

```
sudo vi /etc/sysconfig/network-scripts/ifcfg-NOME_DA_SUA_INTERFACE
```

- Quando estiver dentro do arquivo aperte **I** para entrar no modo **INSERT**

- Edite o seu arquivo com as novas informações:

```
BOOTPROTO=static
IPADDR=SEU_IP
NETMASK=SUA_MASCARA
GATEWAY=SEU_GATEWAY
```

- Depois de editar, Aperte Esc (para entrar no modo comando), e use o comando para sair e salvar:

```
:wq
```

- Reinicie o serviço de rede para aplicar as alterações (Pode ser pedido a senha):

```
sudo systemctl restart NetworkManager
```

- Verifique se as novas informações foram atualizadas:
Use o comando:

```
ip address
```

- Voce pode verificar que o endereço IP foi atualizado em Inet

## Servidor NFS
### Na VM 1 (Servidor NFS):

1. Instale o servidor NFS se ele ainda não estiver instalado:

```
sudo dnf install -y nfs-utils
```

2. Depois de instalado crie uma pasta compartilhada:

```
sudo mkdir /nfs-share
```

- Crie alguns arquivos teste

```
sudo fallocate -l 10MB /nfs-share/file1
echo "This is a shared text file." | sudo tee /nfs-share/shared-text.txt > /dev/null
```

- Verifique se os arquivos foram criados com sucesso.

```
ls -lh /nfs-share
```

3. É importante configurar as permissões da pasta, exemplo:

```
sudo chmod -R 777 /nfs-share
```

### Editando o arquivo /etc/exports

1. Para editar o arquivo:

```
echo "/nfs-share  <CLIENTE_IP>(rw)" | sudo tee -a /etc/exports > /dev/null
```

*Substitua <CLIENTE_IP> pelo endereço IP da sua segunda VM.*

### Reinicie o Servidor

1. Depois de aplicar alterações é necessário que reinicie o seu servidor:

```
sudo systemctl restart nfs-server
```

### Iniciar o Servidor NFS

1. Defina o firewall para permitir tráfego NFS.

```
sudo firewall-cmd --permanent --zone=public --add-service=nfs
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

2. Para ativar e iniciar o serviço NFS.

```
sudo systemctl enable --now nfs-server
showmount -e
```

## Cliente

### Na VM 2 (Cliente NFS):

1. Primeiro instale o cliente NFS:

```
sudo dnf install -y nfs-utils
```

2. Crie um diretório para montar a pasta compartilhada do servidor.

*Montar uma pasta significa torná-la acessível e disponível para leitura e gravação.*

```
sudo mount <SERVER_IP>:/nfs-share /nfs-mount
```

- *Substitua <Server_IP> pelo o endereço IP da sua primeira VM*
- Obtenha uma lista de diretorios:

```
ls -lh /nfs-mount
```

3. Teste o acesso ao compartilhamento NFS:

```
echo "Hello World!" >> /nfs-mount/shared-text.txt
cat /nfs-mount/shared-text.txt
```

## Resultado VM 2:

![nfs5](https://github.com/1saacYves/Simple-NFS-Server/assets/170655155/e5da84f1-6cd0-4bb1-b0ef-df108b521833)

## Resultado VM 1:

![nfs6](https://github.com/1saacYves/Simple-NFS-Server/assets/170655155/2fbea901-3507-4f3d-ac7f-d235b11d6225)
