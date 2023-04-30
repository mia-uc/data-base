## Diagrama E/R

```mermaid
classDiagram
    A1 .. B1 : 0-n a 0-n
    A2 ..> B2 : 0-n a 0-1
    A3 ..|> B3 : 0-n a 1-1
    A4 -- B4 : 1-n a 1-n
    A5 --> B5 : 1-n a 0-1
    A6 --|> B6 : 1-n a 1-1
    A7 <--> B7 : 0-1 a 0-1
    A8 <--|> B8 : 0-1 a 1-1
```

```mermaid
classDiagram
    Tienda ..|> Dirección : Esta en
    Dirección ..|> Comuna: Perteneces a
    Tienda  <|--  Personal : Trabaja en
    Tienda  --|>  Personal : Es dirigida por

    Tienda <|.. Stock : Esta en la bodega de
    Producto o.. Dormitorio: hereda
    Producto o.. Iluminación: hereda
    Producto o.. Living: hereda
    Producto <|-- Caja: Contiene una parte de
    Stock  ..|>  Producto: de
    Oferta ..|> Stock : para


    class Comuna {
        comuna [PK]
        región
    }

    class Dirección {
        did [PK]
        calle
        número
        comuna [FK Comuna comuna]
    }

    class Tienda{
        tid [PK]
        teléfono
        did [FK Dirección did]
        capacidad del estacionamiento
    }

    class Personal {
        nombre
        rut [PK]
        edad
        genero
        la fecha desde inicio
        tid [FK Tienda tid]
    }

    note for Stock "Es el resultado de la agregación\n entre Tienda y Producto"
    class Stock {
        tid [PK] [FK Tienda tid]
        pid [PK] [FK Producto pid]
        cantidad
    }

    class Producto {
        pid [PK]
        nombre
        precio
        número de cajas
    }

    class Dormitorio {
        pid [PK] [FK Producto pid]
        tamaño
        color
        descripción
    }

    class Iluminación {
        pid [PK] [FK Producto pid]
        frecuencia de trabajo
        color
        tensión
    }

    class Living {
        pid [PK] [FK Producto pid]
        dimensiones
        material
        carga
    }

    class Oferta {
         porcentaje de descuento
    }

    class Caja {
        cid [PK]
        pid [FK Producto pid]
        peso de la caja
        descripción
    }
```

**Tienda**(tid `int PRIMARY KEY`, teléfono `string`, Comuna.comuna `string`, capacidad de estacionamiento `int`) \
**Personal**(nombre `string`, rut `string PRIMARY KEY`, edad `int`, genero `string`, la fecha desde inicio `date`, Tienda.tid `int`) \
**EsDirigidaPor**(Tienda.tid `int PRIMARY KEY`, Personal.rut `string`)\
**Comuna**(comuna `string PRIMARY KEY`, region `string`)

**Producto**(pid `int PRIMARY KEY`, nombre `string`, precio `float`, número de cajas `int`) \
**Dormitorio**(Producto.pid `int PRIMARY KEY`, tamaño `float`, color `string`, descripción `string`) \
**Iluminación**(Producto.pid `int PRIMARY KEY`, frecuencia `float`, color `string`, tensión `float`) \
**Living**(Producto.pid `int PRIMARY KEY`, dimensiones `string`, material `string`, carga `string`)\
**Caja**(cid `int PRIMARY KEY`, Producto.pid `int`, peso de la caja `float`, descripción `string`)

**Stock**(Tienda.tid `int PRIMARY KEY`, Producto.pid `int PRIMARY KEY`, cantidad `int`) \
**Oferta**(oid `int PRIMARY KEY`, Tienda.tid `int`, Producto.pid `int`, porcentaje de descuento `float`)

```sql
CREATE TABLE Tienda(
    id SERIAL PRIMARY KEY,
    teléfono varchar(15) NOT NULL,
    comuna varchar(30) NOT NULL,
    capacidadDeEstacionamiento int DEFAULT 0,
    FOREIGN KEY(comuna) REFERENCES Comuna(comuna) ON DELETE SET NULL
)

CREATE TABLE Personal(
    rut varchar(30) PRIMARY KEY,
    nombre varchar(30) NOT NULL,
    edad int DEFAULT 18 CHECK ( edad >= 18 ),
    genero varchar(30) DEFAULT NULL,
    fechaDeInicio date DEFAULT NOW(),
    TiendaId int NOT NULL,
    esJefeDeLaTienda boolean DEFAULT NULL,
    FOREIGN KEY(TiendaId) REFERENCES Tienda(id) ON DELETE CASCADE,
    CONSTRAINT UC_Only_One_Boss UNIQUE (TiendaId, esJefeDeLaTienda)
)

CREATE TABLE EsDirigidaPor(
    TiendaId int PRIMARY KEY,
    PersonalRut varchar(30) UNIQUE NOT NULL ,
    FOREIGN KEY(TiendaId) REFERENCES Tienda(id) ON DELETE CASCADE,
    FOREIGN KEY(PersonalRut) REFERENCES Personal(rut) ON DELETE CASCADE
)

CREATE TABLE Producto(
    id SERIAL PRIMARY KEY,
    nombre varchar(30) NOT NULL,
    precio float NOT NULL CHECK ( precio >= 0 ),
    numeroDeCajas int DEFAULT 1 CHECK ( numeroDeCajas >= 1 ),
)

CREATE TABLE Dormitorio(
    ProductId int PRIMARY KEY,
    tamaño float NOT NULL CHECK ( tamaño > 0 ),
    color varchar(30) NOT NULL,
    descripción varchar(512),
    FOREIGN KEY(ProductId) REFERENCES Producto(id) ON DELETE CASCADE,
)

CREATE TABLE Iluminación(
    ProductId int PRIMARY KEY,
    frecuencia float NOT NULL,
    color varchar(30) NOT NULL,
    tensión float NOT NULL,
    FOREIGN KEY(ProductId) REFERENCES Producto(id) ON DELETE CASCADE,
)

CREATE TABLE Living(
    ProductId int PRIMARY KEY,
    dimensiones varchar(30) NOT NULL,
    material varchar(30) NOT NULL,
    carga varchar(30) NOT NULL,
    FOREIGN KEY(ProductId) REFERENCES Producto(id) ON DELETE CASCADE,
)

CREATE TABLE Caja(
    id SERIAL PRIMARY KEY,
    ProductoId int NOT NULL,
    peso float NOT NULL CHECK ( peso > 0 ),
    descripción varchar(512),
    FOREIGN KEY(ProductId) REFERENCES Producto(id) ON DELETE CASCADE,
)

CREATE TABLE Stock(
    TiendaId int NOT NULL,
    ProductoId int NOT NULL,
    cantidad float DEFAULT 0 CHECK ( cantidad >= 0 ),
    PRIMARY KEY (TiendaId, ProductoId)
    FOREIGN KEY(ProductId) REFERENCES Producto(id) ON DELETE CASCADE,
    FOREIGN KEY(TiendaId) REFERENCES Tienda(id) ON DELETE CASCADE,
)

CREATE TABLE Oferta(
    id SERIAL PRIMARY KEY,
    TiendaId int NOT NULL,
    ProductoId int NOT NULL,
    porCiento float DEFAULT 1 CHECK ( porCiento >= 1 AND porCiento <= 99 ),
    FOREIGN KEY(ProductId) REFERENCES Producto(id) ON DELETE CASCADE,
    FOREIGN KEY(TiendaId) REFERENCES Tienda(id) ON DELETE CASCADE,
)

```
