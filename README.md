# Simple NFS Server

## Crie duas máquinas virtuais
Crie 2 máquinas virtuais seguindo meu passo a passo neste repositório: https://github.com/1saacYves/Linux-Server-on-VirtualBox

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


