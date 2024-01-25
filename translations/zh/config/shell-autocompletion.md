
## Shell 自动补全

您可以为 `bash`, `elvish`, `fish`, `powershell` 和 `zsh` 生成自动补全 shell 脚本。

### zsh

首先，确保以下内容存在于您的  `~/.zshrc` 文件中（如果没有，请添加）：

```sh
autoload -U compinit
compinit -i
```

然后运行：

```sh
forge completions zsh | sudo tee /usr/local/share/zsh/site-functions/_forge
cast completions zsh | sudo tee /usr/local/share/zsh/site-functions/_cast
anvil completions zsh | sudo tee /usr/local/share/zsh/site-functions/_anvil
```

对于基于 ARM 的系统：

```sh
forge completions zsh > /opt/homebrew/completions/zsh/_forge
cast completions zsh > /opt/homebrew/completions/zsh/_cast
anvil completions zsh > /opt/homebrew/completions/zsh/_anvil
```

### fish

```sh
mkdir -p $HOME/.config/fish/completions
forge completions fish > $HOME/.config/fish/completions/forge.fish
cast completions fish > $HOME/.config/fish/completions/cast.fish
anvil completions fish > $HOME/.config/fish/completions/anvil.fish
source $HOME/.config/fish/config.fish
```

### bash

```sh
mkdir -p $HOME/.local/share/bash-completion/completions
forge completions bash > $HOME/.local/share/bash-completion/completions/forge
cast completions bash > $HOME/.local/share/bash-completion/completions/cast
anvil completions bash > $HOME/.local/share/bash-completion/completions/anvil
exec bash
```
