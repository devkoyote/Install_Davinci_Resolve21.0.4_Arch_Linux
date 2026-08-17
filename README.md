# Install DaVinci Resolve 21.0.4 on Arch Linux / Instalação do DaVinci Resolve 21.0.4 no Arch Linux

[![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white)](https://archlinux.org)
[![DaVinci Resolve](https://img.shields.io/badge/DaVinci_Resolve_21.0.4-000000?style=for-the-badge&logo=daVinci-resolve&logoColor=white)](https://www.blackmagicdesign.com/products/davinciresolve)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)

> Complete guide to installing, configuring, and troubleshooting **DaVinci Resolve Studio 21.0.4** on **Arch Linux**.
>
> Guia completo para instalação, configuração e resolução de problemas do **DaVinci Resolve Studio 21.0.4** no **Arch Linux**.

---

## 🌐 Language / Idioma
* 🇺🇸 [English Version](#english)
* 🇧🇷 [Versão em Português](#portugues)

---

<a name="english"></a>
# 🇺🇸 English Version

This guide provides step-by-step instructions for installing DaVinci Resolve Studio 21.0.4 on Arch Linux, fixing dependency issues, setting up GPU drivers, fixing desktop shortcuts, and resolving common audio/Fairlight issues.

## 📋 Table of Contents
1. [Initial Setup](#1-initial-setup)
2. [Dependencies & GPU Drivers](#2-dependencies--gpu-drivers)
3. [Extraction & Installation](#3-extraction--installation)
4. [Desktop Launcher Fix](#4-desktop-launcher-fix)
5. [NVIDIA Launch Fix](#5-nvidia-launch-fix)
6. [Fairlight & Audio Fix](#6-fairlight--audio-fix)

---

### 1. Initial Setup

Download **DaVinci_Resolve_Studio_21.0.4_Linux.zip** (or the Free version installer) from the official [Blackmagic Design Website](https://www.blackmagicdesign.com/products/davinciresolve).

---

### 2. Dependencies & GPU Drivers

#### Base Dependencies
First, ensure you have the required base dependencies installed:

```bash
sudo pacman -S --needed libxcrypt-compat curl glu base-devel git
```

#### Required System Dependencies for DaVinci Resolve 21.0.4
Install all required runtime libraries and utilities:

```bash
sudo pacman -S --needed \
    fuse2 \
    alsa-lib \
    apr \
    apr-util \
    dbus \
    fontconfig \
    freetype2 \
    libglvnd \
    libice \
    librsvg \
    libsm \
    libx11 \
    libxcursor \
    libxext \
    libxfixes \
    libxi \
    libxinerama \
    libxkbcommon \
    libxkbcommon-x11 \
    libxrandr \
    libxrender \
    libxtst \
    libxxf86vm \
    mesa \
    glu \
    mtdev \
    libpulse \
    libxcb \
    xcb-util \
    xcb-util-image \
    xcb-util-keysyms \
    xcb-util-renderutil \
    xcb-util-wm \
    opencl-mesa \
    rocm-opencl-runtime \
    libxcrypt-compat \
    alsa-plugins
```

> **Note:** Older guides often required manually removing or linking bundled libraries (such as `libglib`, `libgio`, or `libz` in `/opt/resolve/libs/`). With modern updates to the AUR `davinci-resolve-studio` package, manual library fixes are no longer required unless you encounter conflict issues.

#### GPU Compute Setup (Required)
Select and install the driver package appropriate for your graphics card:

* **NVIDIA:**
  ```bash
  sudo pacman -S --needed nvidia nvidia-utils opencl-nvidia cuda
  ```
  *(Use `nvidia-open` if `nvidia` is not found or if you are using open-source kernel modules).*

* **AMD:**
  ```bash
  sudo pacman -S --needed rocm-opencl-runtime
  ```

* **Intel:**
  ```bash
  sudo pacman -S --needed intel-compute-runtime
  ```

#### Verify GPU Setup
Check if OpenCL and GPU compute are working properly:
```bash
clinfo | less
```

---

### 3. Extraction & Installation

Navigate to the directory where you downloaded and extracted the installer. 

> ⚠️ **Important:** You must use the `SKIP_PACKAGE_CHECK=1` environment variable when running the installer, otherwise it will fail due to missing package checks on non-officially supported distros.

Run the following commands (adjust the installer filename if using a different version):

```bash
chmod +x DaVinci_Resolve_Studio_21.0.4_Linux.run
SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_Studio_21.0.4_Linux.run
```

#### Disable Conflicting Bundled Libraries
DaVinci Resolve packages older versions of certain system libraries that cause conflicts with Arch Linux's modern packages. Move them to a disabled directory:

```bash
cd /opt/resolve/libs
sudo mkdir -p disabled-libraries
sudo mv libglib* libgio* libgmodule* disabled-libraries/
```

---

### 4. Desktop Launcher Fix

Create or edit the local desktop entry so DaVinci Resolve uses X11/XCB graphics platforms correctly under Wayland or modern desktop environments:

```bash
nano ~/.local/share/applications/com.blackmagicdesign.resolve.desktop
```

Paste the following configuration and save:

```ini
[Desktop Entry]
Version=1.0
Type=Application
Name=DaVinci Resolve
GenericName=DaVinci Resolve
Comment=Revolutionary new tools for editing, visual effects, color correction and professional audio post production
Path=/opt/resolve/
Exec=env QT_QPA_PLATFORM=xcb GDK_BACKEND=x11 /opt/resolve/bin/resolve %u
Terminal=false
MimeType=application/x-resolveproj;
Icon=/opt/resolve/graphics/DV_Resolve.png
StartupNotify=true
Name[en_US]=DaVinci Resolve
```

---

### 5. NVIDIA Launch Fix

If DaVinci Resolve fails to open or gets stuck at the **Waveform Monitor** initialization stage on NVIDIA systems, launch it with the GLX vendor environment variable:

```bash
__GLX_VENDOR_LIBRARY_NAME=nvidia /opt/resolve/bin/resolve
```

You can also update the `Exec` line in your `.desktop` file to include this variable:
```ini
Exec=env QT_QPA_PLATFORM=xcb GDK_BACKEND=x11 __GLX_VENDOR_LIBRARY_NAME=nvidia /opt/resolve/bin/resolve %u
```

---

### 6. Fairlight & Audio Fix

If you experience audio issues or Fairlight fails to process sound on Linux, refer to the dedicated audio patch guide:
👉 [devkoyote/davinci_resolve_audio_linux](https://github.com/devkoyote/davinci_resolve_audio_linux)

---
---

<a name="portugues"></a>
# 🇧🇷 Versão em Português

Este guia oferece instruções passo a passo para instalar o DaVinci Resolve Studio 21.0.4 no Arch Linux, corrigindo dependências, configurando drivers de GPU, ajustando atalhos de área de trabalho e resolvendo problemas comuns de áudio/Fairlight.

## 📋 Sumário
1. [Configuração Inicial](#1-configuração-inicial)
2. [Dependências e Drivers de GPU](#2-dependências-e-drivers-de-gpu)
3. [Extração e Instalação](#3-extração-e-instalação)
4. [Correção do Atalho (.desktop)](#4-correção-do-atalho-desktop)
5. [Correção de Inicialização em GPUs NVIDIA](#5-correção-de-inicialização-em-gpus-nvidia)
6. [Correção de Áudio e Fairlight](#6-correção-de-áudio-e-fairlight)

---

### 1. Configuração Inicial

Faça o download do arquivo **DaVinci_Resolve_Studio_21.0.4_Linux.zip** (ou a versão gratuita) através do site oficial da [Blackmagic Design](https://www.blackmagicdesign.com/products/davinciresolve).

---

### 2. Dependências e Drivers de GPU

#### Dependências Base
Ganta que as dependências base do sistema estejam instaladas:

```bash
sudo pacman -S --needed libxcrypt-compat curl glu base-devel git
```

#### Dependências Necessárias para o DaVinci 21.0.4
Instale todas as bibliotecas e utilitários de execução exigidos:

```bash
sudo pacman -S --needed \
    fuse2 \
    alsa-lib \
    apr \
    apr-util \
    dbus \
    fontconfig \
    freetype2 \
    libglvnd \
    libice \
    librsvg \
    libsm \
    libx11 \
    libxcursor \
    libxext \
    libxfixes \
    libxi \
    libxinerama \
    libxkbcommon \
    libxkbcommon-x11 \
    libxrandr \
    libxrender \
    libxtst \
    libxxf86vm \
    mesa \
    glu \
    mtdev \
    libpulse \
    libxcb \
    xcb-util \
    xcb-util-image \
    xcb-util-keysyms \
    xcb-util-renderutil \
    xcb-util-wm \
    opencl-mesa \
    rocm-opencl-runtime \
    libxcrypt-compat \
    alsa-plugins
```

> **Nota:** Tutoriais mais antigos exigiam a remoção manual ou criação de links para bibliotecas nativas (como `libglib`, `libgio` ou `libz` em `/opt/resolve/libs/`). Com as atualizações recentes do pacote AUR `davinci-resolve-studio`, correções manuais de biblioteca não são mais estritamente necessárias, a menos que ocorram conflitos.

#### Configuração de Aceleração por GPU (Obrigatório)
Instale o driver OpenCL / CUDA adequado para a sua placa de vídeo:

* **NVIDIA:**
  ```bash
  sudo pacman -S --needed nvidia nvidia-utils opencl-nvidia cuda
  ```
  *(Utilize `nvidia-open` caso `nvidia` não seja encontrado no repositório).*

* **AMD:**
  ```bash
  sudo pacman -S --needed rocm-opencl-runtime
  ```

* **Intel:**
  ```bash
  sudo pacman -S --needed intel-compute-runtime
  ```

#### Verificar Configuração de GPU
Valide se o suporte a OpenCL está funcionando corretamente:
```bash
clinfo | less
```

---

### 3. Extração e Instalação

Navegue até a pasta onde o instalador foi baixado e descompactado.

> ⚠️ **Importante:** É necessário utilizar a variável `SKIP_PACKAGE_CHECK=1` ao executar o instalador, caso contrário a instalação falhará relatando ausência de pacotes (pois o instalador busca nomes de pacotes específicos do CentOS/RHEL).

Execute os comandos abaixo (ajuste o nome do arquivo se estiver usando outra versão):

```bash
chmod +x DaVinci_Resolve_Studio_21.0.4_Linux.run
SKIP_PACKAGE_CHECK=1 ./DaVinci_Resolve_Studio_21.0.4_Linux.run
```

#### Desativar Bibliotecas Incompatíveis
O DaVinci Resolve traz empacotadas versões antigas de certas bibliotecas que entram em conflito com os pacotes mais novos do Arch Linux. Mova-as para uma pasta desativada:

```bash
cd /opt/resolve/libs
sudo mkdir -p disabled-libraries
sudo mv libglib* libgio* libgmodule* disabled-libraries/
```

---

### 4. Correção do Atalho (.desktop)

Crie ou edite o atalho de inicialização local para garantir que o Resolve utilize as plataformas de exibição X11/XCB adequadas:

```bash
nano ~/.local/share/applications/com.blackmagicdesign.resolve.desktop
```

Cole o conteúdo abaixo e salve o arquivo:

```ini
[Desktop Entry]
Version=1.0
Type=Application
Name=DaVinci Resolve
GenericName=DaVinci Resolve
Comment=Revolutionary new tools for editing, visual effects, color correction and professional audio post production
Path=/opt/resolve/
Exec=env QT_QPA_PLATFORM=xcb GDK_BACKEND=x11 /opt/resolve/bin/resolve %u
Terminal=false
MimeType=application/x-resolveproj;
Icon=/opt/resolve/graphics/DV_Resolve.png
StartupNotify=true
Name[en_US]=DaVinci Resolve
```

---

### 5. Correção de Inicialização em GPUs NVIDIA

Se o DaVinci Resolve não abrir ou ficar travado na tela de carregamento do **Waveform Monitor** em placas NVIDIA, inicie-o definindo a variável de ambiente GLX:

```bash
__GLX_VENDOR_LIBRARY_NAME=nvidia /opt/resolve/bin/resolve
```

Você também pode atualizar a linha `Exec` no seu arquivo `.desktop`:
```ini
Exec=env QT_QPA_PLATFORM=xcb GDK_BACKEND=x11 __GLX_VENDOR_LIBRARY_NAME=nvidia /opt/resolve/bin/resolve %u
```

---

### 6. Correção de Áudio e Fairlight

Se você tiver problemas de reprodução de áudio ou no painel Fairlight, siga o tutorial detalhado de correção de áudio:
👉 [devkoyote/davinci_resolve_audio_linux](https://github.com/devkoyote/davinci_resolve_audio_linux)

---
