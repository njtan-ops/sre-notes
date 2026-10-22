Download app and put to directory
```
sudo curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.tar.gz
sudo rm -rf /opt/nvim
sudo tar -C /opt -xzf nvim-linux-x86_64.tar.gz
sudo mv /opt/nvim-linux-x86_64 /opt/nvim
rm -f nvim-linux-x86_64.tar.gz
```

if different architecture
```
# 1. Download the ARM64 release
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-arm64.tar.gz

# 2. Extract into /opt
rm -rf /opt/nvim
tar -C /opt -xzf nvim-linux-arm64.tar.gz
mv /opt/nvim-linux-arm64 /opt/nvim

# 3. Clean up the archive
rm -f nvim-linux-arm64.tar.gz

# 4. Add Neovim to your PATH
ln -sf /opt/nvim/bin/nvim /usr/local/bin/nvim

nvim --version
```

link directory
```
sudo ln -sf /opt/nvim/bin/nvim /usr/local/bin/nvim
```

if it says not found, do this
```
hash -r 
sudo ln -sf /opt/nvim/bin/nvim /usr/bin/nvim
```

installing nerdfont

```
sudo dnf install -y --allowerasing fontconfig curl unzip xz 
mkdir -p ~/.local/share/fonts
cd ~/.local/share/fonts
curl -LO https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.tar.xz
tar -xf JetBrainsMono.tar.xz
rm -f JetBrainsMono.tar.xz
fc-cache -fv

echo "--------TESTING------"
echo -e "\uf113 \ue702 \ue795 \uf489 \ue615"
```

installing tree-sitter
```
sudo dnf install -y nodejs npm gcc gcc-c++ make
sudo npm install -g tree-sitter-cli@0.22.6
tree-sitter --version
```

tree-sitter cargo route
```
sudo dnf install -y cargo
cargo install --root /usr/local tree-sitter-cli
sudo dnf install -y nodejs
```

installing ripgrep
```
sudo dnf install -y ripgrep
rg --version
```

if ripgrep not found
```
dnf install -y epel-release
sudo dnf install -y ripgrep
rg --version
```

adding nvchad config
```
cd
sudo dnf install -y git
git clone https://github.com/NvChad/starter ~/.config/nvim && nvim
```

if permission denied
```
sudo chown -R $USER:$USER ~/.config
```


once done, open nvim and do :Mason to install pyright and ruff, press i to install

go to ~/.config/nvim/lua/configs/lspconfig.lua
```
local servers = { "html", "cssls", "pyright" }
vim.lsp.enable(servers)
```

do :TSInstall python
go to .config/nvim/lua/plugins/init.lua

```
require("nvim-treesitter.configs").setup({
  ensure_installed = { "python", "lua", "vim", "vimdoc", "bash" },
  highlight = {
    enable = true,
  },
})
```