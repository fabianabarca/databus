# Arquitectura de la aplicación

## Flujo de uso (interfaz de usuario)

(I) Registro de proveedor de datos

(I) Registro de datos del vehículo
- Obtener datos de vehículos registrados para el proveedor

### Configuración del equipo

Al principio de la operación de la aplicación.

```mermaid
sequenceDiagram
    Databús->>API: Login del proveedor
    Databús->>API: GET /agency?provider_id=provider_id
    API->>Databús: Datos de las agencias del proveedor
    Databús->>API: GET /vehicle?agency_id=agency_id
    API->>Databús: Datos de todos los vehículos de la agencia
    opt Vehículo no existe
        Databús->>API: Datos del nuevo vehículo
        API->>Databús: vehicle id
    end
    Databús->>API: Registro del equipo
```

### Identificación del conductor

Cada vez que cambia el conductor (posiblemente hasta varias veces al día)

```mermaid
sequenceDiagram
    autonumber
    Databús->>API: GET /operator
    API->>Databús: Datos de todos los operadores de la agencia
    opt Operador no existe
        Databús->>API: Datos del nuevo operador
        API->>Databús: operator_id
    end
    Note right of Databús: Elegir un operador
```

Antes de confirmar el inicio de un nuevo viaje: identificar al conductor.

### Envío de datos en un viaje

Cuando inicia un viaje

```mermaid
sequenceDiagram
    autonumber
    Databús->>API: GET /operator
    API->>Databús: Datos de todos los operadores de la agencia
    opt Operador no existe
        Databús->>API: Datos del nuevo operador
        API->>Databús: operator_id
    end
    Note right of Databús: Elegir un operador
```
