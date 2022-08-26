+++
author = "noname"
title = "mason.nvimでFormatter, Linterを自動マッピングする"
date = "2022-08-25"
description = "zennと同じ"
tags = [
		"vim",
    "neovim",
]
categories = [
    "memo",
]
series = ["tech memo"]
aliases = ["migrate-from-jekyl"]
image = "pawel-czerwinski-8uZPynIu-rQ-unsplash.jpg"
slug = "mason.nvim-memo"
+++

`mason-lspconfig` でLSPを自動マッピングすることはヘルプにも方法が書いてあり、すぐにできますがFormatter, Linterは少しドキュメントを読まないといけないので、書き留めておきます。  
フォーマットには `null-ls` を使用します。  

```lua
local mason = require("mason")
local mason_package = require("mason-core.package")
local mason_registry = require("mason-registry")
local null_ls = require("null-ls")


mason.setup({})

local null_sources = {}

for _, package in ipairs(mason_registry.get_installed_packages()) do
        local package_categories = package.spec.categories[1]
        if package_categories == mason_package.Cat.Formatter then
                table.insert(null_sources, null_ls.builtins.formatting[package.name])
        end
        if package_categories == mason_package.Cat.Linter then
                table.insert(null_sources, null_ls.builtins.diagnostics[package.name])
        end
end

null_ls.setup({
        sources = null_sources,
})
```

## PackageSpecについて

```lua
require("mason-registry").get_installed_packages()
```

でインストールされているパッケージをPackageの配列で受け取ることができます。  
`Package.spec` によってPackageSpecを受け取ることができ、これには  
- name
- desc
- homepage
- categories
- languages
- install

の値が長さ1の配列として含まれています。  
`formatter.nvim` などを使用する場合、lua: styluaなど言語名が必要ですが、ここから取ることができます。
