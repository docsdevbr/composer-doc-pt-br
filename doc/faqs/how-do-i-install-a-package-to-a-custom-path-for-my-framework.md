---
# SPDX-FileCopyrightText: Nils Adermann, Jordi Boggiano.
#
# SPDX-License-Identifier: MIT
# Documentation licensed under the MIT License.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/composer-doc-pt-br/blob/-/LICENSES/MIT.txt

source_url: https://github.com/composer/composer/blob/2.10.2/doc/faqs/how-do-i-install-a-package-to-a-custom-path-for-my-framework.md
source_revision: 239638e687e8e67a2f1dadf225731fcd6309e8c4
translation_status: ready
---

# Como instalo um pacote em um caminho personalizado para o meu framework?

Cada framework pode ter um ou vários caminhos de instalação de pacotes
obrigatórios diferentes.
O Composer pode ser configurado para instalar pacotes em uma pasta diferente da
pasta padrão `vendor` usando o
[composer/installers](https://github.com/composer/installers).

Se você é uma **pessoa autora de pacotes** e deseja que seu pacote seja
instalado em um diretório personalizado, adicione `composer/installers` como
dependência e defina o `type` apropriado.
Especificar o tipo de pacote substituirá o caminho de instalação padrão.
Isso é comum se o seu pacote for destinado a um framework específico, como
CakePHP, Drupal ou WordPress.
Aqui está um exemplo de arquivo `composer.json` para um tema do WordPress:

```json
{
    "name": "você/nome-do-tema",
    "type": "wordpress-theme",
    "require": {
        "composer/installers": "~1.0"
    }
}
```

Agora, quando seu tema for instalado com o Composer, ele será colocado na pasta
`wp-content/themes/nome-do-tema/`.
Verifique os
[tipos suportados atualmente](https://github.com/composer/installers#current-supported-package-types)
para o seu pacote.

Como **pessoa consumidora de pacotes**, você pode definir ou substituir o
caminho de instalação de um pacote que requer o `composer/installers`
configurando a opção `installer-paths` na seção `extra`.
Um exemplo útil seria uma configuração multisite do Drupal, onde o pacote deve
ser instalado em um subdiretório do seu site.
Aqui, estamos substituindo o caminho de instalação de um módulo que usa o
`composer/installers` e também colocando todos os pacotes do tipo
`drupal-theme` em uma pasta de temas:

```json
{
    "extra": {
        "installer-paths": {
            "sites/example.com/modules/{$name}": ["fornecedor/pacote"],
            "sites/example.com/themes/{$name}": ["type:drupal-theme"]
        }
    }
}
```

Agora, o pacote será instalado na pasta que você definiu, em vez do local padrão
determinado pelo `composer/installers`.
Além disso, `installer-paths` depende da ordem; isso significa que mover um
pacote por nome deve vir antes do caminho de instalação de um `type:*` que
corresponda a esse mesmo pacote.

> **Nota:** Você não pode usar isso para alterar o caminho de qualquer pacote.
> Isso se aplica apenas a pacotes que requerem `composer/installers` e usam um
> tipo personalizado suportado por ele.
