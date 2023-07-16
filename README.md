



# TABLE OF CONTENTS

• [VIM OPTIONS](#vim-options)
• [AUTOCOMMANDS](#autocommands)
• [GENERAL](#general)
• [GENERIC LSP](#generic-lsp)
• [DISABLE AUTOMATIC INSTALLATION OF SERVERS](#disable-automatic-installation-of-servers)
• [CONFIGURE A SERVER MANUALLY](#configure-a-server-manually)
• [LINTERS, FORMATTERS AND CODE-ACTIONS](#linters-formatters-and-code-actions)
• [KEYMAPPINGS](#keymappings)
• [LEADER KEY](#leader-key)
• [GENERAL](#general)
• [WHICH KEY](#which-key)
• [TREESITER](#treesiter)
• [PLUGINS](#plugins)


# VIM OPTIONS

`lvim` is the global options object
```lua
vim.opt.shiftwidth = 2
vim.opt.tabstop = 2
vim.opt.relativenumber = true
```



# AUTOCOMMANDS

(`:help autocmd`) https://neovim.io/doc/user/autocmd.html

Automatically tangling and exporting the markdown file if the filename matches pattern "README.norg"
```lua
vim.api.nvim_create_autocmd("BufWritePost", {
  pattern = "README.norg",
  callback = function()
    vim.api.nvim_command("Neorg tangle current-file")
    vim.api.nvim_command("Neorg export to-file README.md")
  end,
})
```

Let treesitter use bash highlight for zsh files as well
```lua
vim.api.nvim_create_autocmd("FileType", {
  pattern = "zsh",
  callback = function()
    require("nvim-treesitter.highlight").attach(0, "bash")
  end,
})
```


# GENERAL

```lua
lvim.log.level = "info"
lvim.format_on_save = {
  enabled = true,
  pattern = "*.lua",
  timeout = 1000,
}
```

to disable icons and use a minimalist setup, uncomment the following
``` lua
lvim.use_icons = false
```

```lua
lvim.colorscheme = "tokyonight-night"

lvim.builtin.alpha.active = true
lvim.builtin.alpha.mode = "dashboard"
lvim.builtin.terminal.active = true
lvim.builtin.nvimtree.setup.view.side = "left"
lvim.builtin.nvimtree.setup.renderer.icons.show.git = false
```


# GENERIC LSP

## DISABLE AUTOMATIC INSTALLATION OF SERVERS


``` lua
lvim.lsp.installer.setup.automatic_installation = false
```


## CONFIGURE A SERVER MANUALLY

IMPORTANT: Requires `:LvimCacheReset` to take effect. See the full default list `:lua =lvim.lsp.automatic_configuration.skipped_servers`

``` lua
vim.list_extend(lvim.lsp.automatic_configuration.skipped_servers, { "pyright" })
local opts = {} -- check the lspconfig documentation for a list of all possible options
require("lvim.lsp.manager").setup("pyright", opts)
```

Remove a server from the skipped list, e.g. eslint, or emmet_ls. IMPORTANT: Requires `:LvimCacheReset` to take effect. `:LvimInfo` lists which server(s) are skipped for the current filetype

``` lua
lvim.lsp.automatic_configuration.skipped_servers = vim.tbl_filter(function(server)
  return server ~= "emmet_ls"
end, lvim.lsp.automatic_configuration.skipped_servers)
```


## LINTERS, FORMATTERS AND CODE-ACTIONS


``` lua
local formatters = require "lvim.lsp.null-ls.formatters"
formatters.setup {
  { command = "stylua" },
  {
    command = "prettier",
    extra_args = { "--print-width", "100" },
    filetypes = { "typescript", "typescriptreact" },
  },
}
local linters = require "lvim.lsp.null-ls.linters"
linters.setup {
  { command = "flake8", filetypes = { "python" } },
  {
    command = "shellcheck",
    args = { "--severity", "warning" },
  },
}
local code_actions = require "lvim.lsp.null-ls.code_actions"
code_actions.setup {
  {
    exe = "eslint",
    filetypes = { "typescript", "typescriptreact" },
  },
}
```


# KEYMAPPINGS

## LEADER KEY

```lua
lvim.leader = "space"
```


## GENERAL

Making life easire by setting these keybindings to save, cycle buffers, easy escaping and toggling terminal.

```lua
lvim.keys.normal_mode["<C-s>"] = ":w<cr>"
lvim.keys.normal_mode["<S-l>"] = ":BufferLineCycleNext<CR>"
lvim.keys.normal_mode["<S-h>"] = ":BufferLineCyclePrev<CR>"
lvim.keys.insert_mode["jk"] = "<ESC>"
lvim.keys.insert_mode["kj"] = "<ESC>"
lvim.keys.normal_mode["<C-t>"] = ":ToggleTerm<CR>"
```


## WHICH KEY

Use which-key to add extra bindings with the leader-key prefix

``` lua
lvim.builtin.which_key.mappings["W"] = { "<cmd>noautocmd w<cr>", "Save without formatting" }
lvim.builtin.which_key.mappings["P"] = { "<cmd>Telescope projects<CR>", "Projects" }
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
          ["core.export"] = {},
          ["core.export.markdown"] = {},
        }
      }
    end, -- run require("neorg").setup()
  },
}
```
