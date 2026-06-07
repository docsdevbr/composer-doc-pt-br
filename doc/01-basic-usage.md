<!--
[//]: # Copyright (c) Nils Adermann, Jordi Boggiano.
#
# SPDX-License-Identifier: MIT
# Documentation licensed under the MIT License.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/composer-doc-pt-br/blob/-/LICENSES/MIT.txt

source_url: https://github.com/composer/composer/blob/2.8/doc/01-basic-usage.md
revision: 5bc5c174a68a98fa3779ee4ab8f9c85f64d6b78c
status: ready
-->

# Uso básico

## Introdução

Para a nossa introdução ao uso básico, instalaremos o `monolog/monolog`, uma
biblioteca de log.
Se você ainda não instalou o Composer, consulte o capítulo [Introdução][1].

> **Nota:** para simplificar, esta introdução pressupõe que você tenha feito uma
> instalação [local][2] do Composer.

## `composer.json`: configuração do projeto {: #composer-json-configuracao-do-projeto }

Para começar a usar o Composer no seu projeto, tudo o que você precisa é de um
arquivo `composer.json`.
Este arquivo descreve as dependências do seu projeto e também pode conter outros
metadados.
Normalmente, ele deve ficar no diretório mais alto do seu projeto/repositório
VCS.
Tecnicamente, você pode executar o Composer de qualquer lugar, mas se quiser
publicar um pacote no Packagist.org, ele terá que encontrar o arquivo no topo do
seu repositório VCS.

### A chave `require`

A primeira coisa que você especifica no `composer.json` é a chave
[`require`][3].
Você está dizendo ao Composer de quais pacotes seu projeto depende.

```json
{
    "require": {
        "monolog/monolog": "2.0.*"
    }
}
```

Como você pode notar, [`require`][3] recebe um objeto que mapeia **nomes de
pacotes** (por exemplo, `monolog/monolog`) para **restrições de versão** (por
exemplo, `2.0.*`).

O Composer usa essas informações para procurar o conjunto certo de arquivos nos
"repositórios" de pacotes que você registra usando a chave [`repositories`][4],
ou no [Packagist.org][5], o repositório padrão de pacotes.
No exemplo acima, como nenhum outro repositório foi registrado no arquivo
`composer.json`, presume-se que o pacote `monolog/monolog` esteja registrado no
Packagist.org.
(Leia mais sobre o [Packagist][6] e sobre [repositórios][7]).

### Nomes de pacotes

O nome do pacote consiste no nome do fornecedor e no nome do projeto.
Geralmente, eles serão idênticos — o nome do fornecedor existe apenas para
evitar conflitos de nomes.
Por exemplo, isso permitiria que duas pessoas diferentes criassem uma biblioteca
chamada `json`.
Uma pode ser chamada `igorw/json` enquanto a outra pode ser `seldaek/json`.

Leia mais sobre [publicação e nomenclatura de pacotes][8].
(Observe que você também pode especificar "pacotes de plataforma" como
dependências, permitindo que você exija determinadas versões de programas do
servidor.
Consulte [pacotes de plataforma][9] abaixo.)

### Restrições de versão do pacote

No nosso exemplo, estamos solicitando o pacote Monolog com a restrição de versão
[`2.0.*`][10].
Isso significa qualquer versão no branch de desenvolvimento `2.0`, ou qualquer
versão maior ou igual a `2.0` e menor que `2.1` (`>=2.0 <2.1`).

Leia o [artigo sobre versões][11] para obter informações mais detalhadas sobre
versões, como as versões se relacionam entre si e sobre restrições de versão.

> **Como o Composer baixa os arquivos certos?**
> Quando você especifica uma dependência no `composer.json`, o Composer primeiro
> pega o nome do pacote que você solicitou e o procura em todos os repositórios
> que você registrou usando a chave [`repositories`][4].
> Se você não registrou nenhum repositório extra, ou se ele não encontrar um
> pacote com esse nome nos repositórios que você especificou, ele recorre ao
> Packagist (mais [abaixo][6]).
>
> Quando o Composer encontra o pacote certo, seja no Packagist.org ou em um
> repositório que você especificou, ele usa os recursos de versionamento do VCS
> do pacote (por exemplo, branches e tags) para tentar encontrar a melhor
> correspondência para a restrição de versão que você especificou.
> Leia sobre versões e resolução de pacotes no [artigo sobre versões][11].

> **Nota:** Se você estiver tentando exigir um pacote, mas o Composer lançar um
> erro sobre a estabilidade do pacote, a versão que você especificou pode não
> atender aos seus requisitos mínimos de estabilidade padrão.
> Por padrão, apenas versões estáveis são levadas em consideração ao pesquisar
> versões de pacotes válidas no seu VCS.
>
> Você pode se deparar com essa situação se estiver tentando exigir versões
> `dev`, `alpha`, `beta` ou `RC` de um pacote.
> Leia mais sobre sinalizadores de estabilidade e a chave `minimum-stability` na
> [página do esquema][12].

## Instalando dependências

Para instalar inicialmente as dependências definidas no projeto, execute o
comando [`update`][13]:

```shell
php composer.phar update
```

Isso fará com que o Composer faça duas coisas:

- Ele resolve todas as dependências listadas no seu arquivo `composer.json` e
  grava todos os pacotes e suas versões exatas no arquivo `composer.lock`,
  travando o projeto nessas versões específicas.
  Você deve enviar o arquivo `composer.lock` para o repositório do seu projeto
  para que todas as pessoas que trabalham no projeto usem as mesmas versões
  travadas das dependências (mais abaixo).
  Esta é a função principal do comando `update`.
- Ele então executa implicitamente o comando [`install`][14].
  Isso baixará os arquivos das dependências no diretório `vendor` do seu
  projeto.
  (O diretório `vendor` é o local convencional para todo o código de terceiros
  em um projeto).
  No nosso exemplo acima, você terminaria com os arquivos-fonte do Monolog em
  `vendor/monolog/monolog/`.
  Como o Monolog depende do pacote `psr/log`, os arquivos desse pacote também
  poderiam ser encontrados no diretório `vendor`.

> **Dica:** Se você estiver usando o git no projeto, provavelmente desejará
> adicionar o diretório `vendor` ao seu `.gitignore`.
> Afinal, você não quer adicionar todo esse código de terceiros ao repositório
> versionado.

### Envie o arquivo `composer.lock` para o controle de versão {: #envie-o-arquivo-composer-lock-para-o-controle-de-versao }

É importante enviar esse arquivo para o controle de versão porque fará com que
qualquer pessoa que configurar o projeto use as mesmas versões das dependências
que você está usando.
O servidor de integração contínua, máquinas de produção, outras pessoas no time,
tudo e todas as pessoas usarão as mesmas dependências, reduzindo o potencial de
erros que afetam apenas algumas partes das implantações.
Mesmo se o projeto for desenvolvido por apenas uma pessoa, em seis meses, ao
reinstalar o projeto, você pode ter certeza de que as dependências instaladas
ainda estão funcionando, mesmo que tenham sido lançadas muitas versões novas
dessas dependências desde então.
(Veja a nota abaixo sobre o uso do comando `update`.)

> **Nota:** Para bibliotecas, não é necessário enviar o arquivo de travamento;
> consulte também: [Bibliotecas - Arquivo de travamento][15].

### Instalando a partir do `composer.lock` {: #instalando-a-partir-do-composer-lock }

Se houver um arquivo `composer.lock` na pasta do projeto, significa que você
executou o comando `install` antes ou outra pessoa no projeto executou o comando
`update` e enviou o arquivo `composer.lock` para o projeto (o que é bom).

De qualquer forma, executar `install` quando um arquivo `composer.lock` está
presente resolve e instala todas as dependências que você listou no
`composer.json`, mas o Composer usa as versões exatas listadas no
`composer.lock` para garantir que as versões dos pacotes sejam consistentes para
todas as pessoas que trabalham no seu projeto.
Como resultado, você terá todas as dependências solicitadas pelo seu arquivo
`composer.json`, mas elas podem não estar nas versões mais recentes disponíveis
(algumas das dependências listadas no arquivo `composer.lock` podem ter lançado
versões mais recentes desde que o arquivo foi criado).
Isso é intencional e garante que seu projeto não quebre devido a alterações
inesperadas nas dependências.

Portanto, depois de buscar novas alterações no seu repositório VCS, é
recomendado executar o comando `install` para garantir que o diretório `vendor`
esteja sincronizado com seu arquivo `composer.lock`:

```shell
php composer.phar install
```

O Composer permite compilações reproduzíveis por padrão.
Isso significa que executar o mesmo comando várias vezes produzirá um diretório
`vendor` contendo arquivos idênticos (exceto por suas datas e horas), incluindo
os arquivos do carregador automático.
Isso é especialmente benéfico para ambientes que exigem processos de verificação
rigorosos, bem como para distribuições Linux que visam empacotar aplicações PHP
de forma segura e previsível.

## Atualizando as dependências para suas versões mais recentes

Como mencionado acima, o arquivo `composer.lock` impede que você obtenha
automaticamente as versões mais recentes das suas dependências.
Para atualizar para as versões mais recentes, use o comando [`update`][13].
Ele buscará as versões correspondentes mais recentes (de acordo com seu arquivo
`composer.json`) e atualizará o arquivo de travamento com as novas versões.

```shell
php composer.phar update
```

> **Nota:** O Composer exibirá um alerta ao executar um comando `install` se o
> `composer.lock` não tiver sido atualizado desde que foram feitas alterações
> no `composer.json` que podem afetar a resolução de dependências.

Se você quiser instalar, atualizar ou remover apenas uma dependência, você pode
listá-la explicitamente como um argumento:

```shell
php composer.phar update monolog/monolog [...]
```

## Packagist

[Packagist.org][5] é o principal repositório do Composer.
Um repositório do Composer é basicamente uma fonte de pacotes: um lugar de onde
você pode obter pacotes.
O Packagist pretende ser o repositório central que todas as pessoas usam.
Isso significa que é possível solicitar automaticamente qualquer pacote
disponível lá usando `require`, sem especificar mais detalhes sobre onde o
Composer deve procurar o pacote.

Se você for ao site [Packagist.org][5], poderá navegar e procurar por pacotes.

É recomendado que qualquer projeto de código aberto usando o Composer publique
seus pacotes no Packagist.
Uma biblioteca não precisa estar no Packagist para ser usada pelo Composer, mas
estar lá permite a descoberta e adoção mais rápida por outras pessoas.

## Pacotes de plataforma

O Composer possui pacotes de plataforma, que são pacotes virtuais para coisas
que estão instaladas no sistema, mas que não podem ser instaladas pelo Composer.
Isso inclui o próprio PHP, extensões PHP e algumas bibliotecas do sistema.

* `php` representa a versão do PHP da pessoa usuária, permitindo aplicar
  restrições, por exemplo, `^7.1`.
  Para exigir uma versão de 64 bits do PHP, você pode exigir o pacote
  `php-64bit`.

* `hhvm` representa a versão do tempo de execução HHVM e permite que você
  aplique uma restrição, por exemplo, `^2.3`.

* `ext-<nome>` permite que você exija extensões PHP (incluindo extensões
  nativas).
  O versionamento pode ser bastante inconsistente aqui, então geralmente é uma
  boa ideia definir a restrição como `*`.
  Um exemplo de nome de pacote de extensão é `ext-gd`.

* `lib-<nome>` permite que restrições sejam feitas nas versões das bibliotecas
  usadas pelo PHP.
  As seguintes estão disponíveis: `curl`, `iconv`, `icu`, `libxml`, `openssl`,
  `pcre`, `uuid`, `xsl`.

É possível usar [`show --platform`][16] para obter uma lista dos pacotes de
plataforma disponíveis localmente.

## Carregamento automático

Para bibliotecas que especificam informações de carregamento automático, o
Composer gera um arquivo `vendor/autoload.php`.
Você pode incluir esse arquivo e começar a usar as classes que essas bibliotecas
fornecem sem nenhum trabalho extra:

```php
require __DIR__ . '/vendor/autoload.php';

$log = new Monolog\Logger('nome');
$log->pushHandler(new Monolog\Handler\StreamHandler('app.log', Monolog\Logger::WARNING));
$log->warning('Foo');
```

Você pode até adicionar seu próprio código ao carregador automático, adicionando
um campo [`autoload`][17] ao `composer.json`.

```json
{
    "autoload": {
        "psr-4": {
            "Acme\\": "src/"
        }
    }
}
```

O Composer registrará um carregador automático [PSR-4][18] para o namespace
`Acme`.

Nesse caso, você definiu um mapeamento de namespaces para diretórios.
O diretório `src` estaria na raiz do seu projeto, no mesmo nível do diretório
`vendor`.
Um exemplo de nome de arquivo seria `src/Foo.php` contendo uma classe
`Acme\Foo`.

Depois de adicionar o campo [`autoload`][17], você precisa executar novamente
este comando:

```shell
php composer.phar dump-autoload
```

Esse comando gerará novamente o arquivo `vendor/autoload.php`.
Consulte a seção [`dump-autoload`][19] para mais informações.

Incluir esse arquivo também retornará a instância do carregador automático,
para que você possa armazenar o valor de retorno da chamada de inclusão em uma
variável e então adicionar mais namespaces.
Isso pode ser útil para fazer o carregamento automático de classes numa suíte de
testes, por exemplo.

```php
$loader = require __DIR__ . '/vendor/autoload.php';
$loader->addPsr4('Acme\\Test\\', __DIR__);
```

Além do carregamento automático PSR-4, o Composer também suporta PSR-0, mapas de
classes e o carregamento automático de arquivos.
Consulte a referência de [`autoload`][17] para obter mais informações.

Consulte também a documentação sobre [otimização do carregador automático][20].

> **Nota:** O Composer fornece seu próprio carregador automático.
> Se você não quiser usá-lo, pode incluir os arquivos
> `vendor/composer/autoload_*.php`, que retornam arrays associativos que
> permitem que você configure seu próprio carregador automático.

[1]: 00-intro.md

[2]: 00-intro.md#localmente

[3]: 04-schema.md#require

[4]: 04-schema.md#repositories

[5]: https://packagist.org/

[6]: #packagist

[7]: 05-repositories.md

[8]: 02-libraries.md

[9]: #pacotes-de-plataforma

[10]: https://semver.madewithlove.com/?package=monolog%2Fmonolog&constraint=2.0.*

[11]: articles/versions.md

[12]: 04-schema.md

[13]: 03-cli.md#update-u

[14]: 03-cli.md#install-i

[15]: 02-libraries.md#arquivo-de-travamento

[16]: 03-cli.md#show

[17]: 04-schema.md#autoload

[18]: https://www.php-fig.org/psr/psr-4/

[19]: 03-cli.md#dump-autoload-dumpautoload

[20]: articles/autoloader-optimization.md
