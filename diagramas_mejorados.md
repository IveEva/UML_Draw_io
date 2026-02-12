## Diagrama de clases
```mermaid
classDiagram
    class CalculadoraApp {
        +main(String[] args)$
    }

    class CalculadoraControlador {
        -CalculadoraLogica modelo
        -CalculadoraVista vista
        -boolean continuar
        +iniciar()
        -ejecutarCiclo()
        -procesarSeleccion(char op, double n1, double n2)
    }

    class CalculadoraLogica {
        +sumar(double, double) double
        +restar(double, double) double
        +multiplicar(double, double) double
        +dividir(double, double) double
    }

    class CalculadoraVista {
        -Scanner scanner
        +solicitarDatos() DatosEntrada
        +preguntarReiniciar() boolean
        +mostrarResultado(double)
        +mostrarError(String)
    }

    class DatosEntrada {
        +double n1
        +double n2
        +char operacion
    }

    CalculadoraApp --> CalculadoraControlador : "instancia y arranca"
    CalculadoraControlador --> CalculadoraLogica : "usa para cálculos"
    CalculadoraControlador --> CalculadoraVista : "usa para I/O"
    CalculadoraVista ..> DatosEntrada : "crea para agrupar datos"
```

## Diagrama de secuencia
```mermaid

sequenceDiagram
    participant App as Main
    participant C as Controlador
    participant V as Vista
    participant M as Lógica (Modelo)

    App->>C: iniciar()
    
    loop Mientras continuar == true
        C->>V: solicitarDatos()
        V-->>U: (Interacción Usuario)
        Note right of V: Valida que sean números
        V-->>C: retorna objeto DatosEntrada
        
        C->>M: operar(datos.n1, datos.n2, datos.op)
        alt Operación Válida
            M-->>C: retorna resultado (double)
            C->>V: mostrarResultado(resultado)
        else División por cero / Error
            M-->>C: lanza ArithmeticException
            C->>V: mostrarError("Mensaje de error")
        end
        
        C->>V: preguntarReiniciar()
        V-->>U: "¿Desea realizar otra operación?"
        U->>V: Selecciona (S/N)
        V-->>C: retorna boolean
    end
    
    C->>V: mostrarMensajeDespedida()
    C-->>App: finaliza ejecución
```

## Diagrama de estados
```mermaid
stateDiagram-v2
    [*] --> Inicializado
    
    state AplicacionEnEjecucion {
        Inicializado --> EsperandoEntrada: Iniciar flujo
        
        EsperandoEntrada --> ProcesandoCalculo: Datos recibidos (DTO)
        
        state ProcesandoCalculo {
            [*] --> ValidarOperacion
            ValidarOperacion --> EjecutandoMatematica: OK
            ValidarOperacion --> EstadoError: División por cero / Op. Inválida
            EjecutandoMatematica --> GenerandoResultado
        }
        
        ProcesandoCalculo --> MostrandoResultado: Éxito
        ProcesandoCalculo --> MostrandoError: Fallo
        
        MostrandoResultado --> EsperandoDecision
        MostrandoError --> EsperandoDecision
        
        EsperandoDecision --> EsperandoEntrada: Usuario elige "Reiniciar"
    }
    
    EsperandoDecision --> Finalizado: Usuario elige "Salir"
    Finalizado --> [*]
```

## Diagrama de secuencia del programa
```mermaid
sequenceDiagram
    participant as Usuario
    participant M as Main
    participant C as CalculadoraControlador
    participant V as CalculadoraVista
    participant L as CalculadoraLogica

    M->>C: iniciar()
    
    loop Mientras usuario quiera continuar
        C->>V: solicitarDatos()
        V-->>U: (Pide num1, op, num2)
        Note over V: Valida tipos de datos
        V-->>C: retorna objeto DatosEntrada
        
        C->>L: calcular(datos)
        alt Operación Exitosa
            L-->>C: retorna resultado (double)
            C->>V: mostrarResultado(resultado)
        else Error (División por cero)
            L-->>C: lanza ArithmeticException
            C->>V: mostrarError(mensaje)
        end
        
        C->>V: preguntarDeseaContinuar()
        V-->>C: retorna boolean
    end
    
    C->>V: mostrarDespedida()
    C-->>M: Fin del programa
```
