# Custom Linux Distribution with Yocto for Raspberry Pi

Este repositório documenta o processo de criação de distribuições Linux personalizadas utilizando o Yocto Project, com um enfoque especial no Raspberry Pi. O objetivo é fornecer um guia claro para configurar, construir e comparar uma distribuição mínima com as distribuições oficiais fornecidas pela Raspberry Pi Foundation.

---

## **O que é o Yocto Project?**

O Yocto Project é um conjunto de ferramentas de software para criar sistemas Linux personalizados e otimizados para arquiteturas de hardware específicas. Ele permite que desenvolvedores definam cada aspecto de sua distribuição Linux, desde o kernel até pacotes específicos. O Yocto é amplamente utilizado para sistemas embarcados, sendo ideal para criar imagens enxutas e otimizadas para dispositivos como o Raspberry Pi.

### **Elementos principais do Yocto Project**
- **Poky**: Referência padrão que inclui BitBake, OpenEmbedded-Core, e ferramentas adicionais.
- **BitBake**: Ferramenta para automatizar o processo de construção baseado em receitas.
- **Receitas (.bb)**: Instruções para compilar, configurar e instalar pacotes.
- **Camadas (Layers)**: Coleções de receitas organizadas por propósito.

---

## **Pré-requisitos**

Antes de começar, certifique-se de que sua máquina possui os seguintes pacotes instalados (para Ubuntu):

```bash
sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 python3-subunit zstd liblz4-tool file locales libacl1

```

## **Configuração do Ambiente** ##
Clone os repositórios necessários dentro da pasta _source_.

1. *Clone os repositórios necessários na versão desejada (ex: kirkstone):*
   ```bash
   git clone -b kirkstone git://git.yoctoproject.org/poky
   git clone -b kirkstone git://git.yoctoproject.org/meta-raspberrypi
   git clone -b kirkstone git://git.openembedded.org/meta-openembedded
   ```
2. *Inicialize o ambiente de build:*
   ```bash
   source source/poky/oe-init-build-env build
   ```

---

## Configuração de Camadas

1. *Adicione as camadas necessárias:*
   ```bash
   bitbake-layers add-layer ../meta-raspberrypi
   bitbake-layers add-layer ../meta-openembedded/meta-oe
   bitbake-layers add-layer ../meta-openembedded/meta-python
   bitbake-layers add-layer ../meta-openembedded/meta-multimedia
   bitbake-layers add-layer ../meta-openembedded/meta-networking
   ```

3. *Configure a máquina no arquivo build/conf/local.conf:*
   - Defina o modelo do Raspberry Pi:
     plaintext
     MACHINE = "raspberrypi3"
     
   - Outros ajustes importantes:
     plaintext
     INHERIT += "rm_work"
     ENABLE_UART = "1"
     IMAGE_ROOTFS_EXTRA_SPACE = "4194304"
     LICENSE_FLAGS_ACCEPTED += "synaptics-killswitch"
     

---

## Construção da Imagem

1. *Escolha a imagem base:*
   - Para uma imagem mínima:
     ```bash
     bitbake core-image-minimal
     ```
   - Para uma imagem com interface gráfica (exemplo: Sato):
     ```bash
     bitbake core-image-sato
     ```

2. **Grave a imagem em um cartão SD:**
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


---

## Testando a Imagem

- Insira o cartão SD no Raspberry Pi.
- Conecte os cabos necessários (alimentação, HDMI e teclado).
- Inicie o dispositivo.

---

## Comparativo com a Distribuição Oficial


| Aspecto                    | Distribuição Oficial       | Distribuição Yocto         |
|----------------------------|----------------------------|----------------------------|
| Tempo de Build             | (Preencher)             | (Preencher)             |
| Tamanho da Imagem          | (Preencher)             | (Preencher)             |
| Customização               | Limitada                  | Totalmente Personalizável |

---

## Informações Adicionais

- *Diretório de saída:* Todos os artefatos gerados estão localizados em build/tmp/deploy/images.
- *Documentação Oficial:* [Yocto Project Documentation](https://www.yoctoproject.org/docs/latest/).
