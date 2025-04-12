---
layout: post
title: "Cuidado ao chamar métodos da super classe"
date: 2014-01-20 19:59
lang: pt-BR
---
Lendo um pouco sobre herança em Ruby descobri uma coisa interessante, ao usar o método `super` em algum método, pode ocorrer da erro caso o desenvolvedor não preste atenção. Quando você redefine um método e chama `super` sem parênteses e sem argumentos, o Ruby irá pegar os parâmetros passados para o método na subclasse passar de forma implícita para o método da superclasse, se os parâmetros do método na superclasse forem diferentes dos parâmetros no método redefinido irá resultar em erro.
Exemplo:

```ruby
class Dad
  def hi
    puts "Hello "
  end
end

Class Child < Dad
  def hi(name)
    super
    puts name
  end
end

Child.new('alessandro').hi

=> ArgumentError: wrong number of arguments (1 for 0)
```

Como resolver isso? Simples.
Caso o método da superclasse não receba parâmetros chame o método super com parênteses `super()`
Exemplo:

```ruby
class Dad
  def hi
    puts "Hello "
  end
end

Class Child < Dad
  def hi(name)
    super()
    puts name
  end
end

Child.new('alessandro').hi
=> Hello
=> alessandro
```
