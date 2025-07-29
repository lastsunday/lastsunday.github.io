# Development Memo

<details>
<summary>Linux development environment for wsl2</summary>

|Title|Value|Remark|
|-|-|-|
|System|ubuntu 24.04|In Windows 10 22H2|
|Editor|<ul><li>zsh</li><li>nvim</li><li>lazyvim</li></ul>||
|Language|<ul><li>rust</li><li>java</li></ul>||
|Framework|<ul><li>esp32</li><li>nodejs</li></ul>||

## zsh install

<https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH>

``` shell
sudo apt install zsh
chsh -s $(which zsh)
```

<https://github.com/ohmyzsh/ohmyzsh>

``` shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

<https://github.com/romkatv/powerlevel10k>
 Install the recommended font. Optional but highly recommended.
 Install Powerlevel10k itself.
 Restart Zsh with exec zsh.
 Type p10k configure if the configuration wizard doesn't start automatically.

## lazyvim install

<https://github.com/neovim/neovim/blob/master/INSTALL.md#pre-built-archives-2>

The Releases page provides pre-built binaries for Linux systems.

``` shell
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.tar.gz
sudo rm -rf /opt/nvim
sudo tar -C /opt -xzf nvim-linux-x86_64.tar.gz
```

Then add this to your shell config (~/.bashrc, ~/.zshrc, ...):

``` shell
export PATH="$PATH:/opt/nvim-linux-x86_64/bin"
```

<https://www.lazyvim.org/>

``` shell
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | \grep -Po '"tag_name": *"v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit -D -t /usr/local/bin/

sudo apt-get install build-essential
sudo apt install cmake
sudo apt install fzf
sudo apt-get install ripgrep
sudo apt install fd-find
```

<https://www.lazyvim.org/installation>
 Install the LazyVim Starter or Clone Your Repo

## git

``` shell
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

## pgp

``` shell
sudo apt-get install gnupg
gpg --import [your gpg file].asc
gpg --list-secret-keys --keyid-format=long
git config --global user.signingkey [GPG 密钥 ID]
git config --global commit.gpgsign true
git config --global tag.gpgSign true
```

> gpg: signing failed: Inappropriate ioctl for device

Add the following line to your shell's profile configuration file (e.g., ~/.bash_profile, ~/.zshrc):

```
export GPG_TTY=$(tty)
```

## wsl proxy by clash verge

配置Clash
 找到General > Allow LAN，打开开关。

配置防火墙
 打开控制面板，找到系统和安全 > Windows Defender 防火墙 > 允许应用通过 Windows 防火墙，勾选上所有Clash相关的应用，包括但不限于Clash for Windows、clash-win64等。

``` shell
hostip=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*')
export https_proxy="http://${hostip}:7897"
export http_proxy="http://${hostip}:7897"
export all_proxy="socks5://${hostip}:7897"
```

## project

### nodejs

<https://github.com/Schniz/fnm>

```shell
curl -fsSL https://fnm.vercel.app/install | bash
```

### rust

<https://www.rust-lang.org/tools/install>

``` shell
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

### jdk

``` shell
sudo apt-get install openjdk-21-jdk
```

### esp32

<https://docs.espressif.com/projects/esp-idf/zh_CN/stable/esp32/get-started/linux-macos-setup.html>

1. setup

``` shell
sudo apt-get install git wget flex bison gperf python3 python3-pip python3-venv cmake ninja-build ccache libffi-dev libssl-dev dfu-util libusb-1.0-0
python3 --version

mkdir -p ~/esp
cd ~/esp
git clone -b v5.5 --recursive https://github.com/espressif/esp-idf.git

cd ~/esp/esp-idf
./install.sh esp32 esp32s3

```

2. run in project

```shell
. $HOME/esp/esp-idf/export.sh
idf.py set-target esp32s3
idf.py menuconfig
idf.py build
idf.py -p PORT flash
```

3. remark wsl2 get PORT
<https://developer.espressif.com/blog/espressif-devkits-with-wsl2/>

in the host terminal

``` shell
winget install usbipd
usbipd list
usbipd bind --busid 3-4
usbipd attach --wsl --busid 3-4
```

in wsl terminal

``` shell
ls /dev/tty*
sudo chmod 777 /dev/ttyUSB0
```

## wsl

### 端口转发

> 如果访问失败，则需要先将端口转发删除，然后再重新添加端口转发

1. 配置防火墙入站规则
打开windows的防火墙高级设置，添加入站端口，比如：3000
1. 以管理员权限打开PowerShell
1. 查看当前的转发设置

``` shell
netsh interface portproxy show all
```

1. 添加新的转发

``` shell
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=3000 connectaddress=localhost connectport=3000
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=8080 connectaddress=localhost connectport=8080
```

1.删除不需要的转发

```shell
netsh interface portproxy delete v4tov4 listenaddress=0.0.0.0 listenport=3000
netsh interface portproxy delete v4tov4 listenaddress=0.0.0.0 listenport=8080
```

</details>
