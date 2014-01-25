---
layout: post
title: "classes são objetos"
date: 2014-01-24 00:49
comments: true
categories: ruby
---
No último post falei sobre a construção de objetos em Ruby, nesse vamos falar sobre classes.  
Uma caracteristica das classes é que elas também são objetos. Então você pode definir uma classe
da forma mais comum.

```ruby
class MyClass < SuperClass
  def my_method
  end
end
```
ou pode criar uma instância da classe `[Class](http://www.ruby-doc.org/core-2.1.0/Class.html)`

```ruby
MyClass = Class.new(SuperClass) do
  def my_method
  end
end
```
As duas abordagens são válidas, a primeira é apenas um _syntax sugar_.  
Outra coisa interessante sobre classes é que [elas também são módulos](http://nomedojogo.com/2009/12/24/classes-sao-modulos-no-ruby/)
