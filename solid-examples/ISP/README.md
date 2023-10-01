### ISP aplicado no código

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
Tornando as interfaces coesas ajuda as implementações a serem mais coesas e as funcionalidades mais específicas.s