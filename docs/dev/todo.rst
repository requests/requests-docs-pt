Como Ajudar
===========

Requests está sob desenvolvimento ativo e contribuições são mais que bem-vindas!

#. Procure por issues abertas ou abra uma nova issue para começar uma discussão sobre um bug.
   Existe um tag Contributor Friendly para issues que são ideais para pessoas que não estão muito
   familiarizadas com a base de código ainda.
#. Faça um fork do `repositório <https://github.com/kennethreitz/requests>`_ no GitHub e comece
   a fazer suas mudanças em uma nova branch.
#. Escreva um teste que mostra que seu bug foi corrigido.
#. Crie um pull request e avise o mantenedor até que seja aceito e publicado. :)
   Certifique-se de se adicionar no arquivo `AUTHORS <https://github.com/kennethreitz/requests/blob/master/AUTHORS.rst>`_.

Congelamento de funcionalidades
-------------------------------

A partir da versão v1.0.0., Requests entrou num período de congelamento de funcionalidades.
Pedidos de novas funcionalidades e Pull Requests implementando-as não serão aceitos.

Dependências de desenvolvimento
-------------------------------

Você vai precisar instalar py.test para rodar a suíte de tests de Requests::

    $ pip install -r requirements.txt
    $ invoke test
    py.test
    platform darwin -- Python 2.7.3 -- pytest-2.3.4
    collected 25 items

    test_requests.py .........................
    25 passed in 3.50 seconds

Ambientes de execução
---------------------

Requests atualmente suporta as seguintes versões do Python:

- Python 2.6
- Python 2.7
- Python 3.1
- Python 3.2
- Python 3.3
- PyPy 1.9

Suporte para Python 3.1 e 3.2 pode ser descontinuado a qualquer momento.

Google App Engine nunca será oficialmente suportado. Pull requests para compatibilidade serão aceitos, desde que eles não compliquem a base código.


Você está louco?
----------------

- Suporte a SPDY seria fantástico. Sem extensões em C.
