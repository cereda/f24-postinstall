# Fedora post installation guide

Welcome to my personal Fedora post installation guide. In this particular document, I try to describe most of steps I do after a clean install of this awesome Linux distribution. Ideas and suggestions were collected from different sources and also from personal experience. Use this guide at your own risk and have fun with Fedora! :wink:

## Upgrade the entire system

First of all, let us update the entire system after a clean Fedora install. This procedure is highly recommended (if not a mandatory step), as a lot of potential bugs that could not be fixed for the initial distribution release might already have a patch by now. Open the terminal and type:

```bash
$ sudo dnf upgrade --refresh -y
```

This procedure might take a significant amount of time, so please be patient. I highly recommend going for a walk during the system upgrade. A hot cup of chocolate might also be recommended. :wink:

After a proper update, let us also enable the Fedora workstation repositories. These are third party sources and are not included by default in a typical installation:

```bash
$ sudo dnf install fedora-workstation-repositories -y
```

And there we go.

## Enable RPM Fusion

Fedora already has great applications available out of the box. As to enhance the experience, let us add the repositories for contributed packages ([RPM Fusion](http://rpmfusion.org/)). We can configure it in one command:

```bash
sudo dnf install \
https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm -y
```

For reference:

1. Free:

```bash
sudo dnf install \
https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm -y
```

2. Non-free:

```bash
sudo dnf install \
https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm -y
```

It should not take much time. Just for reference, `rpm -E %fedora` returns the installed release number, so we can ensure to install the correct repositories. :wink:

## Terminal with colours

I enjoy having colours in my terminal, although it is entirely optional. If you want to have a nice coloured terminal, create a file named `colours.sh` with the following content:

```bash
if [[ ! -z \$BASH ]]; then
 if [[ \$USER = "root" ]]; then
  PS1="\[\033[33m\][\[\033[m\]\[\033[31m\]\u@\h\[\033[m\] \[\033[33m\]\W\[\033[m\]\[\033[33m\]]\[\033[m\] # "
 elif [[ $(whoami) = "root" ]]; then
  PS1="\[\033[33m\][\[\033[m\]\[\033[31m\]\u@\h\[\033[m\] \[\033[33m\]\W\[\033[m\]\[\033[33m\]]\[\033[m\] # "
 else
  PS1="\[\033[36m\][\[\033[m\]\[\033[34m\]\u@\h\[\033[m\] \[\033[32m\]\W\[\033[m\]\[\033[36m\]]\[\033[m\] \$ "
 fi
fi
```

And move it to `/etc/profile.d/`:

```bash
$ sudo mv colours.sh /etc/profile.d/
```

There is no need to restart your entire session. Simply restart your terminal (or open a new tab) and have fun! Alternatively, you can copy `colours.sh.sample` from this repository and rename it accordingly.

## Improve font rendering

Fedora has a great font support. I used to rely on an specific release of Freetype available in the contributed repositories, but this procedure became obsolete since Fedora 30. It is quite certain that you already have the default `freetype` package installed in your machine, so let us tweak our font configuration. In order to ease our setup, I highly suggest installing the Gnome Tweaks tool:

```bash
$ sudo dnf install gnome-tweaks
```

Now, open this tool we have just installed and set _subpixel antialising_ in the _Fonts_ section. Alternatively, we can use the terminal to adjust such things:

```bash
$ gsettings set org.gnome.settings-daemon.plugins.xsettings antialiasing "rgba"
$ gsettings set org.gnome.settings-daemon.plugins.xsettings hinting "slight"
```

You can tweak a lot of things, so your mileage can greatly vary. I usually restart my session, so changes can be applied accordingly.

## Useful packages

These are some packages I like to install in most of my Fedora machines. Use them at your own risk!

1. Editors in general. Note that `vim` and `neovim` are the typical command line editors (I omited `emacs` since I do not use it, and `nano` because, from Fedora 33 on, it is set as the default editor). Memorable mentions: Leafpad (`leafpad`), a lightweight editor, and TeXworks (`texworks`), one of the best TeX editors out there.

    ```bash
    $ sudo dnf install vim neovim
    ```

2. MPV as a multimedia player. I also set it as my default multimedia player in the system configuration. Memorable mentions: FFmpeg (`ffmpeg`) and Lame (`lame`) for conversion.

    ```bash
    $ sudo dnf install mpv
    ```

3. Some useful command line archiving tools. Of course, `zip` and `tar` are already available out of the box, but it is quite convenient to have support for other archive formats, specially `rar` and `7z` files.

    ```bash
    $ sudo dnf install p7zip p7zip-plugins unrar
    ```

4. The Lilypond engraver for writing sheet music. Memorable mentions: Frescobaldi (`frescobaldi`).

    ```bash
    $ sudo dnf install lilypond
    ```

5. Timidity for playing MIDI files. Include the `gstreamer1-plugins-bad-free-fluidsynth` multimedia codec for use in other applications.

    ```bash
    $ sudo dnf install timidity++
    ```
    
6. Graphic design editors and utilities. Inkscape and GIMP are graphical applications, while Potrace and ImageMagick are command line utilities.

    ```bash
    $ sudo dnf install inkscape gimp potrace ImageMagick
    ```

8. The latest Java virtual machine. Be mindful that the default Java commands might not resolve to this version, so make sure to update the symbolic links with the `update-alternatives` tool.

    ```bash
    $ sudo dnf install java-latest-openjdk \
    java-latest-openjdk-jmods \
    java-latest-openjdk-devel \
    java-latest-openjdk-headless
    ```

9. Other useful applications and tools. Make sure to read their descriptions before installing them in the wild. I would rather have only the packages I actually need on a daily basis.

    ```bash
    $ sudo dnf install ack zsh audacity-freeworld
    ```

## Editors

From Fedora 31 on, I decided to take a different approach regarding `vim`. Instead of relying on a distribution, I use `vim-plug` to manage my plug-ins. Simply run the following  command in your terminal session:

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

If you use NeoVim, use this command instead:

```bash
curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Now, we need to populate our configuration file with the plug-ins we want to use. It is just a matter of including a `Plug` line in between the `vim-plug` block. Simply add the following lines to your `.vimrc` to include your plug-ins:

```vim
call plug#begin('~/.vim/plugged')

Plug 'godlygeek/tabular'
Plug 'itchyny/lightline.vim'
Plug 'sheerun/vim-polyglot'
Plug 'sainnhe/edge'
Plug 'preservim/nerdtree'
Plug 'psliwka/vim-smoothie'
Plug 'mhinz/vim-startify'

call plug#end()
```

Once the plug-ins are properly referenced in the configuration file, we need to fetch them for actual use! This is a simple procedure, open `vim` and call:

```vim
:PlugInstall
```

Wait a couple of seconds and your `vim` is now ready! This is my typical `.vimrc` configuration file:

```vim
"/home/paulo/.config/nvim

call plug#begin('~/.vim/plugged')

Plug 'godlygeek/tabular'
Plug 'itchyny/lightline.vim'
Plug 'sheerun/vim-polyglot'
Plug 'sainnhe/edge'
Plug 'preservim/nerdtree'
Plug 'psliwka/vim-smoothie'
Plug 'mhinz/vim-startify'

call plug#end()

set nocompatible

filetype plugin indent on
syntax on

set autoindent
set expandtab

set softtabstop=4
set shiftwidth=4
set shiftround

set backspace=indent,eol,start
set hidden

set incsearch
set nohlsearch

set ttyfast
set lazyredraw

set number
set ruler

set noshowmode
set laststatus=2

set background=dark
if has('termguicolors')
    set termguicolors
endif

let g:edge_style = 'aura'
let g:edge_enable_italic = 0
let g:edge_disable_italic_comment = 1

let g:lightline = {'colorscheme' : 'edge'}

colorscheme edge

let g:startify_fortune_use_unicode = 1
let g:startify_custom_footer =
    \ ['', "   ooh vim", '']
```

My configuration file is also available as a `.vimrc.sample` file in this repository, so it can be used for a quick setup.

If you use NeoVim, there is my configuration file, available as `init.vim.sample` in this repository. Copy it to `~/.config/nvim/init.vim` (notice the file renaming).

## Fortune cookies in the terminal

This is a very amusing feature to add to your terminal. First of all, we need to install `fortune-mod`:

```bash
$ sudo dnf install fortune-mod
```

Then add the following content to `~/.bashrc`:

```bash
if [ -f /usr/bin/fortune ]; then
    /usr/bin/fortune
    echo
fi
```

There is no need to restart your entire session. Simply restart your terminal (or open a new tab) and have fun! My configuration file is also available as `.bashrc.sample` in this repository.

## My `/opt` configuration

I usually configure third-party applications in a different fashion in my Fedora machines. For starters, I create a personal directory inside `/opt` for my current user:

```bash
$ cd /opt
$ sudo mkdir `whoami`
$ sudo chown `whoami`.`whoami` `whoami`
$ cd `whoami`
$ mkdir applications
```

Then I redirect most of the installations to the `applications` directory. :wink: 

## A fancy Z shell

A great enhancement to `zsh` is [Oh my `zsh`](https://github.com/robbyrussell/oh-my-zsh). I personally do not like the last part of the install script, as it changes your current shell. I usually execute the following command:

```bash
$ MYOPT="/opt/`whoami`/applications"
$ ZSH="${MYOPT}/oh-my-zsh" CHSH="no" sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

I like to use the `spaceship` theme for my Z shell. In order to configure it, run inside `zsh`:

```zsh
$ git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt" --depth=1
$ ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
```

I also recommend installing the syntax highlighting plugin. Run the following command inside `zsh`:

```zsh
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

This is my `.zshrc`, with comments omitted:

```zsh
export ZSH="/opt/paulo/applications/oh-my-zsh"
ZSH_THEME="spaceship"

CASE_SENSITIVE="true"
DISABLE_AUTO_UPDATE="true"
COMPLETION_WAITING_DOTS="true"

plugins=(
    git
    colored-man-pages
    colorize
    common-aliases
    copyfile
    zsh-syntax-highlighting
)

source $ZSH/oh-my-zsh.sh

if [ -f /usr/bin/fortune ]; then
    /usr/bin/fortune
    echo
fi

export SDKMAN_DIR="/opt/paulo/applications/sdkman"
[[ -s "/opt/paulo/applications/sdkman/bin/sdkman-init.sh" ]] && source "/opt/paulo/applications/sdkman/bin/sdkman-init.sh"
```

I still favour bash, but Z is an interesting shell. :wink: My configuration file is also available as `.zshrc.sample` in this repository.

## SDKMAN!

SDKMAN! is a tool for managing Java-based tools and virtual machines. If you are a developer and wants to live dangerously, I recommend this script. Simply run the following commands:

```bash
$ MYOPT="/opt/`whoami`/applications"
$ export SDKMAN_DIR="${MYOPT}/sdkman" && curl -s "https://get.sdkman.io" | bash
```

There we go. :wink:

## Install and configure TeX Live

TeX Live is, in my humble opinion, the best TeX distribution out there. Let us get the install script from TUG and run:

```bash
$ wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
$ tar xvzf install-tl-unx.tar.gz
$ cd install-tl-20*
$ sudo ./install-tl
```

After installing TeX Live, let us create a symbolic link:

```bash
$ sudo ln -s /usr/local/texlive/<year>/bin/<arch> /opt/texbin
```

Create a file named `texlive.sh` with the following content:

```bash
#!/bin/bash
pathmunge () {
    if ! echo $PATH | /bin/egrep -q "(^|:)$1($|:)" ; then
        if [ "$2" = "after" ] ; then
            PATH=$PATH:$1
        else
            PATH=$1:$PATH
        fi
    fi
}
pathmunge /opt/texbin
unset pathmunge
```

Then move it to `/etc/profile.d/`:

```bash
$ sudo mv texlive.sh /etc/profile.d/
```

The script is available as `texlive.sh.sample` in this repository. :wink:

The following procedure is optional. I personally had an unfortunate experience, but feel free to configure OpenType fonts from TeX Live in your system:

```bash
$ sudo cp $(kpsewhich -var-value TEXMFSYSVAR)/fonts/conf/texlive-fontconfig.conf \
/etc/fonts/conf.d/09-texlive.conf
$ sudo fc-cache -fsv
```

## Apache NetBeans

I usually do two additional steps when configuring Apache NetBeans in my machine. First, I edit `etc/netbeans.conf` and append the following instruction in the `netbeans_default_options` key:

```ini
 netbeans_default_options=" ... -J-Djava.io.tmpdir=/opt/paulo/temp"
```

Second, I create a `netbeans.desktop` in the following location:

```bash
$ vim ~/.local/share/applications/netbeans.desktop
```

And add the following lines:

```ini
[Desktop Entry]
Version=1.0
Type=Application
Name=NetBeans IDE
Icon=/opt/paulo/applications/netbeans/nb/netbeans.icns
Exec="/opt/paulo/applications/netbeans/bin/netbeans"
Comment=The smarter way to code
Categories=Development;IDE;
Terminal=false
```

The file is available as `netbeans.desktop.sample` in this repository. :wink:

## Configuring `git`

This is my `git` configuration:

```bash
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
$ git config --global core.editor vim
$ git config --global push.default simple
```

*To be continued.* :wink:
