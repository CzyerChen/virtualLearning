> 都说普通青年用bash, 装逼青年用zsh,文艺青年用fish,之前也接触到过这两种shell，但是并没有投入使用，可能也是因为其实用shell不多，今天在安装了解一下
> [配置指南](https://blog.csdn.net/gatieme/article/details/52741221)

### 什么是zsh?
- sh有：bash zsh csh ksh tcsh 这么多总有各自的特点才能存活
```text
cat /etc/shells
```
- 其中zsh就主要使用因为他的强大了
- sh工具主要就是为了解决如何将敲击的命令转化为计算机层面的指令，来通知计算机做要做的事


### 安装zsh
- MAC自带zsh
- ubuntu: sudo apt-get install zsh
- redhat: sudo yum install zsh
- 切换zsh 和 bash
```text
 chsh -s /bin/bash
 chsh -s /bin/zsh
 echo $
```

### 安装oh-my-zsh
- 我们通过安装[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)来简化zsh的配置,github上面也提供了说明
- 可以通过curl/wget进行
- 我这边是MAC就采取了curl
```text
curl:
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

wget:
sh -c "$(wget -O- https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

```
- 下载好之后，会提示是否需要将zsh设置为默认的系统bash,这边自然是先不要，除非你已经对系统bash深恶痛绝

### 配置zshrc和插件
- vi ~/.zshrc    这个配置就是默认在用户根目录下的$HOME,并且不会因为卸载二删除，是为了重新下载能够不用重新设置一些个性化的东西
- 在配置文件里面，可以默认使用一些插件，那么在敲击命令行的时候，你就能够发现有大量的提示，让你不用再百度了
```text
plugins=(
  git
  bundler
  dotenv
  osx
  rake
  rbenv
  ruby
)
```
- zsh的配置文件仅此一个，很好管理，不需要类似在linux下每一个用户都需要设置一些公用配置，类似于一些命令的简写，应用文件的默认打开方式
```text
alias cls='clear'
alias ll='ls -l'
alias la='ls -a'
alias vi='vim'
alias javac="javac -J-Dfile.encoding=utf8"
alias grep="grep --color=auto"
alias -s html=mate   # 在命令行直接输入后缀为 html 的文件名，会在 TextMate 中打开
alias -s rb=mate     # 在命令行直接输入 ruby 文件，会在 TextMate 中打开
alias -s py=vi       # 在命令行直接输入 python 文件，会用 vim 中打开，以下类似
alias -s js=vi
alias -s c=vi
alias -s java=vi
alias -s txt=vi
alias -s gz='tar -xzvf'
alias -s tgz='tar -xzvf'
alias -s zip='unzip'
alias -s bz2='tar -xjvf'

alias -s html=mate，意思就是你在命令行输入 hello.html，zsh会为你自动打开 TextMat 并读取 hello.html； 
alias -s gz='tar -xzvf'，表示自动解压后缀为 gz 的压缩包。
看上去就很强，很厉害的样子

```
- zsh的配置很复杂，但是oh-my-zsh简化了配置，安装也只需要通过提供的install.sh进行一键安装
- 配置主题，配置插件，强大的自动执行和补全功能


- [更多参考1](https://www.jianshu.com/p/d194d29e488c?open_source=weibo_search)
- [更多参考2](https://zhuanlan.zhihu.com/p/19556676)
- 卸载oh-my-zsh：uninstall_oh_my_zsh



