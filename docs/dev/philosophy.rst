Filosofia de desenvolvimento
============================

Requests é uma biblioteca aberta porém teimosa, criado por um desenvolvedor aberto porém teimoso.


Ditador Benevolente
~~~~~~~~~~~~~~~~~~~

`Kenneth Reitz <http://kennethreitz.org>`_ é o BDFL (Ditador Benevolente para a Vida). Ele tem a palavra
final em qualquer discussão relacionada ao Requests.

Valores
~~~~~~~

- Simplicidade é sempre melhor que funcionalidade.
- Ouça a todos, então, ignore.
- A API é tudo o que importa. Todo o restante é secundário.
- Satisfaça 90% dos casos de uso. Ignore os opositores.

Versionamento semântico
~~~~~~~~~~~~~~~~~~~~~~~

Durante muitos anos, a comunidade open source tem sofrido com distonia dos números das versões. Números variam tanto de projeto para projeto, que são praticamente sem significado.

Requests usa `Versionamento Semântico <http://semver.org>`_. Esta especificação procura por um fim nesta confusão com um pequeno número de sugestões práticas para você e seus colegas usaremos no seu próximo projeto.

Biblioteca padrão?
~~~~~~~~~~~~~~~~~~

Requests não tem planos *ativos* de ser incluída na biblioteca padrão. Esta discussão tem sido longamente discutida com Guido assim como vários desenvolvedores do núcleo do Python.

Essencialmente, a biblioteca padrão é onde uma biblioteca vai para morrer. É apropriado para um módulo ser incluído quando não há mais necessidade de desenvolvimento ativo.

Requests acabou de chegar na versão v1.0.0. Este grande marco representa um grande passo na direção correta.

Pacotes de distribuições Linux
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Pacotes têm sido feitos para várias distribuições Linux, incluindo: Ubuntu, Debian, RHEL e Arch.

Às vezes, estes pacotes são forks divergentes, ou não são mantidos atualizados com o código e correções mais atuais. PyPI (e seus mirrors) e o GitHub são as fontes oficiais de distribuição; alternativas não são suportadas pelo projeto requests.
