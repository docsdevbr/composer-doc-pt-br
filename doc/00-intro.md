<!--
source_url: https://github.com/composer/composer/blob/-/doc/00-intro.md
revision: 69746f699f01f7b33d411cd4ddceeeb3e26b5139
status: ready
-->

# Introdução

O Composer é uma ferramenta para gerenciamento de dependências em PHP.
Ele permite que as bibliotecas das quais o projeto depende sejam declaradas e
gerenciadas (instaladas/atualizadas).

## Gerenciamento de dependências

O Composer **não** é um gerenciador de pacotes no mesmo sentido que o Yum ou
Apt.
Sim, ele lida com "pacotes" ou bibliotecas, mas os gerencia separadamente
por projeto, instalando-os em um diretório (por exemplo, `vendor`) dentro do seu
projeto.
Por padrão, ele não instala nada globalmente.
Portanto, ele é um gerenciador de dependências.
No entanto, ele suporta um projeto "global" por conveniência, através do comando
[global][1].

Essa ideia não é nova e o Composer é fortemente inspirado pelo [npm][2] do node
e pelo [bundler][3] do ruby.

Suponha que:

1. Você tem um projeto que depende de várias bibliotecas.
2. Algumas dessas bibliotecas dependem de outras bibliotecas.

O Composer:

1. Permite que você declare as bibliotecas das quais depende.
2. Descobre quais versões de quais pacotes podem e precisam ser instaladas e as
   instala (o que significa que ele as baixa para seu projeto).
5. Você pode atualizar todas as suas dependências em um comando.

Consulte o capítulo [Uso básico][4] mais detalhes sobre a declaração de
dependências.

## Requisitos de sistema

O Composer em sua versão mais recente requer o PHP 7.2.5 para ser executado.
Uma versão de suporte de longo prazo (2.2.x) ainda oferece suporte para PHP
5.3.2+, caso seja necessário usar uma versão legada do PHP.
Algumas configurações sensíveis e sinalizadores de compilação do PHP também são
necessários, mas ao usar o instalador, será possível saber de quaisquer
incompatibilidades.

O Composer precisa de várias aplicações de suporte para funcionar efetivamente,
tornando o processo de tratamento de dependências de pacotes mais eficiente.
Para descompactar arquivos, o Composer conta com ferramentas como `7z` (ou
`7zz`), `gzip`, `tar`, `unrar`, `unzip` e `xz`.
Quanto aos sistemas de controle de versão, o Composer integra-se perfeitamente
com Fossil, Git, Mercurial, Perforce e Subversion, garantindo assim o bom
funcionamento da aplicação e o gerenciamento dos repositórios de bibliotecas.
Antes de usar o Composer, certifique-se de que essas dependências estejam
instaladas corretamente no seu sistema.

O Composer é multiplataforma e nos esforçamos para fazê-lo funcionar igualmente
bem no Windows, Linux e macOS.

## Instalação - Linux / Unix / macOS

### Baixando o executável do Composer

O Composer oferece um instalador conveniente que pode ser executado diretamente
da linha de comando.
Sinta-se à vontade para [baixar este arquivo][5] ou revisá-lo no [GitHub][6], se
desejar saber mais sobre o funcionamento interno do instalador.
O código-fonte é PHP puro.

Em resumo, existem duas formas de instalar o Composer.
Localmente como parte do seu projeto, ou globalmente como um executável
disponível em todo o sistema.

#### Localmente

Para instalar o Composer localmente, execute o instalador no diretório do seu
projeto.
Consulte [a página de download][7] para obter instruções.

O instalador verificará algumas configurações do PHP e baixará o `composer.phar`
no diretório atual.
Este arquivo é o binário do Composer.
Ele é um PHAR (PHP Archive), que é um formato de arquivo para PHP que pode ser
executado na linha de comando, entre outras coisas.

Agora execute `php composer.phar` para executar o Composer.

Você pode instalar o Composer em um diretório específico usando a opção
`--install-dir` e, adicionalmente, também pode ser renomeado usando a opção
`--filename`.
Ao executar o instalador seguindo [as instruções da página de download][7],
adicione os seguintes parâmetros:

```shell
php composer-setup.php --install-dir=bin --filename=composer
```

Agora execute `php bin/composer` para executar o Composer.

#### Globalmente

Você pode colocar o PHAR do Composer em qualquer lugar que desejar.
Se você colocá-lo em um diretório que faça parte da variável de ambiente `PATH`,
poderá acessá-lo globalmente.
Nos sistemas Unix, você pode até mesmo torná-lo executável e invocá-lo sem usar
diretamente o interpretador `php`.

Depois de executar o instalador seguindo
[as instruções da página de download][7],
este comando pode ser executado para mover o `composer.phar` para um diretório
que esteja na variável `PATH`:

```shell
mv composer.phar /usr/local/bin/composer
```

Se você quiser instalá-lo apenas para seu usuário e evitar a necessidade de
permissões de administrador, use `~/.local/bin`, que está disponível por padrão
em algumas distribuições Linux.

> **Nota:** Se o comando acima falhar devido a permissões, você pode precisar
> executá-lo novamente com `sudo`.

> **Nota:** Em algumas versões do macOS, o diretório `/usr` não existe por
> padrão.
> Se ocorrer o erro `/usr/local/bin/composer: No such file or directory`, o
> diretório deverá ser criado manualmente antes de continuar:
> `mkdir -p /usr/local/bin`.

> **Nota:** Para obter informações sobre como alterar a variável `PATH`, leia o
> [artigo da Wikipedia][8] ou use um mecanismo de busca.

Agora execute `composer` para executar o Composer em vez de `php composer.phar`.

## Instalação - Windows

### Usando o instalador

Esta é a maneira mais fácil de configurar o Composer na sua máquina.

Baixe e execute o binário [Composer-Setup.exe][9].
Ele instalará a versão mais recente do Composer e configurará a variável `PATH`
para que o `composer` possa ser executado de qualquer diretório na linha de
comando.

> **Nota:** Feche o terminal atual.
> Teste o uso em um novo terminal: isso é importante, pois a variável `PATH` só
> é carregada quando o terminal é iniciado.

### Instalação Manual

Mude para um diretório que esteja na variável `PATH` e execute o instalador
seguindo [as instruções da página de download][7] para baixar o `composer.phar`.

Crie um novo arquivo `composer.bat` junto ao `composer.phar`:

Usando `cmd.exe`:

```shell
C:\bin> echo @php "%~dp0composer.phar" %*>composer.bat
```

Usando PowerShell:

```shell
PS C:\bin> Set-Content composer.bat '@php "%~dp0composer.phar" %*'
```

Adicione o diretório à variável de ambiente `PATH`, se ainda não tiver
adicionado.
Para obter informações sobre como alterar a variável `PATH`, consulte [este
artigo][10] ou use um mecanismo de busca.

Feche o terminal atual.
Teste o uso em um novo terminal:

```shell
C:\Users\username>composer -V
```

```text
Composer version 2.4.0 2022-08-16 16:10:48
```

## Imagem do Docker

O Composer é publicado como imagem do Docker em alguns lugares, veja a lista no
[README do composer/docker][11].

Exemplo de uso:

```shell
docker pull composer/composer
docker run --rm -it -v "$(pwd):/app" composer/composer install
```

Para adicionar o Composer a um **Dockerfile** existente, você pode simplesmente
copiar o arquivo binário de imagens pré-construídas de tamanho reduzido:

```Dockerfile
# Última versão
COPY --from=composer/composer:latest-bin /composer /usr/bin/composer

# Versão específica
COPY --from=composer/composer:2-bin /composer /usr/bin/composer
```

Leia a [descrição da imagem][12] para obter mais informações de uso.

> **Nota:** Problemas específicos do Docker devem ser registrados
> [no repositório composer/docker][13].

> **Nota:** Você também pode usar `composer` em vez de `composer/composer` como
> nome da imagem acima.
> É mais curto e é uma imagem oficial do Docker, mas não é publicada diretamente
> por nós e, por isso, costuma receber novos lançamentos com atraso de alguns
> dias.

> **Importante**: Imagens com apelidos curtos não têm equivalentes apenas com
> binários, então para a abordagem `COPY --from` é melhor usar
> `composer/composer`.

## Usando o Composer

Agora que você instalou o Composer, já pode usá-lo!
Vá para o próximo capítulo para uma breve demonstração.

[1]: 03-cli.md#global

[2]: https://www.npmjs.com/

[3]: https://bundler.io/

[4]: 01-basic-usage.md

[5]: https://getcomposer.org/installer

[6]: https://github.com/composer/getcomposer.org/blob/main/web/installer

[7]: https://getcomposer.org/download/

[8]: https://en.wikipedia.org/wiki/PATH_(variable)

[9]: https://getcomposer.org/Composer-Setup.exe

[10]: https://www.computerhope.com/issues/ch000549.htm

[11]: https://github.com/composer/docker

[12]: https://hub.docker.com/r/composer/composer

[13]: https://github.com/composer/docker/issues
