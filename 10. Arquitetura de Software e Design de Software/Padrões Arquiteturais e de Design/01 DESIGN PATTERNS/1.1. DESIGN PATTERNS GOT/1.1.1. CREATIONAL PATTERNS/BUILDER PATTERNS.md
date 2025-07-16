O padrão Builder é um padrão de design criacional que permite a construção de objetos complexos passo a passo. Ao contrário dos outros padrões de criação, o Builder não requer que os produtos tenham uma interface comum. Isso permite que você crie diferentes representações de um objeto usando o mesmo processo de construção.

### Estrutura do Builder

- **Builder (Construtor)**: Declara uma interface para criar partes de um objeto Product.
- **ConcreteBuilder (ConstrutorConcreto)**: Implementa a interface Builder para construção e montagem das partes do produto. Mantém uma representação do produto que está sendo construído.
- **Product (Produto)**: Representa o objeto complexo em construção.
- **Director (Diretor)**: Constrói um objeto usando a interface Builder.

### Exemplo em Java

Vamos considerar um exemplo onde construímos diferentes tipos de veículos (Carro e Moto) usando o padrão Builder.

Implementação

Product

```java
// Produto: Veículo
public class Vehicle {
    private String type;
    private int wheels;
    private String engine;
    private int seats;

    public void setType(String type) {
        this.type = type;
    }

    public void setWheels(int wheels) {
        this.wheels = wheels;
    }

    public void setEngine(String engine) {
        this.engine = engine;
    }

    public void setSeats(int seats) {
        this.seats = seats;
    }

    @Override
    public String toString() {
        return "Vehicle [type=" + type + ", wheels=" + wheels + ", engine=" + engine + ", seats=" + seats + "]";
    }
}

```

Builder

```java
// Construtor: Declara uma interface para criar partes do produto Veículo
public interface VehicleBuilder {
    void buildType();
    void buildWheels();
    void buildEngine();
    void buildSeats();
    Vehicle getVehicle();
}

```

ConcreteBuilder

```java
// Construtor Concreto: Carro
public class CarBuilder implements VehicleBuilder {
    private Vehicle car;

    public CarBuilder() {
        this.car = new Vehicle();
    }

    @Override
    public void buildType() {
        car.setType("Car");
    }

    @Override
    public void buildWheels() {
        car.setWheels(4);
    }

    @Override
    public void buildEngine() {
        car.setEngine("V8");
    }

    @Override
    public void buildSeats() {
        car.setSeats(5);
    }

    @Override
    public Vehicle getVehicle() {
        return this.car;
    }
}

// Construtor Concreto: Moto
public class MotorcycleBuilder implements VehicleBuilder {
    private Vehicle motorcycle;

    public MotorcycleBuilder() {
        this.motorcycle = new Vehicle();
    }

    @Override
    public void buildType() {
        motorcycle.setType("Motorcycle");
    }

    @Override
    public void buildWheels() {
        motorcycle.setWheels(2);
    }

    @Override
    public void buildEngine() {
        motorcycle.setEngine("Single-cylinder");
    }

    @Override
    public void buildSeats() {
        motorcycle.setSeats(2);
    }

    @Override
    public Vehicle getVehicle() {
        return this.motorcycle;
    }
}

```

Director

```java
// Diretor: Constrói um objeto usando a interface Builder
public class Director {
    private VehicleBuilder builder;

    public Director(VehicleBuilder builder) {
        this.builder = builder;
    }

    public void construct() {
        builder.buildType();
        builder.buildWheels();
        builder.buildEngine();
        builder.buildSeats();
    }
}

```

Client

```java
public class BuilderPatternTest {
    public static void main(String[] args) {
        // Construtor de Carro
        VehicleBuilder carBuilder = new CarBuilder();
        Director carDirector = new Director(carBuilder);
        carDirector.construct();
        Vehicle car = carBuilder.getVehicle();
        System.out.println(car);

        // Construtor de Moto
        VehicleBuilder motorcycleBuilder = new MotorcycleBuilder();
        Director motorcycleDirector = new Director(motorcycleBuilder);
        motorcycleDirector.construct();
        Vehicle motorcycle = motorcycleBuilder.getVehicle();
        System.out.println(motorcycle);
    }
}

```

### Explicação

1. **Product (Vehicle)**: Representa o objeto complexo em construção.
2. **Builder (VehicleBuilder)**: Declara uma interface para criar partes do produto `Vehicle`.
3. **ConcreteBuilder (CarBuilder, MotorcycleBuilder)**: Implementa a interface `VehicleBuilder` para construção e montagem das partes do produto.
4. **Director (Director)**: Constrói um objeto usando a interface `VehicleBuilder`.
5. **Client (BuilderPatternTest)**: Usa o `Director` e os `ConcreteBuilder` para criar e montar objetos `Vehicle`.

### Saída Esperada

```
Vehicle [type=Car, wheels=4, engine=V8, seats=5]
Vehicle [type=Motorcycle, wheels=2, engine=Single-cylinder, seats=2]
```

### Benefícios e Considerações

Benefícios

1. **Construção Gradual**: Permite a construção gradual de um objeto, passo a passo.
2. **Separação da Lógica de Construção**: A lógica de construção é separada da lógica de negócio, facilitando a manutenção e a legibilidade.
3. **Facilidade de Alteração**: Facilita a criação de diferentes representações de um objeto, alterando o builder.

Considerações

1. **Complexidade Adicional**: Pode introduzir complexidade adicional ao sistema, especialmente se houver muitas partes a serem construídas.
2. **Mais Código**: Pode levar a um aumento significativo na quantidade de código, com muitas interfaces e classes concretas.

### **Casos para Uso**

### Conclusão

O padrão Builder é uma poderosa ferramenta para a construção de objetos complexos passo a passo, promovendo a separação da lógica de construção da lógica de negócio. Ele permite a criação de diferentes representações de um objeto usando o mesmo processo de construção, embora possa introduzir complexidade adicional ao sistema.