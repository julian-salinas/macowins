# TP Individual - Macowins

## Prendas
- **Requerimiento:** “Queremos saber el precio de venta de una prenda y sus tipos, los tipos de prenda son: sacos, pantalones, camisas.”

- El precio de una prenda es el precio propio modificado según su estado *(nueva, promoción, liquidación)*

```java
class Prenda {
    var precioPropio // int
    var tipo // Se usa un enum para los tipos disponibles
    var estado // Se implementa interfaz de estado

    method precioPropio() {
        return precioPropio
    }

    method precio() {
        return estado.calcularPrecioPrenda(self.precioPropio())
    }
}
```


## Tipos
Los unicos tipos de prenda disponibles son *sacos, pantalones y camisas*
```java
enum Tipo {
    SACOS, PANTALONES, CAMISAS
}
```


## Estados
Los estados serán quienes determinen el precio de la prenda 
```java
interface Estado {
    method calcularPrecioPrenda(precio) {}
}

class Nueva implements Estado {
    // Si una prenda es nueva, no se modifica el precio base
    method calcularPrecioPrenda(precio) {
        return precio
    }
}

class Promocion implements Estado {
    var property valorFijo

    // Se le resta al precio propio de la prenda un valor fijo
    method calcularPrecioPrenda(precio) {
        return precio - self.valorfijo()
    }
}

class Liquidacion implements Estado {
    // El valor de la prenda se reduce a la mitad
    method calcularPrecioPrenda(precio) {
        return precio * 0.5
    } 
}
```

## Ventas
- Cada venta tiene asociada las prendas que se vendieron, su cantidad y la fecha de venta. 
- Las ventas pueden ser en efectivo o con tarjeta. En el caso que sea con tarjeta, tienen el mismo comportamiento que en efectivo (el cual no modifica el precio), sólo que se le aplica un recargo según la cantidad de cuotas seleccionadas (cantidad de cuotas * un coeficiente fijo + 0.01 del valor de cada prenda).”

```java
class Venta {
    // Lista formada por objetos de la clase PrendaVendida
    const prendasVendidas
    var fechaVenta
    var medioDePago // Puede ser efectivo o crédito, se implementa interface

    method fechaVenta() {
        return fechaVenta
    }

    method precioVenta() {
        return prendasVendidas
                .forEach({ prendaVendida => self.gananciaDeLaVenta(prendaVendida) })
                .sum()
    }

    method gananciaDeLaVenta(prendaVendida) {
        precio = prendaVendida.prenda().precio()
        cantidad = prendaVendida.cantidad()

        // por cada prenda calculo su precio y lo multiplico por cantidad de prendas que se vendieron
        return medioDePago.calcularPrecioTotalVenta(precio) * cantidad
    }
}

// Esto sería casi como una clase auxiliar, para poder llevar registro de las prendas que se venden
class PrendaVendida {
    var prenda
    var cantidad

    method prenda() {
        return prenda
    }

    method cantidad() {
        return cantidad
    }
}
```

## Medios de Pago
Los medios de pago van a modificar (o no) el precio de una venta

```java
interface MedioDePago {
    method calcularPrecioTotalVenta(prenda) {}
}

class Efectivo implements MedioDePago {
    method calcularPrecioTotalVenta(prenda) {
        return precio 
    }
}

class Credito implements MedioDePago {
    var cantidadDeCuotas
    var coeficienteFijo

    method calcularPrecioTotalVenta(precio) {
        return cantidadDeCuotas * coeficienteFijo + precio * 0.01
    }
}
```

## Clase Macowins
La clase Macowins es la que tendrá todas las ventas realizadas (en todas las fechas) y se encargará de calcular la ganancia de un determinado día

```java
class Macowins {
    const ventasHistoricas = []

    method ventasHistoricas(){
        return ventasHistoricas
    }

    method calcularGananciaDeUnDia(dia) {
        return self.ventasHistoricas
            .filter({ unaVenta => unaVenta.FechaVenta().equals(dia)})
            .map({ unaVenta => unaVenta.gananciaDeLaVenta() })
            .sum()
    }
}
```