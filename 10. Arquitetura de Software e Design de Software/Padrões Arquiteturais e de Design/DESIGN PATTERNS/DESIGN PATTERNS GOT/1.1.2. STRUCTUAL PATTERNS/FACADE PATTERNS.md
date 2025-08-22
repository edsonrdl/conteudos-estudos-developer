O padrão Facade é um padrão estrutural que fornece uma interface simplificada para um subsistema complexo. Ele oculta as complexidades do sistema e fornece uma interface para o cliente interagir com o sistema de uma maneira mais fácil e compreensível.

### Estrutura do Facade

- **Facade**: Fornece uma interface simplificada para o subsistema.
- **Subsystem Classes**: Contém a funcionalidade complexa do sistema. O Facade delega as chamadas dos clientes para os métodos apropriados dessas classes.

Diagrama UML ClASS

![[Pasted image 20250407125733.png]]
### Exemplo em Java

Vamos considerar um exemplo onde temos um sistema complexo de home theater com vários componentes, como DVD player, projetor, amplificador, etc. O Facade vai simplificar a interface para ligar e desligar o sistema de home theater.

Classes do Subsistema

```
public class Amplifier {
    public void on() {
        System.out.println("Amplifier on");
    }

    public void off() {
        System.out.println("Amplifier off");
    }

    public void setVolume(int level) {
        System.out.println("Setting volume to " + level);
    }
}

public class DvdPlayer {
    public void on() {
        System.out.println("DVD Player on");
    }

    public void off() {
        System.out.println("DVD Player off");
    }

    public void play(String movie) {
        System.out.println("Playing movie: " + movie);
    }
}

public class Projector {
    public void on() {
        System.out.println("Projector on");
    }

    public void off() {
        System.out.println("Projector off");
    }

    public void setInput(String input) {
        System.out.println("Setting projector input to " + input);
    }
}

public class Screen {
    public void down() {
        System.out.println("Screen down");
    }

    public void up() {
        System.out.println("Screen up");
    }
}

public class TheaterLights {
    public void dim(int level) {
        System.out.println("Dimming lights to " + level + "%");
    }
}

```

Facade

```
public class HomeTheaterFacade {
    private Amplifier amp;
    private DvdPlayer dvd;
    private Projector projector;
    private Screen screen;
    private TheaterLights lights;

    public HomeTheaterFacade(Amplifier amp, DvdPlayer dvd, Projector projector, Screen screen, TheaterLights lights) {
        this.amp = amp;
        this.dvd = dvd;
        this.projector = projector;
        this.screen = screen;
        this.lights = lights;
    }

    public void watchMovie(String movie) {
        System.out.println("Get ready to watch a movie...");
        lights.dim(10);
        screen.down();
        projector.on();
        projector.setInput("DVD");
        amp.on();
        amp.setVolume(5);
        dvd.on();
        dvd.play(movie);
    }

    public void endMovie() {
        System.out.println("Shutting movie theater down...");
        lights.dim(100);
        screen.up();
        projector.off();
        amp.off();
        dvd.off();
    }
}

```

Cliente

```
public class HomeTheaterTest {
    public static void main(String[] args) {
        Amplifier amp = new Amplifier();
        DvdPlayer dvd = new DvdPlayer();
        Projector projector = new Projector();
        Screen screen = new Screen();
        TheaterLights lights = new TheaterLights();

        HomeTheaterFacade homeTheater = new HomeTheaterFacade(amp, dvd, projector, screen, lights);
        homeTheater.watchMovie("Inception");
        homeTheater.endMovie();
    }
}

```

### Explicação

1. **Subsystem Classes (Amplifier, DvdPlayer, Projector, Screen, TheaterLights)**: Contêm a funcionalidade complexa e detalhada do sistema de home theater.
2. **Facade (HomeTheaterFacade)**: Fornece métodos simplificados `watchMovie` e `endMovie` que encapsulam a complexidade de ligar e desligar o sistema de home theater.
3. **Cliente (HomeTheaterTest)**: Interage com o sistema de home theater através da interface simplificada fornecida pela classe `HomeTheaterFacade`.

### Saída Esperada

Get ready to watch a movie...

Dimming lights to 10%

Screen down

Projector on

Setting projector input to DVD

Amplifier on

Setting volume to 5

DVD Player on

Playing movie: Inception

Shutting movie theater down...

Dimming lights to 100%

Screen up

Projector off

Amplifier off

DVD Player off

### Benefícios e Considerações

Benefícios

1. **Simplicidade**: Simplifica a interface para o cliente.
2. **Isolamento**: Reduz o acoplamento entre o cliente e o subsistema.
3. **Manutenibilidade**: Facilita a manutenção e evolução do subsistema sem afetar os clientes.

Considerações

1. **Transparência**: Pode esconder funcionalidades específicas do subsistema que o cliente pode precisar.
2. **Performance**: Adicionar uma camada extra pode introduzir um pequeno overhead de desempenho.

### Conclusão

O padrão Facade é uma maneira eficaz de simplificar a interação com sistemas complexos, fornecendo uma interface limpa e fácil de usar para os clientes. Ele promove o desacoplamento e facilita a manutenção e evolução dos sistemas ao encapsular suas complexidades internas.