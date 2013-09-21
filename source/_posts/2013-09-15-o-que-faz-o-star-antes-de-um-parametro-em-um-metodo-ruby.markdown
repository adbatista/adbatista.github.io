---
layout: post
title: "O que faz o \"*\" antes de um parâmetro em um método ruby?"
date: 2013-09-15 13:25
comments: true
categories: 
---
Esses dias olhando um código ruby na internet vi um trecho como esse: `def example(*b)`
O que esse código faz não vem ao caso, mas olhei o `*b` e pensei, oque que é esse * antes da variavel me veio na hora ponteiro, mas ruby não possui ponteiros, então oque `*b` faz?
Resumindo é a sintaxe para vArgs em ruby, ex:

```ruby
    def example(*b)
      puts b
    end

    example 1,2,3,4 # => 1 2 3 4
    arr = [5,6,7,8]

    example *arr # => 5 6 7 8
```

na ultima linha o `*arr` faz com que cada item do array seja passado como parâmetro para o método `example`, você pode usar dessa forma também.

```ruby
    def example2(v1,v2,3)
      puts v1,v2,v3
    end

    arr = [2, 3, 1]
    example2 *arr # => 2 3 1
```
Nessa segunda abordagem você de ter cuidado para que a quantidade de elementos no array seja igual a quantidade de parâmetros requeridos pelo método.

Outra forma válida é o próximo exemplo:

```ruby
    def example3(v1,*v2)
      puts v2
    end

    arr = [2, 3, 1]

    example3 *arr #=> 3 1
```

Nesse caso o primeiro valor da variavel `arr` é atribuido ao primeiro parâmetro e os demais ao parâmetro v2.
