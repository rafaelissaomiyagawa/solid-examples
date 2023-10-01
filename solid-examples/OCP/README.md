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