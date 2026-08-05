---
# SPDX-FileCopyrightText: Nils Adermann, Jordi Boggiano.
#
# SPDX-License-Identifier: MIT
# Documentation licensed under the MIT License.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/composer-doc-pt-br/blob/-/LICENSES/MIT.txt

source_url: https://github.com/composer/composer/blob/2.10.2/doc/faqs/how-to-install-composer-programmatically.md
source_revision: defa1bb2ee520c276d839613d9287fcf441c79e4
translation_status: ready
---

# Como instalo o Composer de forma programática?

Conforme indicado na página de download, o script do instalador contém um
checksum que muda quando o código do instalador é alterado; portanto, não se
deve depender dele a longo prazo.

Uma alternativa é usar este script, que funciona apenas com utilitários UNIX:

```shell
#!/bin/sh

EXPECTED_CHECKSUM="$(php -r 'copy("https://composer.github.io/installer.sig", "php://stdout");')"
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
ACTUAL_CHECKSUM="$(php -r "echo hash_file('sha384', 'composer-setup.php');")"

if [ "$EXPECTED_CHECKSUM" != "$ACTUAL_CHECKSUM" ]
then
    >&2 echo 'ERROR: Invalid installer checksum'
    rm composer-setup.php
    exit 1
fi

php composer-setup.php --quiet
RESULT=$?
rm composer-setup.php
exit $RESULT
```

O script encerrará com código `1` em caso de falha, ou `0` em caso de sucesso, e
não gera saída se nenhum erro ocorrer.

Alternativamente, se você quiser depender de uma cópia exata do instalador, pode
obter uma versão específica do histórico do GitHub.
O hash do commit deve ser suficiente para garantir unicidade e autenticidade,
desde que você confie nos servidores do GitHub.
Por exemplo:

```shell
wget https://raw.githubusercontent.com/composer/getcomposer.org/f3108f64b4e1c1ce6eb462b159956461592b3e3e/web/installer -O - -q | php -- --quiet
```

Você pode substituir o hash do commit pelo hash do commit mais recente em
https://github.com/composer/getcomposer.org/commits/main

## Usando o utilitário de CLI do GitHub (`gh`)

Você pode baixar e verificar o `composer.phar` usando o utilitário de CLI `gh`
da seguinte forma:

```shell
gh release --repo composer/composer download --pattern composer.phar
gh attestation verify --repo composer/composer composer.phar
```

Use o `composer.phar` como está ou mova-o para o local desejado posteriormente.
