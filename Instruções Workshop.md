# Primeira demo - Full Disk Encryption em VMs locais via QEMU + KVM

## Ambiente de testes

- SO do Host: Fedora 42 Workstation (Linux 6.18)
- SO da VM de Teste: Ubuntu

## Configurando QEMU + VirtManager

Instale os pacotes necessários com o comando abaixo:

```bash
sudo dnf install @virtualization # instala VirtManager e QEMU
```

Depois disso, habilite o serviço de virtualização `libvirtd`:

```bash
sudo systemctl enable --now libvirtd
```

Opcionalmente, adicione o usuário atual ao grupo `libvirt` para evitar ter que digitar `sudo` toda vez.

```bash
sudo usermod -aG libvirt $(whoami)
```

## Acessando o arquivo da imagem de disco

Depois de configurar a VM local como desejado,

Usamos `guestmount` para montar o arquivo de disco da VM no sistema de arquivos do host, para que possamos acessar os arquivos criados durante os testes.

```bash
guestmount -a <path/of/file.qcow2> -m <guest_partition> --ro <target_dir>
```

Depois de verificar que os arquivos de teste estão presentes, desmontamos o arquivo de imagem usando o seguinte comand:

```bash
guestunmount <target_dir>
```

## Encriptando a imagem com QEMU

Use o seguinte comando para criar uma versão encriptada da imagem:

```bash
qemu-img convert -f qcow2 -O qcow2 --object secret,id=sec0,data=<password> \
    -o encrypt.format=luks,encrypt.key-secret=sec0 \
    <path/to/source-image.qcow2> <path/to/output-image.qcow2>
```

Assim que a execução do comando anterior acabar, é possível verificar o estado do novo arquivo de imagem com:

```bash
qemu-img info <path/to/output-image.qcow2>
```

O comando acima deve produzir como saída algo do tipo:

```bash
# Exemplo de saída
# ...
file format: qcow2
virtual size: 25 GiB (26843545600 bytes)
disk size: 8.64 GiB
encrypted: yes # imagem está encriptada
cluster_size: 65536
Format specific information:
    compat: 1.1
    compression type: zlib
    lazy refcounts: false
    refcount bits: 16
    encrypt:
        ivgen alg: plain64
        detached header: false
        hash alg: sha256
        cipher alg: aes-256
        uuid: 7a80f591-517c-45c6-b25a-5c7962a74555
        format: luks # encriptada usando LUKS
        cipher mode: xts # encriptada com AES-XTS
        slots:
        # ...
    file length: 8.64 GiB (9278717952 bytes)
    disk size: 8.64 GiB
```

## Checando encriptação da imagem

Você pode manualmente verificar que a imagem do disco está de fato encriptada checando o conteúdo bruto dos arquivos QCOW2.

Execute o seguinte comando para a imagem encriptada e a original:

```bash
head -n 50 <path/to/image.qcow2>
```

# Segunda demo - Full Disk Encryption em VMs em um provedor de nuvem

## Ambiente de testes

- Provedor de nuvem: AWS
- So do host: EC2 Instance (Default configurations)
- So da VM de Testes: Ubuntu Server

## Configurando a instância EC2 e a instância de disco

- Inicie a instância EC2: navegue até o Amazon EC2 Dashboard e crie uma nova instância. Selecione Ubuntu como a Amazon Machine Image (AMI) em vez do Amazon Linux, que vem como padrão. Tipos de instância padrão (como `t2.micro`) são suficientes para essa demo.
- Crie e anexe um volume: navegue até Elastic Block Store (EBS) > Volumes. Crie um novo volume EBS na mesma Availability Zone que a sua instância. Selecione o volume, escolha Attach volume e anexe-o à sua instância do EC2 Ubuntu.

## Encriptando e montando o disco

Assim que sua instância estiver em execução com o volume anexado, conecte-se a ela via SSH.

### Identifique o novo disco anexado

Execute o seguinte comando para listar todos os dispositivos de bloco disponíveis. Procure o disco não formatado que você acabou de anexar (geralmente nomeado `/dev/sdb`, `/dev/xvdf` ou `/dev/nvme1n1`, dependendo do tipo de instância. Para essa demo, assume-se que o disco é `/dev/sdb`).

```bash
lsblk
```

### Instale a ferramenta de encriptação

Instale `cryptsetup`, que provê as utilidades necessárias para configurar encripração via LUKS:

```bash
sudo apt update && sudo apt install cryptsetup -y
```

### Inicialize a partição LUKS

Esse passo irá apagar qualquer dado existente no disco. Antes de abrir e usar o disco, você deve formatá-lo como uma partição encriptada LUKS. Será solicitado uma confirmação (digite `YES`) e uma passphrase segura. Rode o seguinte comando, assumindo que o disco é `/dev/sdb`:

```bash
sudo cryptsetup luksFormat /dev/sdb
```

### Abra o disco encriptado

Agora, mapeia o disco físico encriptado para um mapeador de dispositivo virtual. Troque `<nome_do_disco_encriptado>` por um nome de sua escolha. Você deve digitar a passphrase criada no passo anterior. Isso irá criar um mapeamento decriptado localizado em `/dev/mapper/<nome_do_disco_encriptado>`

```bash
sudo cryptsetup open /dev/sdb <nome_do_disco_encriptado>
```

### Formate o volume mapeado

Crie um sistema de arquivos no novo mapeamento decriptado. Vamos utilizar o sistema de arquivos ext4 para essa demo.

```bash
sudo mkfs.ext4 /dev/mapper/<nome_do_disco_encriptado>
```

### Monte o disco encriptado

Finalmente, crie um diretório para servir como ponto de montagem e monte o mapeamento decriptado para ele. Execute:

```bash
sudo mkdir -p /mnt/<ponto_de_montagem>
sudo mount /dev/mapper/<nome_do_disco_encriptado> /mnt/<ponto_de_montagem> 
```

Verifique que o disco está montado com sucesso e disponível executando:

```bash
df -h
```
