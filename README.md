# S.O.L.I.D. Exemplos

* [**S**ingle Responsibility Principle](#single-responsibility-principle)
  * [SRP aplicado no código](solid-examples/SRP)
* [**O**pen/Closed Principle](#open-closed-principle)
  * [OCP aplicado no código](#ocp-aplicado-no-código)
* [**L**iskov Substitution Principle](#liskov-substitution-principle)
* [**I**nterface Segregation Principle](#interface-segregation-principle)
* [**D**ependency Inversion Principle](#dependency-inversion-principle)

## Single Responsibility Principle

> Uma classe deve ter um, e somente um, motivo para mudar[^1].

> Deixe junto as coisas que mudam pela mesma razão. Separe aquelas que mudam por razões diferentes[^1].

O princípio da responsabilidade única fala sobre como diminuir o acoplamento baseado na frequência e na origem das mudanças/alterações no código.

Alguns exemplos:
* [SRP aplicado no código](solid-examples/SRP)

[^1]: https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html

## Open/Closed Principle

> Entidades de software (classes, módulos, funções, etc.) devem ser abertas para extensão, mas fechadas para modificação[^2].

[^2]: https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle

Módulos que atendem o princípio aberto/fechado possuem dois principais atributos:

* Eles estão "abertos para extensão"
* Eles estão "fechados para modificação"

Conseguimos atingir estes dois atributos utilizando abstração.

Alguns exemplos:
* [OCP aplicado no código](solid-examples/OCP)

## Liskov Substitution Principle

> Os serviços da classe derivada não devem exigir mais e prometer nada menos do que os serviços correspondentes da classe base[^3].

> A função que usa uma classe base deve conseguir usar suas subclasses sem distinguir[^3].

[^3]: https://en.wikipedia.org/wiki/Liskov_substitution_principle

## Interface Segregation Principle

> Muitas interfaces específicas são melhores do que uma interface única geral[^4].

Este princípio traz o foco em diminuir o acoplamento em interfaces. Com interfaces mais coesas, conseguimos criar códigos coesos e com baixo acoplamento.

Alguns exemplos:
* [ISP aplicado no código](solid-examples/ISP)

[^4]: https://en.wikipedia.org/wiki/Interface_segregation_principle

## Dependency Inversion Principle

> Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações[^5].

[^5]: https://en.wikipedia.org/wiki/Dependency_inversion_principle

