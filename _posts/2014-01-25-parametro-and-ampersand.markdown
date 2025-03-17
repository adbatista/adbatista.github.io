---
layout: post
title: "o parâmetro '&' (Ampersand)"
date: 2014-01-25 17:10
comments: true
categories: [ruby, ampersand]
---
Para que serve o &amp; antes de um parâmetro em Ruby?

O &amp; antes de algum parâmetro é utilizado basicamente de duas formas:

1 Na definição de um método:

```ruby
def awesome_method &block
  yield if block_given?
end
```

2 Antes da chamada de um método:
```ruby
  awesome_method &parameter
```

A primeira forma serve para deixar explícito que o seu método recebe um bloco.
Já a segunda forma funciona assim:
pega o parâmetro, chama o método `to_proc` e o passa como bloco, idependente de o método ter deixado o bloco explícito ou não. Então basta que o objeto passado responda a `to_proc`. Você ja deve ter visto parecido com isso:

```ruby
  @users.map &:name
```
E uma coisa que sempre quis saber é "como um símbolo passado como parâmetro irá no exemplo acima irá retornar o nome de todos usuários?".
Pesquisei um pouco, <a href="http://stackoverflow.com/questions/1217088/what-does-mapname-mean-in-ruby#answer-1217114" target="_blank">e achei como isso é feito</a>
Símbolos implementam o `to_proc`, tá e dai, como funciona esse to_proc`?

```ruby
  class Symbol
    # ...
    def to_proc
      Proc.new do |obj, *args|
        obj.send self, *args
      end
    end
    # ...
```
Ainda um pouco confuso? Vamos destrinchar esse código então.
vamos chamar o `to_proc` para o símbolo `:name`
```ruby
  #...
  Proc.new do |obj, *args|
    obj.send :name, *args
  end
  #...
```
o `map` passa como parâmetro para o bloco um item da coleção, nesse caso um user, vamos adicioná-lo no código, e já que apenas um argumento é passado podemos remover o `*args` para simplificar.

```ruby
  #...
  Proc.new do |user|
    user.send :name # => equivalente a user.name
  end
  #...
```
Mais simples agora? O método `send` envia para o user a mensagem `:name`, porque na verdade em Ruby metódos não são chamados, são mensagens enviadas ao objeto.
o resultado final seria algo equivalente a isso:
```ruby
  @users.map do |user|
    user.send :name # ou user.name
  end
```

Caso queira saber mais sobre o ampersand: <a href="http://www.skorks.com/2013/04/ruby-ampersand-parameter-demystified/">Ruby ampersand parameter demystified</a>
