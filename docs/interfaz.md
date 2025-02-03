# Interfaz de usuario

```mermaid
flowchart TD
    splash[Splash]
    login[Ingreso]
    profile[Perfil]
    first{Primera vez}
    equipment[[Registro de equipo]]
    home[Inicio]
    config[Configuración de viaje]
    start((Confirmar))
    trip[Viaje en progreso]
    alert[Crear alerta]
    complete((Confirmar))

    splash --> login --> profile --> first
    first -->|Sí| equipment
    first -->|No| home
    equipment --> home
    home --> config 
    config --> start --> trip
    trip -->|Interrumpir| alert --> home
    trip -->|Completar| complete --> home


```

## Arranque (*splash*)

### UI

Despliega el logo de Databús sobre un fondo azul (azul UCR).

### Datos

- No necesita datos del API

## Ingreso (*login*)

### UI

Logo y campos de texto para credenciales y botón de "Ingresar".

### API

```http
POST /api/login/ HTTP/1.1
...
Content-Type: application/json
Content-Length: 51

{
	"username": "conductor",
  	"password": "contraseña"
}
```

Ejemplo de respuesta:

```json
{
    "token": "412d1c7682d76919b568a3dcddd3fb67e17c7865",
    "operator_id": "1-4321-8765"
}
```

Todos los `POST` sucesivos llevan el *token* como método de autenticación.

## Perfil (*profile*)

### UI

Datos del conductor y selección de la agencia (`agency_id`) y el vehículo (`vehicle.id`)

### API

```http
GET /api/operator/<operator_id> HTTP/1.1
...
Authorization: Bearer 412d1c7682d76919b568a3dcddd3fb67e17c7865
...
```

Ejemplo de respuesta:

```json
{
    "url": "http://127.0.0.1:8000/api/operator/1-4321-8765/",
    "user": 2,
    "agency": [1, 2],
    "vehicle": null,
    "equipment": null,
    "phone": "87654321",
    "data_provider": []
}
```

Para cargar las agencias disponibles, el *endpoint* `/api/gtfs/schedule/agencies` puede devolver una lista de agencias según el filtro `?ids=1,2,3`:

```http
GET /api/gtfs/schedule/agencies/?ids=1,2 HTTP/1.1
...
Authorization: Bearer 412d1c7682d76919b568a3dcddd3fb67e17c7865
...
```

- Verificar que la app tiene el UUID de registro del equipo en el servidor.
- Si no lo tiene es la primera vez que se usa la aplicación, y hay que registrarla en el servidor.
- POST /api/equipment

## Inicio (*home*)

