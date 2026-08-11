---
# SPDX-FileCopyrightText: Nils Adermann, Jordi Boggiano.
#
# SPDX-License-Identifier: MIT
# Documentation licensed under the MIT License.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/composer-docs-pt-br/blob/-/LICENSES/MIT.txt

source_url: https://github.com/composer/composer/blob/2.10.2/doc/faqs/how-to-use-composer-behind-a-proxy.md
source_revision: bb8387e5a0680769be7a1e4a37f5057dbe135b28
translation_status: ready
---

# Como usar o Composer atrás de um proxy

O Composer, assim como muitas outras ferramentas, usa variáveis de ambiente para
controlar o uso de um servidor proxy e oferece suporte a:

- `http_proxy` - o proxy a ser usado para requisições HTTP.
- `https_proxy` - o proxy a ser usado para requisições HTTPS.
- `CGI_HTTP_PROXY` - o proxy a ser usado para requisições HTTP em um contexto
  que não seja CLI.
- `no_proxy` - domínios que não requerem proxy.

Esses nomes de variáveis constituem uma convenção, e não um padrão oficial,
sendo complexa a sua evolução e uso em diferentes sistemas operacionais e
ferramentas.
O Composer prefere o uso de nomes em minúsculas, mas aceita nomes em maiúsculas
quando apropriado.

## Uso

O Composer requer variáveis de ambiente específicas para requisições HTTP e
HTTPS.
Por exemplo:

```
http_proxy=http://proxy.com:80
https_proxy=http://proxy.com:80
```

Nomes em maiúsculas também podem ser usados.

### Uso fora da CLI

O Composer não busca por `http_proxy` ou `HTTP_PROXY` em um contexto que não
seja CLI.
Se você o estiver executando dessa forma (ou seja, integração em um CMS ou caso
de uso semelhante), deve usar `CGI_HTTP_PROXY` para requisições HTTP:

```
CGI_HTTP_PROXY=http://proxy.com:80
https_proxy=http://proxy.com:80

# cgi_http_proxy também pode ser usado
```

> **Nota:** A variável CGI_HTTP_PROXY foi introduzida pelo Perl em 2001 para
> evitar a manipulação de cabeçalhos de requisição e foi popularizada em 2016,
> quando essa vulnerabilidade foi amplamente divulgada: https://httpoxy.org

## Sintaxe

Use `esquema://host:porta` conforme os exemplos acima.
Embora a ausência do esquema assuma `http` como padrão e a ausência da porta
assuma `80`/`443` para os esquemas `http`/`https`, outras ferramentas podem
exigir esses valores.

O host pode ser especificado como um endereço IP usando a notação decimal
pontilhada para IPv4 ou entre colchetes para IPv6.

### Autorização

O Composer oferece suporte à autorização básica, usando a sintaxe
`esquema://usuário:senha@host:porta`.
Caracteres reservados da URL presentes no nome de usuário ou na senha devem ser
codificados no formato com porcentagem.
Por exemplo:

```
user:  me@company
pass:  p@ssw$rd
proxy: http://proxy.com:80

# autorização codificada por porcentagem
me%40company:p%40ssw%24rd

scheme://me%40company:p%40ssw%24rd@proxy.com:80
```

> **Nota:** Os componentes de nome de usuário e senha devem ser codificados
> individualmente no formato com porcentagem e, em seguida, combinados com o
> separador de dois-pontos.
> O nome de usuário não pode conter dois-pontos (mesmo que codificados), pois o
> proxy separará os componentes no primeiro caractere de dois-pontos que
> encontrar.

## Servidores proxy HTTPS

O Composer oferece suporte a servidores proxy HTTPS, onde HTTPS é o esquema
usado para a conexão com o proxy, mas apenas a partir do PHP 7.3 com a versão
7.52.0 ou superior do cURL.

```
http_proxy=https://proxy.com:443
https_proxy=https://proxy.com:443
```

## Ignorando o proxy para domínios específicos

Use a variável de ambiente `no_proxy` (ou `NO_PROXY`) para definir uma lista
separada por vírgulas de domínios para os quais o proxy **não** deve ser usado.

```
no_proxy=example.com
# Ignora o proxy para example.com e seus subdomínios

no_proxy=www.example.com
# Ignora o proxy para www.example.com e seus subdomínios, mas não para example.com
```

Um domínio pode ser restrito a uma porta específica (por exemplo, `:80`) e
também pode ser especificado como um endereço IP ou um bloco de endereços IP na
notação CIDR.

Endereços IPv6 não precisam estar entre colchetes, como ocorre nos valores de
`http_proxy`/`https_proxy`, embora esse formato seja aceito.

Definir o valor como `*` fará com que o proxy seja ignorado para todas as
requisições.

> **Nota:** Um ponto inicial no nome do domínio não tem significado e é removido
> antes do processamento.

## Variáveis de ambiente obsoletas

Originalmente, o Composer disponibilizava as variáveis
`HTTP_PROXY_REQUEST_FULLURI` e `HTTPS_PROXY_REQUEST_FULLURI` para mitigar
problemas com proxies que apresentavam comportamento inadequado.
Elas não são mais necessárias nem usadas.

## Alterações nos requisitos

Versões do Composer anteriores à `2.8` usavam `http_proxy` tanto para
requisições HTTP quanto HTTPS caso `https_proxy` não estivesse definido; no
entanto, a partir da versão 2.8.0, o Composer exige variáveis de ambiente
[específicas para o esquema](#uso).

O objetivo dessa mudança é alinhar o Composer às práticas atuais de outras
ferramentas populares.
Para facilitar a transição, a partir da versão 2.7.3 do Composer, o
comportamento original é mantido, mas uma mensagem de alerta é exibida
instruindo a pessoa usuária a adicionar uma variável de ambiente `https_proxy`.

Para evitar o comportamento original durante o período de transição, defina uma
variável de ambiente vazia (`https_proxy=`).
