---
# SPDX-FileCopyrightText: Nils Adermann, Jordi Boggiano.
#
# SPDX-License-Identifier: MIT
# Documentation licensed under the MIT License.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/composer-docs-pt-br/blob/-/LICENSES/MIT.txt

source_url: https://github.com/composer/composer/blob/2.10.3/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md
source_revision: bb128c465ca852076bd1bd70a77d7cc6d918d57b
translation_status: ready
---

# Devo fazer o commit das dependências do meu diretório `vendor`?

A recomendação geral é **não**.
O diretório `vendor` (ou onde quer que suas dependências estejam instaladas)
deve ser adicionado ao `.gitignore`/`svn:ignore`/etc.

A melhor prática é fazer com que todas as pessoas desenvolvedoras usem o
Composer para instalar as dependências.
Da mesma forma, o servidor de construção, CI, ferramentas de implantação, etc.,
devem ser adaptados para executar o Composer como parte da inicialização do
projeto.

Embora possa ser tentador fazer o commit desse diretório em alguns ambientes,
isso gera alguns problemas:

- Tamanho excessivo do repositório VCS e diffs grandes ao atualizar o código.
- Duplicação do histórico de todas as suas dependências no seu próprio VCS.
- Adicionar dependências instaladas via git a um repositório git fará com que
  elas apareçam como submódulos.
  Isso é problemático porque não são submódulos reais, o que causará problemas.

Se você realmente acha que precisa fazer isso, tem algumas opções:

1. Limite-se a instalar versões com tags (sem versões de desenvolvimento), para
   obter apenas instalações via arquivo compactado (zip) e evitar problemas com
   os "submódulos" do git.
2. Use `--prefer-dist` ou defina `preferred-install` como `dist` na sua
   [configuração](../04-schema.md#config).
3. Remova o diretório `.git` de cada dependência após a instalação; assim, você
   poderá adicioná-las ao seu repositório git.
   Você pode fazer isso com `rm -rf vendor/**/.git` no ZSH ou
   `find vendor/ -type d -name ".git" -exec rm -rf {} \;` no Bash.
   No entanto, isso significa que você terá que excluir essas dependências do
   disco antes de executar o `composer update`.
4. Adicione uma regra ao `.gitignore` (`/vendor/**/.git`) para ignorar todas as
   pastas `.git` dentro do diretório `vendor`.
   Essa abordagem não exige a exclusão das dependências do disco antes de
   executar o `composer update`.
