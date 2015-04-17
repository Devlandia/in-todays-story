---
layout: post
title: "Getting Started with Angularjs"
date: 2015-04-17 08:00:00 -0700
comments: true
author: Vinícius Alonso
categories: [javascript, angularjs, frontend, spa]
---

_by [@viniciusalonso](https://github.com/viniciusalonso)_


> Esse post, tem como objetivo passar uma visão geral sobre o framework Angularjs. Conhecer suas principais características e funcionalidades. Em posts futuros pretendo entrar em detalhes sobre cada tópico aqui apresentado.

### Começando com Angularjs

#### Introdução

O Angularjs é um framework client-side criado pelo Google, utilizado para criar aplicações web dinâmicas. 
O Angularjs possui diversas vantagens, segunda a W3C, ele estende o HTML, é perfeito para aplicações SPA (Single Page Application) e também é facil de ser aprendido.

Nesse post vamos explorar suas funcionalidades mais básicas, que são:

* Expressions
* Directives  
* Filters 
* Modules
* Controllers

#### Expressions

É a forma como o Angularjs constrói dados no HTML. Para usar esse recurso é necessário usar a notação `{{ }}`. O resulta é semelhante ao uso da função javascript `eval()`.

Sendo assim, é possível exibir dados, concatenar strings, fazer contas, etc. Abaixo alguns exemploes de uso:

```html
<!DOCTYPE html>
<html>
  <head>
    <title> Example Angularjs</title>
    <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.3.14/angular.min.js"></script>
  </head>
  <body>

    <div ng-app="">
      {{ "Hello " + " World !!!" }}
    </div>

  </body>
</html>
```

Outros examplos:

```html
  <div ng-app="">
    <p>5 + 10 * 12 = {{ 5 + 10 * 12 }}</p>
  </div>
```

```html
  <div ng-app="" ng-init="divisor=5; dividend=200;">
    <p>200 / 5 = {{ 200 / 5 }}</p>
  </div>
```