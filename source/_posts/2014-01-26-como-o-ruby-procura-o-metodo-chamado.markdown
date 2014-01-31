---
layout: post
title: "como o ruby procura o método chamado?"
date: 2014-01-26 16:56
comments: true
categories: 
published: false
---
Ja ficou curioso sobre como o ruby faz para encontrar um método chamado? Se ele busca na superclasse ou se procura no módulos primeiro e em que ordem ele busca nos módulos?  
Pois então, quando você envia uma mensagem para um objeto (chama um método), o Ruby procura esse método no objeto, caso não encontre ele irá verificar na <a href="http://simplesideias.com.br/ruby-object-model-singleton-class" target="_blank">singleton class</a> nos módulos incluidos começando pelo último incluido até o primeiro, e caso não encontre, irá para a superclasse e fazer a mesma coisa (classe, modulos,superclasse), até chegar na classe BasicObject, que é a classe no topo da hieraquia, e se método não for encontrado, o Ruby chama o método `method_missing` que lança um exceção `NoMethodError`