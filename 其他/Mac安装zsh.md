###查看 Mac 上已有的 shell

```bash
cat /etc/shells

/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
```

### 将默认 shell 改成 zsh

```bash
chsh -s /bin/zsh
```

### 安装 oh my zsh

```bash
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

### 安装 autojump 插件

```bash
brew install autojump # 确保有 brew 命令
```

### 安装 zsh-autosuggestions 插件

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

编辑～/.zshrc 文件

```bash
vim ~/.zshrc

plugins=(git zsh-autosuggestions)
```

### 安装 zsh-syntax-highlighting 插件

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

编辑～/.zshrc 文件

```bash
vim ~/.zshrc

plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

###最后

```bash
source ~/.zshrc
```

