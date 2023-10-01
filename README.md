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

Alguns exemplos.
* [SRP aplicado no código](solid-examples/SRP)

[^1]: https://blog.cleancoder.com/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html

## Open/Closed Principle

> Entidades de software (classes, módulos, funções, etc.) devem ser abertas para extensão, mas fechadas para modificação[^2].

[^2]: https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle

Módulos que atendem o princípio aberto/fechado possuem dois principais atributos:

* Eles estão "abertos para extensão"
* Eles estão "fechados para modificação"

Conseguimos atingir estes dois atributos utilizando abstração.

Alguns exemplos.
* [OCP aplicado no código](solid-examples/OCP)

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

