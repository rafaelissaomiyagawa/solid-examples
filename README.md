# S.O.L.I.D. Exemplos

* [**S**ingle Responsibility Principle](#single-responsibility-principle)
  * [SRP aplicado no código](doc-examples/SRP)
* [**O**pen/Closed Principle](#open-closed-principle)
  * [OCP aplicado no código](#ocp-aplicado-no-código)
* [**L**iskov Substitution Principle](#liskov-substitution-principle)
* [**I**nterface Segregation Principle](#interface-segregation-principle)
* [**D**ependency Inversion Principle](#dependency-inversion-principle)

## Single Responsibility Principle

> Uma classe deve ter um, e somente um, motivo para mudar[^1].

> Deixe junto as coisas que mudam pela mesma razão. Separe aquelas que mudam por razões diferentes[^1].

O princípio da responsabilidade única fala sobre como diminuir o acoplamento baseado na frequência e na origem das mudanças/alterações no código.
s
Para entender melhor vamos ver alguns exemplos.
* [SRP aplicado no código](doc-examples/SRP)

[^1]: https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html

## Open/Closed Principle

> Entidades de software (classes, módulos, funções, etc.) devem ser abertas para extensão, mas fechadas para modificação[^2].

[^2]: https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle

Módulos que atendem o princípio aberto/fechado possuem dois principais atributos:

* Eles estão "abertos para extensão"
* Eles estão "fechados para modificação"

Conseguimos atingir estes dois atributos utilizando abstração.

### OCP aplicado no código

Vamos ver o que acontece com um código que não atende o princípio aberto/fechado para depois arrumarmos o problema.

Vamos supor que temos um sistema que é possível simular um dia de trabalho de uma equipe ágil.

```java
class EquipeAgil {
    public void simulaUmDiaDeTrabalho() {
    }
}
```

Agora, vamos começar a montar o time. Primeiro, vamos adicionar desenvolvedores.

```java
class EquipeAgil {
    private final List<Desenvolvedor> desenvolvedores = new ArrayList<>();

    public void simulaUmDiaDeTrabalho() {
        for (Desenvolvedor desenvolvedor : desenvolvedores) {
            desenvolvedor.desenvolve();
        }
    }
    
    public void adicionaDesenvolvedor(Desenvolvedor desenvolvedor) {
        desenvolvedores.add(desenvolvedor);
    }
}

class Desenvolvedor {
    public void desenvolve() {
        System.out.println("Desenvolvendo");
    }
}
```

Percebemos que precisamos de testadores, para ensinar os desenvolvedores a testar e ajudar a equipe a se preocupar com qualidade. Logo, vamos adicionar testadores.

```java
class EquipeAgil {
    private final List<Desenvolvedor> desenvolvedores = new ArrayList<>();
    private final List<Testador> testadores = new ArrayList<>();

    public void simulaUmDiaDeTrabalho() {
        for (Desenvolvedor desenvolvedor : desenvolvedores) {
            desenvolvedor.desenvolve();
        }
        // Para cada novo perfil, preciso adicionar um novo loop
        for (Testador testador : testadores) {
            testador.testa();
        }
    }
    
    public void adicionaDesenvolvedor(Desenvolvedor desenvolvedor) {
        desenvolvedores.add(desenvolvedor);
    }
    
    // Para cada novo perfil, preciso criar um método para adicionar
    public void adicionaTestador(Testador testador) {
        testadores.add(testador);
    }
}

class Desenvolvedor {
    public void desenvolve() {
        System.out.println("Desenvolvendo");
    }
}

class Testador {
    public void testa() {
        System.out.println("Testando");
    }
}
```

Veja o problema aqui. Com o design que temos agora, para cada profissional com novo perfil na equipe, precisamos modificar a classe EquipeAgil. Ou seja, esta classe não está fechada para modificação. Por consequência, temos um custo maior para alterar o comportamento do nosso código.

Para resolver este problema criamos uma abstração para representar todas as necessidades da classe EquipeAgil. Vamos chamar, por exemplo, essa abstração de Profissional e implementá-las nas classes Desenvolvedor e Testador. E também vamos utilizar a abstração Profissional na classe EquipeAgil.

```java
interface Profissional {
    void executa();
}

// Nunca mais precisamos modificar esta classe
class EquipeAgil {
    private final List<Profissional> profissionais = new ArrayList<>();

    public void simulaUmDiaDeTrabalho() {
        // Não importa o perfil, todos executam
        for (Profissional profissional : profissionais) {
            profissional.executa();
        }
    }
    
    // Não importa o perfil, todos são adicionados por este método
    public void adicionaProfissional(Profissional profissional) {
        profissionais.add(profissional);
    }
}

class Desenvolvedor implements Profissional {
    public void desenvolve() {
      System.out.println("Desenvolvendo");
    }
    
    @Override
    public void executa() {
        this.desenvolve();
    }
}

class Testador implements Profissional {
    public void testa() {
        System.out.println("Testando");
    }
    
    @Override
    public void executa() {
        this.testa();
    }
}
```

O importante é que agora, a classe EquipeAgil não precisa mais ser modificada para adicionar novos profissionais. 

Ela tem os dois principais atributos:

* "Aberta para extensão"
  * Para adicionar novos profissionais, basta criar uma classe que implemente a interface Profissional.
* "Fechada para modificação"
  * Não precisamos modificar a classe EquipeAgil para adicionar novos profissionais.

Se quisermos adicionar agora um gerente de produto, o código fica assim:

```java
interface Profissional {
  void executa();
}

class EquipeAgil {
  private final List<Profissional> profissionais = new ArrayList<>();

  public void simulaUmDiaDeTrabalho() {
    for (Profissional profissional : profissionais) {
      profissional.executa();
    }
  }

  public void adicionaProfissional(Profissional profissional) {
    profissionais.add(profissional);
  }
}

class Desenvolvedor implements Profissional {
  public void desenvolve() {
    System.out.println("Desenvolvendo");
  }

  @Override
  public void executa() {
    this.desenvolve();
  }
}

class Testador implements Profissional {
  public void testa() {
    System.out.println("Testando");
  }

  @Override
  public void executa() {
    this.testa();
  }
}

// Só preciso criar uma classe que implementa Profissional
class GerenteDeProduto implements Profissional {
    public void gerencia() {
        System.out.println("Gerenciando");
    }
    
    @Override
    public void executa() {
        this.gerencia();
    }
}
```


## Liskov Substitution Principle

> Os serviços da classe derivada não devem exigir mais e prometer nada menos do que os serviços correspondentes da classe base[^3].

> A função que usa uma classe base deve conseguir usar suas subclasses sem distinguir[^3].

[^3]: https://en.wikipedia.org/wiki/Liskov_substitution_principle

## Interface Segregation Principle

> Muitas interfaces específicas são melhores do que uma interface única geral[^4].

Este princípio traz o foco em diminuir o acoplamento em interfaces. Com interfaces mais coesas, conseguimos criar códigos coesos e com baixo acoplamento. Vamos ver um exemplo.

Imagine que temos uma interface chamada de SmartPhone com as seguintes funcionalidades:

```java
interface SmartPhone {
    void tocaMusica();
    void tiraFoto();
}
```

E vamos supor que temos duas classes, IPhone13 e GalaxyS21, que implementam a interface SmartPhone.

```java
class IPhone13 implements SmartPhone {
    @Override
    public void tocaMusica() {
        System.out.println("Tocando música no iPhone 13");
    }

    @Override
    public void tiraFoto() {
        System.out.println("Tirando foto no iPhone 13");
    }
}

class GalaxyS21 implements SmartPhone {
    @Override
    public void tocaMusica() {
        System.out.println("Tocando música no Galaxy S21");
    }

    @Override
    public void tiraFoto() {
        System.out.println("Tirando foto no Galaxy S21");
    }
}
```

E temos um potencial comprador na loja que quer um celular que toque música e tire foto. Então, vamos mostrar os dois celulares para ele.

```java
class Loja {
    public void mostraCelulares() {
        SmartPhone iPhone13 = new IPhone13();
        SmartPhone galaxyS21 = new GalaxyS21();
        
        mostraComoTiraFoto(iPhone13);
        mostraComoTiraFoto(galaxyS21);
        
        mostraComoEscutaMusica(iPhone13);
        mostraComoEscutaMusica(galaxyS21);
    }
    
    private void mostraComoTiraFoto(SmartPhone smartPhone) {
        smartPhone.tiraFoto();
    }
    
    private void mostraComoEscutaMusica(SmartPhone smartPhone) {
        smartPhone.tocaMusica();
    }
}
```

Esse código, por enquanto está ok. Vai compilar e funcionar tranquilamente. Mas o que acontece quando temos um celular que não tira foto? Por exemplo, um celular antigo? Vamos criar uma classe chamada de CelularAntigo que implementa a interface SmartPhone.

```java
class CelularAntigo implements SmartPhone {
    @Override
    public void tocaMusica() {
        System.out.println("Tocando música no celular antigo");
    }

    @Override
    public void tiraFoto() {
        throw new UnsupportedOperationException("Celular antigo não tira foto");
    }
}
```

Se eu usar o CelularAntigo na loja, o código já não vai mais funcionar. Vai dar uma exceção quando chamar o método tiraFoto. 

```java
class Loja {
    public void mostraCelulares() {
        SmartPhone iPhone13 = new IPhone13();
        SmartPhone galaxyS21 = new GalaxyS21();
        SmartPhone celularAntigo = new CelularAntigo();
        
        mostraComoTiraFoto(iPhone13);
        mostraComoTiraFoto(galaxyS21);
        mostraComoTiraFoto(celularAntigo); // Vai lançar uma exceção
        
        mostraComoEscutaMusica(iPhone13);
        mostraComoEscutaMusica(galaxyS21);
        mostraComoEscutaMusica(celularAntigo);
    }
    
    private void mostraComoTiraFoto(SmartPhone smartPhone) {
        smartPhone.tiraFoto();
    }
    
    private void mostraComoEscutaMusica(SmartPhone smartPhone) {
        smartPhone.tocaMusica();
    }
}
```

O problema aqui é que a interface SmartPhone não é coesa conforme o nosso novo contexto. Temos uma classe com algumas das funcionalidades da interface. Quando ignoramos o ISP, as implementações da interface SmartPhone são obrigadas a implementar funcionalidades que não são necessárias para elas. 

Um bom sinal de quebra deste princípio é o CelularAntigo lançar uma exceção no método tiraFoto. Ou quando somente comentamos a implementação do método tiraFoto na classe CelularAntigo.

Outro sinal da quebra do princípio é quando os métodos mostraComoTiraFoto e mostraComoEscutaMusica podem utilizar objetos que não suportam as funcionalidades que eles precisam.

Para atender este princípio, precisamos mexer em alguns lugares:

* Segregar a interface SmartPhone em duas interfaces: ComCamera e ComPlayerDeMusica
* Alterar os métodos mostraComoTiraFoto e mostraComoEscutaMusica para receberem as novas interfaces
* Alterar a classe CelularAntigo para implementar somente a interface ComPlayerDeMusica

As novas alterações ficam assim:

```java
interface ComCamera {
    void tiraFoto();
}

interface ComPlayerDeMusica {
    void tocaMusica();
}

interface SmartPhone extends ComCamera, ComPlayerDeMusica {
}

class CelularAntigo implements ComPlayerDeMusica {
    @Override
    public void tocaMusica() {
        System.out.println("Tocando música no celular antigo");
    }
}

class Loja {
    public void mostraCelulares() {
        SmartPhone iPhone13 = new IPhone13();
        SmartPhone galaxyS21 = new GalaxyS21();
        ComPlayerDeMusica celularAntigo = new CelularAntigo();
        
        mostraComoTiraFoto(iPhone13);
        mostraComoTiraFoto(galaxyS21);
        // mostraComoTiraFoto(celularAntigo); Não é mais possível, pois celularAntigo não implementa ComCamera
        
        mostraComoEscutaMusica(iPhone13);
        mostraComoEscutaMusica(galaxyS21);
        mostraComoEscutaMusica(celularAntigo);
    }
    
    private void mostraComoTiraFoto(ComCamera celularComCamera) {
        celularComCamera.tiraFoto();
    }
    
    private void mostraComoEscutaMusica(ComPlayerDeMusica celularComPlayerDeMusica) {
        celularComPlayerDeMusica.tocaMusica();
    }
}
```
Tornando as interfaces coesas ajuda as implementações a serem mais coesas e as funcionalidades mais específicas.

[^4]: https://en.wikipedia.org/wiki/Interface_segregation_principle

## Dependency Inversion Principle

> Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações[^5].

[^5]: https://en.wikipedia.org/wiki/Dependency_inversion_principle

