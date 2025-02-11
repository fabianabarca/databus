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
    "operator_id": "1-4321-8765",
    "first_name": "José",
    "last_name": "Vargas",
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
    "url": "http://127.0.0.1:8000/api/operator/2-1234-5678/",
    "user": 3,
    "company": [
        "TRANSTUSA",
        "OSG"
    ],
    "phone": "87654321",
    "photo": "http://127.0.0.1:8000/media/operators/jose.jpg"
},
```

Para cargar las agencias (compañías) disponibles para el operador, la app hace sucesivas llamadas al API en `api/company/<id>`.

- Verificar que la app tiene el UUID de registro del equipo en el servidor.
- Si no lo tiene es la primera vez que se usa la aplicación, y hay que registrarla en el servidor.
- POST `/api/equipment`

```http
POST /api/equipment/?data_provider=1964&vehicle=SJB1234&serial_number=MJF9324046TJHEGR0&brand=TopPlast&model=Rattan&os_version=17.3&app_version=2.4&provides_vehicle=true&status=ACTIVE HTTP/1.1
...
Content-Type: application/json
Content-Length: 0
```

## Inicio (*home*)

Nada, solo saludos. Queda abierto a implementación.

## Configurar viaje (*config*)

Para configurar el viaje necesitamos elegir:

- Vehículo (`vehicle.id`): vinculado a la compañía `company.id`
- Ruta (`route_id`): vinculado a la agencia `agency_id` vinculada a la compañía `company.id`
- Recorrido (`shape_id`): vinculado a la ruta `route_id` en `/which-shapes`
- Calendario (`service_id`): vinculado al día actual en `/service-today` (automático)

Luego de seleccionar todo, devuelve la lista de horarios de salida de viajes para la ruta, el recorrido y el calendario elegidos en `/find-trips`.

Al seleccionar el horario, captura el `trip_id` y ya es posible completar la información para comenzar el viaje con un `POST` en `/journey`.

Ya están todos los datos.

## Viaje en progreso (*trip*)

- Si es conductor, muestra los datos del viaje actual y las opciones de terminación.
- Si es despachador, muestra una lista de viajes activos (*polling* de `/journey`)

Opcional (futura versión): indicador y selector de ocupación, que el conductor puede actualizar con un click cada vez que sube o baja gente (si hay cambios significativos).

## Alerta (*alert*)

Pendiente de implementación.
