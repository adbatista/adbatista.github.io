---
layout: post
title: "construtores em ruby"
date: 2014-01-23 15:53
lang: pt-BR
---
Você ja deve ter percebido que diferente do C#, Java e outras linguagens OO, em Ruby a criação de um novo objeto é feita pelo método `new`, mas como é feita a criação de um objeto em Ruby?
Primeiro faremos uma lista do que precisamos para para instanciar um novo objeto.

1.  Alocar espaço para um objeto vazio
2.  Setar o estado inicial do objeto
3.  Retornar a referência para o objeto alocado

Após feita a lista vamos criar um objeto com um construtor personalizado com cada item da lista

```ruby
class Book
  def self.custom_new(title, author)
    instance = allocate # Aloca espaço para um novo objeto vazio
    instance.custom_initialize(title, author)
    instance # Retorna a referência do objeto
  end

  def custom_initialize(title, author)
    @title, @author = title, author
  end
end

Book.custom_new('Title','Alessandro')
 => #<Book:0x00000003607978 @author="ale", @title="Title">
```
No caso acima criamos uma classe `Book` que possui um construtor personalizado, claro que poderíamos sobrescrever o método `new` e/ou `initialize`, mas fiz dessa forma apena para mostrar que é possivel fazer assim.

Caso queira saber sobre o método `allocate` aqui está a [documentação](http://www.ruby-doc.org/core-2.1.0/Class.html#method-i-allocate)
