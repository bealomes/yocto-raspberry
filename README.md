# Custom Linux Distribution with Yocto for Raspberry Pi

Este repositório documenta o processo de criação de distribuições Linux personalizadas utilizando o Yocto Project, com um enfoque especial no Raspberry Pi. O objetivo é fornecer um guia claro para configurar, construir e comparar uma distribuição mínima com as distribuições oficiais fornecidas pela Raspberry Pi Foundation.

---

## **O que é o Yocto Project?**

O Yocto Project é um conjunto de ferramentas de software para criar sistemas Linux personalizados e otimizados para arquiteturas de hardware específicas. Ele permite que desenvolvedores definam cada aspecto de sua distribuição Linux, desde o kernel até pacotes específicos. O Yocto é amplamente utilizado para sistemas embarcados, sendo ideal para criar imagens enxutas e otimizadas para dispositivos como o Raspberry Pi.

### **Elementos principais do Yocto Project**
- **Poky**: 
  Distribuição de referência do Yocto, inclui:
  - **Bitbake**: Ferramenta de construção.
  - **OE-Core**: Core compartilhado.
  - **Meta-Yocto-BSP**: Suporte para hardware de referência.
  - Documentação.

- **Open Embedded Core (OE-Core)**: 
  Core compartilhado entre Yocto e OpenEmbedded. Permite criar distribuições Linux para sistemas embarcados. Embora sejam organizações separadas, compartilham metadados e trabalham em conjunto.

- **Metadata**: 
  Conjunto de arquivos que definem a configuração e especificações do sistema:
  - **Arquivos de Configuração (.conf)**: Definem variáveis globais e informações de hardware.
  - **Receitas (.bb e .bbappend)**: Instruções sobre código-fonte, patches, opções de compilação e licenças.
  - **Classes (.bbclass)**: Abstraem funcionalidades comuns para compartilhar entre várias receitas.
  - **Includes (.inc)**: Arquivos incluídos para modularização e reutilização de código.

- **Bitbake**: 
  Agendador de tarefas que processa código misto de Python e shell script. Lê receitas, busca pacotes, compila e integra os resultados em uma imagem inicializável.

- **Meta-Yocto-BSP**: 
  Conjunto de arquivos para suportar dispositivos de hardware específicos. Define informações sobre recursos de hardware presentes em dispositivos como BeagleBone, Intel x86, Raspberry Pi, entre outros.

- **Receitas (.bb Files)**: 
  Instruções que descrevem código-fonte, patches a serem aplicados, opções de configuração e compilação, além de licenças. Lidas e processadas pelo Bitbake.

- **Arquivos de Configuração (.conf)**: 
  Definem variáveis globais e específicas do hardware. Exemplos incluem `local.conf` e `bblayers.conf`.

- **Classes (.bbclass)**: 
  Arquivos que abstraem funcionalidades comuns para compartilhar entre múltiplas receitas.

- **Layers**: 
  Coleções de receitas relacionadas organizadas em pastas (`meta-[layername]`, como `meta`, `meta-poky`, `meta-yocto-bsp`). As camadas são listadas em `bblayers.conf`. Outras camadas podem ser encontradas em [OpenEmbedded Layer Index](https://layers.openembedded.org/layerindex/branch/master/layers/).

- **Image**: 
  Define como o sistema de arquivos raiz é construído, incluindo pacotes a serem instalados.

- **Package**: 
  Arquivo binário resultante de uma receita, no formato `.rpm`, `.deb`, ou `.ipkg`. Uma única receita pode produzir vários pacotes.

---

## **Pré-requisitos**

Distribuições Linux suportadas:
- Ubuntu 20.04 (LTS)
- Ubuntu 22.04 (LTS)
- Fedora 38
- CentOS Stream 8
- Debian GNU/Linux 11 (Bullseye)
- Debian GNU/Linux 12 (Bookworm)
- OpenSUSE Leap 15.4
- AlmaLinux 8
- AlmaLinux 9
- Rocky 9

Antes de começar, certifique-se de que sua máquina possui os seguintes pacotes instalados (para Ubuntu):

```bash
sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 python3-subunit zstd liblz4-tool file locales libacl1

```

## **Estrutura base do projeto** ##
O projeto contará com a seguinte estrutura:

```bash
Workdir
├── build
│   ├── cache
│   ├── conf
│   │   ├── bblayers.conf
│   │   ├── local.conf
│   │   └── templateconf.cfg
│   ├── downloads
│   ├── sstate-cache
│   └── tmp
│       └── deploy
│           └── images
│               └── raspberrypi3
└── source
    ├── meta-mylayer
    ├── meta-openembedded
    ├── meta-raspberrypi
    └── poky
```

#### **1. `source`**
Contém as camadas (layers) e metadados do Yocto.

#### **2. `build`**
Diretório onde os arquivos de build se encontram:
- **`conf`**: Configurações do projeto:
  - **`bblayers.conf`**: Lista de camadas.
  - **`local.conf`**: Configurações locais (ex. hardware, threads).
- **`tmp/deploy/images/raspberrypi3`**: Contém a imagem final gerada para Raspberry Pi 3. 

Esse é o local onde você encontrará os arquivos `.wic` ou similares para flash no dispositivo.

## **Configuração do Ambiente** ##
Clone os repositórios necessários dentro da pasta _source_.

1. Clone os repositórios necessários na versão desejada (ex: dunfell):
   ```bash
   git clone -b dunfell git://git.yoctoproject.org/poky
   git clone -b dunfell git://git.yoctoproject.org/meta-raspberrypi
   git clone -b dunfell git://git.openembedded.org/meta-openembedded
   ```
2. Inicialize o ambiente de build:
   ```bash
   source source/poky/oe-init-build-env build
   ```

---

## Configuração de Camadas

1. Adicione as camadas necessárias. Na pasta build, execute:
   ```bash
   bitbake-layers add-layer ../source/meta-raspberrypi
   bitbake-layers add-layer ../source/meta-openembedded/meta-oe
   bitbake-layers add-layer ../source/meta-openembedded/meta-python
   bitbake-layers add-layer ../source/meta-openembedded/meta-multimedia
   bitbake-layers add-layer ../source/meta-openembedded/meta-networking
   ```

3. Configure a máquina no arquivo build/conf/local.conf:
   - Defina o modelo do Raspberry Pi:
     ```plaintext
     MACHINE = "raspberrypi3"
     ```
   - Outros ajustes importantes:
     ```plaintext
     INHERIT += "rm_work"
     ENABLE_UART = "1"
     IMAGE_ROOTFS_EXTRA_SPACE = "4194304"
     LICENSE_FLAGS_ACCEPTED += "synaptics-killswitch"
     ```

---

## Construção da Imagem

Escolha a imagem base:
   - Para uma imagem mínima:
     ```bash
     bitbake core-image-minimal
     ```
   - Para uma imagem com interface gráfica (exemplo: Sato):
     ```bash
     bitbake core-image-sato
     ```

## Grave a imagem em um cartão SD:
   - Certifique-se de identificar corretamente o dispositivo correspondente ao cartão SD, por exemplo, `/dev/sda`. Utilize o comando:
     ```bash
      lsblk
     ```
   - Execute os seguintes comandos para preparar e gravar a imagem no cartão:
     ```bash
     sudo umount /dev/sda
     bunzip2 /build/tmp/deploy/images/raspberrypi3-64/core-image-sato-raspberrypi3-64.rootfs-20241030194845.wic.bz2
     sudo dd if=/build/tmp/deploy/images/raspberrypi3-64/core-image-sato-raspberrypi3-64.rootfs-20241030194845.wic of=/dev/sda bs=4M status=progress
     sync
     ```

     Aqui é importante entrar no diretório e ver o nome da imagem que foi gerada. O arquivo "core-image-sato-raspberrypi3-64.rootfs-20241030194845.wic" varia os números a cada build.


---

## Testando a Imagem

- Insira o cartão SD no Raspberry Pi.
- Conecte os cabos necessários (alimentação, HDMI e teclado).
- Inicie o dispositivo.

---

## Comparativo com a Distribuição Oficial


| Aspecto                    | Distribuição Oficial       | Distribuição Yocto         |
|----------------------------|----------------------------|----------------------------|
| Tempo de Build             | 15 min             | de 4 a 7 horas            |
| Tamanho da Imagem          | 2 Gb             | 180 Mb (sem interface) e 1 Gb (com interface)             |
| Customização               | Limitada                  | Totalmente Personalizável |

As duas maiores desvantagens de trabalhar com yocto são o tempo de build de uma imagem e a memória necessária em disko. Um projeto de yocto pode chegar a 40Gb.

---

## Links Úteis

- [Yocto Project Documentation](https://www.yoctoproject.org/docs/latest/).
- [Yocto Project: Introdução](https://embarcados.com.br/yocto-project-introducao/)
- [Kickstart Embedded](https://kickstartembedded.com/2022/02/28/yocto-part-9-customising-images-by-adding-your-recipes/)
- [Compile uma Distro de Linux Embarcado para a Raspberry Pi 3 usando Yocto Project](https://embarcados.com.br/linux-para-a-raspberry-pi-3-usando-yocto/)
- [Raspberry Pi + Yocto (dora)](https://embarcados.com.br/raspberrypi-yocto/) 

## Alunos: 
Beatriz Lomes da Silva - 12548038

Pedro Oliveira Torrente - 11798853
