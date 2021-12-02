# Assisstants in work
## Changing shell

```bash
sudo chsh -s /bin/zsh
```

# Install plugins

Add in your .zshrc rows:

```bash
source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
```
```bash
source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

# Install plugin manager


Oh-my-zsh:

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Install zsh theme:

git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

nano .zshrc and change the value of row ZSH_THEME= into ZSH_THEME="powerlevel10k/powerlevel10k"

# Install user-friendly console editor

```bash
sudo eopkg it micro
```


**Other languages**:

Русский: https://github.com/Solus-users/solus-guides/blob/main/ru/terminal-without-pain.md
