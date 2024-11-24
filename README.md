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
sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev pylint3 xterm
