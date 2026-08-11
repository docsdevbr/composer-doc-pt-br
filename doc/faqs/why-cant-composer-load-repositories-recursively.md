---
# SPDX-FileCopyrightText: Nils Adermann, Jordi Boggiano.
#
# SPDX-License-Identifier: MIT
# Documentation licensed under the MIT License.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/docsdevbr/composer-docs-pt-br/blob/-/LICENSES/MIT.txt

source_url: https://github.com/composer/composer/blob/2.10.2/doc/faqs/why-cant-composer-load-repositories-recursively.md
source_revision: 7328f5e5db213cf6cd6860e9204a378636d612df
translation_status: ready
---

# Por que o Composer não consegue carregar repositórios recursivamente?

Você pode encontrar problemas ao usar repositórios personalizados, pois o
Composer não carrega os repositórios das suas dependências; assim, você precisa
redefinir esses repositórios em todos os seus arquivos `composer.json`.

Antes de entrar em detalhes sobre o motivo disso, é preciso entender que o uso
principal de repositórios VCS e de pacotes personalizados é testar algo
temporariamente ou usar um fork de um projeto até que seu pull request seja
integrado, etc.
Você não deve usá-los para gerenciar pacotes privados.
Para isso, é melhor considerar o [Private Packagist](https://packagist.com), que
permite configurar todos os seus pacotes privados em um único local e evita
a lentidão associada a repositórios VCS definidos diretamente no arquivo.

Existem três maneiras pelas quais o resolvedor de dependências poderia lidar com
repositórios personalizados:

- Buscar os repositórios do pacote raiz, obter todos os pacotes dos repositórios
  definidos e, então, resolver as dependências.
  Esse é o funcionamento atual e funciona bem, exceto pela limitação de não
  carregar repositórios recursivamente.

- Buscar os repositórios do pacote raiz e, ao inicializar pacotes dos
  repositórios definidos, inicializar recursivamente todos os repositórios
  encontrados nesses pacotes, e nos pacotes desses pacotes, etc., para então
  resolver as dependências.
  Isso poderia funcionar, mas torna a inicialização muito lenta, já que cada
  repositório VCS pode levar alguns segundos.
  Além disso, poderia resultar em um estado totalmente inconsistente, pois
  várias versões de um pacote poderiam definir os mesmos pacotes em um
  repositório de pacotes, mas com `dist`/`source` diferentes.
  Há muitas formas de isso dar errado.

- Buscar os repositórios do pacote raiz, depois buscar os repositórios das
  dependências de primeiro nível, depois buscar os repositórios das dependências
  delas, etc., e então resolver as dependências.
  Isso parece mais eficiente, mas apresenta os mesmos problemas da segunda
  solução, pois carregar os repositórios das dependências não é tão simples
  quanto parece.
  É necessário carregar todos os repositórios de todas as possíveis
  correspondências para uma dependência, o que, novamente, pode envolver
  definições de pacotes conflitantes.
