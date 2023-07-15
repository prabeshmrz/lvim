



# Table of Contents

• [BASIC SETTINGS](#basic-settings)
• [VIM OPTIONS](#vim-options)
• [GENERAL](#general)
• [KEYMAPPINGS](#keymappings)
• [LEADER KEY](#leader-key)
• [GENERAL](#general)
• [PLUGINS](#plugins)


# BASIC SETTINGS

## VIM OPTIONS

```lua
vim.opt.shiftwidth = 2
vim.opt.tabstop = 2
vim.opt.relativenumber = true
```


## GENERAL

```lua
lvim.log.level = "info"
lvim.format_on_save = {
  enabled = true,
  pattern = "*.lua",
  timeout = 1000,
}
-- lvim.use_icons = false

lvim.colorscheme = "tokyonight-night"

lvim.builtin.alpha.active = true
lvim.builtin.alpha.mode = "dashboard"
lvim.builtin.terminal.active = true
lvim.builtin.nvimtree.setup.view.side = "left"
lvim.builtin.nvimtree.setup.renderer.icons.show.git = false
```


## KEYMAPPINGS

### LEADER KEY

```lua
lvim.leader = "space"
```


### GENERAL

Making life easire by setting these keybindings to save, cycle buffers, easy escaping and toggling terminal.

```lua
lvim.keys.normal_mode["<C-s>"] = ":w<cr>"
lvim.keys.normal_mode["<S-l>"] = ":BufferLineCycleNext<CR>"
lvim.keys.normal_mode["<S-h>"] = ":BufferLineCyclePrev<CR>"
lvim.keys.insert_mode["jk"] = "<ESC>"
lvim.keys.insert_mode["kj"] = "<ESC>"
lvim.keys.normal_mode["<C-t>"] = ":ToggleTerm<CR>"
```


# TREESITER

Automatically install missing parsers when entering buffer
```lua
lvim.builtin.treesitter.auto_install = true
-- lvim.builtin.treesitter.ignore_install = { "haskell" }
```

Always installed on startup, useful for parsers without a strict filetype
``` lua
lvim.builtin.treesitter.ensure_installed = { "comment", "markdown_inline", "regex" }
```




# PLUGINS

```lua
lvim.plugins = {
  { "folke/tokyonight.nvim" },
  { "lervag/vimtex" },
  {
    "nvim-neorg/neorg",
    build = ":Neorg sync-parsers", -- This is the important bit!
    ft = "norg",                   -- lazy-load on filetype
    config = function()
      require("neorg").setup {
        load = {
          ["core.defaults"] = {},
          ["core.concealer"] = {},
          ["core.tangle"] = {},
        }
      }
    end, -- run require("neorg").setup()
  },
}
```
