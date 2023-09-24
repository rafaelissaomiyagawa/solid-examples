# S.O.L.I.D. Exemplos

* [**S**ingle Responsibility Principle](#single-responsibility-principle)
  * [SRP aplicado no código](#srp-aplicado-no-código)
* [**O**pen/Closed Principle](#open-closed-principle)
* [**L**iskov Substitution Principle](#liskov-substitution-principle)
* [**I**nterface Segregation Principle](#interface-segregation-principle)
* [**D**ependency Inversion Principle](#dependency-inversion-principle)

## Single Responsibility Principle

> Uma classe deve ter um, e somente um, motivo para mudar[^1].

> Deixe junto as coisas que mudam pela mesma razão. Separe aquelas que mudam por razões diferentes[^1].

O princípio da responsabilidade única fala sobre como diminuir o acoplamento baseado na frequência e na origem das mudanças/alterações no código.

Para entender melhor vamos ver alguns exemplos.

### SRP aplicado no código 

Imagine uma classe chamada de Usuario que representa os usuários com email e senha para acessar um sistema.

```java
class Usuario {
    private final String email;
    private final String senha;
}
```

Agora vamos supor que este sistema é um sistema utilizado para registrar notas dos alunos que frequentam a escola. Então, vamos adicionar um atributo chamado de matrícula e notas.

```java
class Usuario {
    private final String email;
    private final String senha;
    private final String matricula;
    private final List<Double> notas;
}
```

Aqui já temos um sinal de que o princípio está sendo quebrado. Mas, vamos continuar para entender o problema.
O nosso sistema está ficando bom. As escolas gostaram e agora querem que o sistema tenha um cadastro de professores. Então, vamos adicionar um atributo chamado de disciplina para saber qual disciplina o professor leciona e um booleano para diferenciar aluno e professor.

```java
class Usuario {
    private final String email;
    private final String senha;
    private final String matricula;
    private final List<Double> notas;
    private final String disciplina;
    private final boolean isProfessor;
}
```

Agora o problema fica mais claro. A classe está com mais de uma responsabilidade. Ela está responsável por representar um usuário e também por representar um aluno ou professor. Como este problema aconteceu? Vamos analisar as origens das alterações feitas na classe com o diagrama seguinte:

![srp origem mudanca](doc-resources/srp-origem-mudanca.png "Origem das alterações")

Temos três casos de uso:

* Autenticar usuário
* Cadastrar aluno
* Cadastrar professor

E uma classe é responsável por representar todos os casos de uso. E este é o problema. Está muito acoplado com **casos de usos** diferentes. 

O custo para alterar a classe Usuario vai ficando cada vez mais caro, pois precisamos verificar se os três casos de uso ainda continuam do jeito que deveria se comportar.

Para solucionar, o princípio diz: **Separar as coisas que mudam por razões diferentes**. Então, vamos separar a classe Usuario em três classes:Usuario, Aluno e Professor.

```java
class Usuario {
    private final String email;
    private final String senha;
}

class Aluno {
    private final String matricula;
    private final List<Double> notas;
}

class Professor {
    private final String disciplina;
}
```

Agora, cada classe tem uma responsabilidade e tem somente uma razão para mudar.




[^1]: https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html

## Open/Closed Principle

> Entidades de software (classes, módulos, funções, etc.) devem ser abertas para extensão, mas fechadas para modificação[^2].

[^2]: https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle

## Liskov Substitution Principle

> Os serviços da classe derivada não devem exigir mais e prometer nada menos do que os serviços correspondentes da classe base[^3].

> A função que usa uma classe base deve conseguir usar suas subclasses sem distinguir[^3].

[^3]: https://en.wikipedia.org/wiki/Liskov_substitution_principle

## Interface Segregation Principle

> Muitas interfaces específicas são melhores do que uma interface única geral[^4].

[^4]: https://en.wikipedia.org/wiki/Interface_segregation_principle

## Dependency Inversion Principle

> Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações[^5].

[^5]: https://en.wikipedia.org/wiki/Dependency_inversion_principle

