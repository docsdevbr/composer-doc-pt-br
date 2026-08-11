---
# SPDX-FileCopyrightText: Nils Adermann, Jordi Boggiano.
#
# SPDX-License-Identifier: MIT
# Documentation licensed under the MIT License.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/composer-docs-pt-br/blob/-/LICENSES/MIT.txt

source_url: https://github.com/composer/composer/blob/2.10.2/doc/faqs/why-are-unbound-version-constraints-a-bad-idea.md
source_revision: 955194f8969bdbf110475f3eda604f869d059133
translation_status: ready
---

# Por que restrições de versão sem limite superior são uma má ideia?

Uma restrição de versão sem limite superior, como `*`, `>=3.4` ou `dev-master`,
permite atualizações para qualquer versão futura da dependência.
Isso inclui versões principais que quebram a compatibilidade com versões
anteriores.

Uma vez que é criada a tag de uma versão do seu pacote, você não pode mais
ajustar suas dependências caso uma delas quebre a compatibilidade; você precisa
lançar uma nova versão, mas a anterior permanece quebrada.

A única boa alternativa é definir um limite superior nas suas restrições, o qual
você pode aumentar em uma nova versão após testar se o seu pacote é compatível
com a nova versão principal da sua dependência.

Por exemplo, em vez de usar `>=3.4`, você deve usar `^3.4`, que permite todas
as versões até `3.999`, mas não inclui a `4.0` ou superior.
O operador `^` funciona muito bem com bibliotecas que seguem o
[versionamento semântico](https://semver.org).

**Nota:** Como pessoa mantenedora de um pacote, você pode ajudar suas pessoas
usuárias fornecendo uma [versão de alias](../articles/aliases.md) para seu
branch de desenvolvimento, permitindo que ela corresponda a restrições de versão
limitadas.
