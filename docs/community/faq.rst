.. _faq:

FAQ
==========================

Esta parte da documentação responde as perguntas mais frequentes sobre Requests.

Dados codificados?
-------------

Requests descomprime automaticamente as respostas comprimidas com gzip e faz o possível
para decodificar o conteúdo para unicode quando possível.

Você tem acesso direto à resposta crua (e até mesmo ao socket), se for necessário.


User-Agents personalizados?
-------------------

Requests permite que você facilmente sobrescreva informações de User-Agent, além de qualquer
outro cabeçalho HTTP.


Por que não Httplib2?
-----------------

Chris Adams deu uma excelente explicação em
`Hacker News <http://news.ycombinator.com/item?id=2884406>`_:

    httplib2 é parte do motivo por que você deveria usar requests: é bem mais respeitável
    como um cliente, mas não é tão bem documentado e ainda precisa de muito código para relizar
    operações básicas. Eu reconheço o que httplib2 está tentando fazer, no sentido que há muitas
    complicações de baixo nível em construir um cliente HTTP moderno, porém, use requests.
    Kenneth Reitz é bastante motivado e ele entende o conceito de que coisas simples devem ser simples
    enquanto httplib2 parece muito mais um exercício acadêmico do que algo que as pessoas
    deveriam usar para criar sistemas em produção[1].

    Confissão: Eu apareço no arquivo AUTHORS do requests mas eu só tomo crédito por
    mais ou menos 0,0001% do quão sensacional ele é.

    1. http://code.google.com/p/httplib2/issues/detail?id=96 é um bom exemplo:
    um bug que afeta muitas pessoas, havia uma correção disponível há meses, que
    funcionava muito bem quando eu o aplicava em um fork e passava TBs de dados por ele,
    mas demorou mais de 1 ano para ser aplicado ao código e ainda mais tempo para
    estar disponível pelo PyPI, onde qualquer outro projeto que depende de "httplib2"
    conseguiria a versão funcional.


Suporte ao Python 3?
-----------------

Sim! Aqui está uma lista de plataformas do Python que
são suportadas oficialmente:

* Python 2.6
* Python 2.7
* Python 3.1
* Python 3.2
* Python 3.3
* PyPy 1.9
