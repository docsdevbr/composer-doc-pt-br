---
# SPDX-FileCopyrightText: Nils Adermann, Jordi Boggiano.
#
# SPDX-License-Identifier: MIT
# Documentation licensed under the MIT License.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/composer-docs-pt-br/blob/-/LICENSES/MIT.txt

source_url: https://github.com/composer/composer/blob/2.10.2/doc/articles/authentication-for-private-packages.md
source_revision: 00b43d4ebd9c031e6f3be18113f948eee70efe8b
translation_status: ready

tagline: Acesse pacotes e repositórios privados
---

# Autenticação para pacotes e repositórios privados

Seu [servidor de pacotes privado](handling-private-packages.md) ou sistema de
controle de versão provavelmente está protegido por uma ou mais opções de
autenticação.
Para permitir que seu projeto acesse esses pacotes e repositórios, você
precisará informar ao Composer como se autenticar no servidor que os hospeda.

## Princípios de autenticação

Sempre que o Composer encontra um repositório protegido, ele tenta autenticar-se
usando primeiro as credenciais já definidas.
Quando nenhuma dessas credenciais se aplica, ele solicita as credenciais e as
salva (ou salva um token, caso consiga obtê-lo).

| Tipo                                                                  | Gerado por solicitação? |
|-----------------------------------------------------------------------|:-----------------------:|
| [http-basic](#http-basic)                                             |           sim           |
| [http-basic em linha](#http-basic-em-linha)                           |           não           |
| [HTTP Bearer](#http-bearer)                                           |           não           |
| [Cabeçalhos customizados](#cabeçalhos-customizados)                   |           não           |
| [Cabeçalhos customizados em linha](#cabeçalhos-customizados-em-linha) |           não           |
| [gitlab-oauth](#gitlab-oauth)                                         |           sim           |
| [gitlab-token](#gitlab-token)                                         |           sim           |
| [github-oauth](#github-oauth)                                         |           sim           |
| [bitbucket-oauth](#bitbucket-oauth)                                   |           sim           |
| [Certificados TLS de cliente](#certificados-tls-de-cliente)           |           não           |
| [forgejo-token](#forgejo-token)                                       |           sim           |

Às vezes, a autenticação automática não é possível, ou você pode querer
predefinir credenciais de autenticação.

As credenciais podem ser armazenadas em 4 locais diferentes: em um arquivo
`auth.json` do projeto, em um `auth.json` global, no próprio `composer.json` ou
na variável de ambiente `COMPOSER_AUTH`.

### Autenticação por projeto no `auth.json`

Neste método de armazenamento de autenticação, um arquivo `auth.json` estará
presente na mesma pasta que o arquivo `composer.json` do projeto.
Você pode criar e editar esse arquivo usando a linha de comando ou
editá-lo/criá-lo manualmente.

> **Nota: Certifique-se de que o arquivo `auth.json` esteja no `.gitignore`**
> para evitar expor credenciais no seu histórico do git.

### Credenciais de autenticação globais

Se você não quiser fornecer credenciais para cada projeto em que trabalha,
armazenar suas credenciais globalmente pode ser uma ideia melhor.
Essas credenciais são armazenadas em um `auth.json` global no diretório inicial
do Composer.

#### Editando as credenciais globais na linha de comando

É possível editar todos os métodos de autenticação usando a linha de comando:

- [`http-basic`][11]
- [`http-basic` em linha][12]
- [HTTP Bearer][13]
- [`gitlab-oauth`][14]
- [`gitlab-token`][15]
- [`github-oauth`][16]
- [`bitbucket-oauth`][17]

#### Editando as credenciais de autenticação global manualmente

> **Nota:** Não é recomendado editar manualmente as opções de autenticação, pois
> isso pode resultar em JSON inválido.
> Em vez disso, use preferencialmente [a linha de comando][18].

Para editá-las manualmente, execute o seguinte:

```shell
php composer.phar config --global --editor [--auth]
```

Para implementações de autenticação específicas, consulte suas seções:

- [`http-basic`][19]
- [`http-basic` em linha][20]
- [HTTP Bearer][21]
- [Cabeçalho personalizado][22]
- [`gitlab-oauth`][23]
- [`gitlab-token`][24]
- [`github-oauth`][25]
- [`bitbucket-oauth`][26]

Editar manualmente este arquivo em vez de usar a linha de comando pode resultar
em erros de JSON inválidos.
Para corrigir isso, é preciso abrir o arquivo em um editor e corrigir o erro.
Para encontrar a localização do arquivo `auth.json` global, execute:

```shell
php composer.phar config --global home
```

A pasta conterá o arquivo `auth.json` global, se ele existir.

É possível abrir esse arquivo em um editor e corrigir o erro.

### Autenticação no próprio arquivo `composer.json` {: #autenticacao-no-proprio-arquivo-composer-json }

> **Nota:** **Isso não é recomendado**, pois essas credenciais serão visíveis
> para qualquer pessoa com acesso ao `composer.json` quando ele for
> compartilhado por meio de um sistema de controle de versão como o git ou
> se um atacante obtiver acesso (leitura) aos arquivos do servidor de produção.

Também é possível adicionar credenciais a um `composer.json` por projeto na
seção `config` ou diretamente na definição do repositório.

### Autenticação usando a variável de ambiente `COMPOSER_AUTH` {: #autenticacao-usando-a-variavel-de-ambiente-composer-auth }

> **Nota:** Usar o método de variável de ambiente na linha de comando também tem
> implicações de segurança.
> Essas credenciais provavelmente serão armazenadas em memória e podem ser
> persistidas em um arquivo como `~/.bash_history` (Linux) ou
> `ConsoleHost_history.txt` (PowerShell no Windows) ao fechar a sessão.

A última opção para fornecer credenciais ao Composer é usar a variável de
ambiente `COMPOSER_AUTH`.
Essa variável pode ser passada como variável de linha de comando ou definida
como uma variável de ambiente real.
Leia mais sobre o uso desta variável de ambiente [aqui][27].

## Métodos de autenticação

### Autenticação com `http-basic`

#### Autenticação com `http-basic` na linha de comando

```shell
php composer.phar config [--global] http-basic.repo.example.org usuario senha
```

No comando acima, a chave de configuração `http-basic.repo.example.org` possui
duas partes:

- `http-basic` é o método de autenticação.
- `repo.example.org` é o nome do host do repositório e deve ser substituído pelo
  nome correto.

#### Autenticação manual com `http-basic`

```shell
php composer.phar config [--global] --editor --auth
```

```json
{
    "http-basic": {
        "repo.example.org": {
            "username": "<usuario>",
            "password": "<senha>"
        }
    }
}
```

### Autenticação com `http-basic` em linha

Para o método de autenticação `http-basic` em linha, as credenciais não são
armazenadas em um arquivo `auth.json` separado no projeto ou globalmente, mas
no `composer.json` ou na configuração global no mesmo local onde a definição do
repositório foi adicionada.

Certifique-se de que o nome de usuário e a senha estejam codificados conforme a
[RFC 3986][28] (2.1. Codificação percentual).
Se o nome de usuário, por exemplo, for um endereço de e-mail, ele precisará ser
passado como `name%40example.com`.

#### Autenticação com `http-basic` em linha na linha de comando

```shell
php composer.phar config [--global] repositories composer.unique-name https://<usuario>:<senha>@repo.example.org
```

#### Autenticação manual com `http-basic` em linha

```shell
php composer.phar config [--global] --editor
```

```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://<usuario>:<senha>@example.org"
        }
    ]
}
```

### Autenticação com HTTP Bearer

#### Autenticação com HTTP Bearer na linha de comando

```shell
php composer.phar config [--global] bearer.repo.example.org <token>
```

No comando acima, a chave de configuração `bearer.repo.example.org` possui duas
partes:

- `bearer` é o método de autenticação.
- `repo.example.org` é o nome do host do repositório e deve ser substituído pelo
  nome correto.

#### Autenticação manual com HTTP Bearer

```shell
php composer.phar config [--global] --editor --auth
```

```json
{
    "bearer": {
        "repo.example.org": "<token>"
    }
}
```

### Autenticação com cabeçalho personalizado

#### Autenticação manual com cabeçalho personalizado

```shell
php composer.phar config [--global] --editor
```

```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://example.org",
            "options": {
                "http": {
                    "header": [
                        "<cabecalho>: <token>"
                    ]
                }
            }
        }
    ]
}
```

### Autenticação com `gitlab-oauth`

> **Nota:** Para que a autenticação do GitLab funcione em instâncias privadas do
> GitLab, a seção [`gitlab-domains`][29] também deve conter o URL.

#### Autenticação com `gitlab-oauth` na linha de comando

```shell
php composer.phar config [--global] gitlab-oauth.gitlab.example.org <token>
```

No comando acima, a chave de configuração `gitlab-oauth.gitlab.example.org`
possui duas partes:

- `gitlab-oauth` é o método de autenticação.
- `gitlab.example.org` é o nome do host da instância do GitLab e deve ser
  substituído pelo nome correto ou usar `gitlab.com` se não tiver uma instância
  própria do GitLab.

#### Autenticação manual com `gitlab-oauth`

```shell
php composer.phar config [--global] --editor --auth
```

```json
{
    "gitlab-oauth": {
        "gitlab.example.org": "<token>"
    }
}
```

### Autenticação com `gitlab-token`

> **Nota:** Para que a autenticação do GitLab funcione em instâncias privadas do
> GitLab, a seção [`gitlab-domains`][29] também deve conter o URL.

Para criar um token de acesso, vá para a [seção de tokens de acesso no GitLab]
[30] (ou a URL equivalente em sua instância privada) e crie um token.
Consulte também a [documentação de tokens de acesso do GitLab][31] para obter
mais informações.

Ao criar um token do GitLab manualmente, certifique-se de que ele tenha o escopo
`read_api` ou `api`.

#### Autenticação com `gitlab-token` na linha de comando

```shell
php composer.phar config [--global] gitlab-token.gitlab.example.org <token>
```

No comando acima, a chave de configuração `gitlab-token.gitlab.example.org`
possui duas partes:

- `gitlab-token` é o método de autenticação.
- `gitlab.example.org` é o nome do host da instância do GitLab e deve ser
  substituído pelo nome correto ou usar `gitlab.com` se não tiver uma instância
  própria do GitLab.

#### Autenticação manual com `gitlab-token`

```shell
php composer.phar config [--global] --editor --auth
```

```json
{
    "gitlab-token": {
        "gitlab.example.org": "<token>"
    }
}
```

### Autenticação com `github-oauth`

Para criar um token de acesso, vá para a [seção de configurações de token no
GitHub][32] e [gere um token][33].

Para repositórios públicos, quando a taxa é limitada, um token *sem* escopo
específico é suficiente (ver `(no scope)` na [documentação sobre escopos][34]).
Esses tokens concedem acesso somente leitura a informações públicas.

Para repositórios privados, o escopo `repo` é necessário.
Observe que o token terá amplo acesso de leitura/gravação aos seus repositórios
privados e muito mais - consulte a [documentação sobre escopos][34] para obter
uma lista completa.
No momento da escrita (novembro de 2021), limitar ainda mais as permissões para
esses tokens parece impossível.

Leia mais sobre [tokens de acesso pessoal][35] ou assine o [item do roteiro para
tokens com escopo melhor no GitHub][36].

#### Autenticação com `github-oauth` na linha de comando

```shell
php composer.phar config [--global] github-oauth.github.com <token>
```

No comando acima, a chave de configuração `github-oauth.github.com`
possui duas partes:

- `github-oauth` é o método de autenticação.
- `github.com` é o nome do host ao qual este token se aplica.
  Para o GitHub, isso provavelmente não precisará ser alterado.

#### Autenticação manual com `github-oauth`

```shell
php composer.phar config [--global] --editor --auth
```

```json
{
    "github-oauth": {
        "github.com": "<token>"
    }
}
```

### Autenticação com `bitbucket-oauth`

O driver BitBucket usa OAuth para acessar os repositórios privados por meio das
APIs REST do BitBucket, e será necessário criar um consumidor OAuth para usar o
driver; consulte a [documentação da Atlassian][37].
Será necessário preencher o URL da chamada de retorno com algo que satisfaça o
BitBucket, mas o endereço não precisa ir a lugar algum e não é usado pelo
Composer.

#### Autenticação com `bitbucket-oauth` na linha de comando

```shell
php composer.phar config [--global] bitbucket-oauth.bitbucket.org <consumer-key> <consumer-secret>
```

No comando acima, a chave de configuração `bitbucket-oauth.bitbucket.org` possui
duas partes:

- `bitbucket-oauth` é o método de autenticação.
- `bitbucket.org` é o nome do host ao qual este token se aplica.
  Não é necessário alterar isso, a menos que tenha uma instância privada.

#### Autenticação manual com `bitbucket-oauth`

```shell
php composer.phar config [--global] --editor --auth
```

```json
{
    "bitbucket-oauth": {
        "bitbucket.org": {
            "consumer-key": "<consumer-key>",
            "consumer-secret": "<consumer-secret>"
        }
    }
}
```

[1]: ../articles/handling-private-packages.md

[2]: #autenticacao-com-http-basic

[3]: #autenticacao-com-http-basic-em-linha

[4]: #autenticacao-com-http-bearer

[5]: #autenticacao-com-cabecalho-personalizado

[6]: #autenticacao-com-gitlab-oauth

[7]: #autenticacao-com-gitlab-token

[8]: #autenticacao-com-github-oauth

[9]: #autenticacao-com-bitbucket-oauth

[10]: ../03-cli.md#composer-home

[11]: #autenticacao-com-http-basic-na-linha-de-comando

[12]: #autenticacao-com-http-basic-em-linha-na-linha-de-comando

[13]: #autenticacao-com-http-bearer-na-linha-de-comando

[14]: #autenticacao-com-gitlab-oauth-na-linha-de-comando

[15]: #autenticacao-com-gitlab-token-na-linha-de-comando

[16]: #autenticacao-com-github-oauth-na-linha-de-comando

[17]: #autenticacao-com-bitbucket-oauth-na-linha-de-comando

[18]: #editando-as-credenciais-globais-na-linha-de-comando

[19]: #autenticacao-manual-com-http-basic

[20]: #autenticacao-manual-com-http-basic-em-linha

[21]: #autenticacao-manual-com-http-bearer

[22]: #autenticacao-manual-com-cabecalho-personalizado

[23]: #autenticacao-manual-com-gitlab-oauth

[24]: #autenticacao-manual-com-gitlab-token

[25]: #autenticacao-manual-com-github-oauth

[26]: #autenticacao-manual-com-bitbucket-oauth

[27]: ../03-cli.md#composer-auth

[28]: http://www.faqs.org/rfcs/rfc3986.html

[29]: ../06-config.md#gitlab-domains

[30]: https://gitlab.com/-/profile/personal_access_tokens

[31]: https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html#creating-a-personal-access-token

[32]: https://github.com/settings/tokens

[33]: https://github.com/settings/tokens/new

[34]: https://docs.github.com/en/developers/apps/building-oauth-apps/scopes-for-oauth-apps

[35]: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token

[36]: https://github.com/github/roadmap/issues/184

[37]: https://support.atlassian.com/bitbucket-cloud/docs/use-oauth-on-bitbucket-cloud/
