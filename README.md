# API de Worklist DICOM

Documentación de la API REST para la gestión de worklists DICOM en el sistema multitenant.

## Autenticación

Todas las peticiones requieren autenticación mediante API Key en el header:

```
X-API-KEY: your_api_key_here
```

## URL Base

La API está configurada en la ruta: `/v1/worklists`

## Endpoints

### Obtener lista de worklists

```
GET /v1/worklists
```

**Filtros disponibles:**

Se pueden usar filtros mediante parámetros GET con la sintaxis de ActiveDataFilter de Yii2.

**Ejemplo de respuesta exitosa (200 OK):**

```json
{
  "data": [
    {
      "id": 1,
      "accession_number": "2025200100001",
      "patient_id": "PID12345",
      "surname": "GONZALEZ",
      "forename": "JUAN",
      "sex": "M",
      "date_of_birth": "1980-01-15",
      "modality": "CT",
      "exam_date_and_time": "2025-07-23 09:00:00",
      "study_instance_uid": "1.2.826.0.1.3680043.10.543.1721745600.123456",
      "status": "SCHEDULED",
      "exam_description": "CT ABDOMEN CON CONTRASTE",
      "referring_physician": "DR. SMITH",
      "performing_physician": "DR. JONES",
      "exam_room": "SALA 1",
      "hospital_name": "Hospital Central",
      "scheduled_aet": "CT_SCANNER_1",
      "created_at": 1721745600,
      "updated_at": 1721745600
    }
  ]
}
```

### Obtener un worklist específico

```
GET /v1/worklists/{id}
```

**Parámetros de ruta:**

- `id`: ID único del worklist

**Ejemplo de respuesta exitosa (200 OK):**

```json
{
  "data": {
    "id": 1,
    "accession_number": "2025200100001",
    "patient_id": "PID12345",
    "surname": "GONZALEZ",
    "forename": "JUAN",
    "sex": "M",
    "date_of_birth": "1980-01-15",
    "modality": "CT",
    "exam_date_and_time": "2025-07-23 09:00:00",
    "study_instance_uid": "1.2.826.0.1.3680043.10.543.1721745600.123456",
    "status": "SCHEDULED"
  }
}
```

### Crear un nuevo worklist

```
POST /v1/worklists
```

**Cuerpo de la petición (JSON):**

```json
{
  "patient_id": "PID12345",
  "surname": "GONZALEZ",
  "forename": "JUAN",
  "sex": "M",
  "date_of_birth": "1980-01-15",
  "modality": "CT",
  "exam_date_and_time": "2025-07-23 09:00:00",
  "exam_description": "CT ABDOMEN CON CONTRASTE",
  "referring_physician": "DR. SMITH",
  "scheduled_aet": "CT_SCANNER_1"
}
```

**Campos requeridos (según rules del modelo):**

- `patient_id`
- `surname`
- `forename`
- `modality`
- `exam_date_and_time`
- `scheduled_aet`

**Campos opcionales:**

- `title`
- `sex`
- `date_of_birth`
- `referring_physician`
- `performing_physician`
- `exam_room`
- `exam_description`
- `procedure_id`
- `procedure_step_id`
- `hospital_name`

**Campos generados automáticamente:**

- `accession_number`: Se genera automáticamente con formato AAAAJJJNNNNN
- `study_instance_uid`: Se genera automáticamente si no se proporciona
- `status`: Por defecto es 'SCHEDULED'
- `institution_uid`: Se asigna automáticamente según el tenant autenticado

### Actualizar un worklist

```
PUT /v1/worklists/{id}
```

**Parámetros de ruta:**

- `id`: ID único del worklist

**Cuerpo de la petición (JSON):**

```json
{
  "status": "IN_PROGRESS",
  "performing_physician": "DR. RODRIGUEZ",
  "exam_room": "SALA 2"
}
```

**Ejemplo de respuesta exitosa (200 OK):**

```json
{
  "data": {
    "id": 1,
    "status": "IN_PROGRESS",
    "performing_physician": "DR. RODRIGUEZ",
    "exam_room": "SALA 2",
    "updated_at": 1721745700
  }
}
```

### Eliminar un worklist

```
DELETE /v1/worklists/{id}
```

**Parámetros de ruta:**

- `id`: ID único del worklist

**Ejemplo de respuesta exitosa (204 No Content):**

```json
{
  "message": "Elemento de worklist eliminado exitosamente."
}
```

## Códigos de estado HTTP

- `200 OK`: Petición exitosa
- `201 Created`: Recurso creado exitosamente
- `204 No Content`: Recurso eliminado exitosamente
- `401 Unauthorized`: API Key inválida o faltante
- `404 Not Found`: Recurso no encontrado o no pertenece al tenant
- `422 Unprocessable Entity`: Error de validación
- `500 Internal Server Error`: Error del servidor

## Validación de datos

La API valida los siguientes aspectos según las reglas del modelo Worklist:

### Campos de longitud máxima:

- `accession_number`: 16 caracteres
- `institution_uid`, `patient_id`, `surname`, `forename`, `referring_physician`, `performing_physician`, `study_instance_uid`, `procedure_id`, `procedure_step_id`, `scheduled_aet`: 64 caracteres
- `title`: 16 caracteres
- `sex`: 1 carácter
- `modality`: 16 caracteres
- `exam_room`: 32 caracteres
- `exam_description`: 128 caracteres
- `hospital_name`: 128 caracteres
- `status`: 16 caracteres

### Valores permitidos:

- `status`: 'SCHEDULED', 'IN_PROGRESS', 'COMPLETED', 'CANCELED'
- `modality`: 'CR', 'CT', 'MR', 'US', 'XA', 'RF', 'DX', 'MG', 'NM', 'PT'
- `sex`: Cualquier carácter único

### Validaciones especiales:

- `accession_number` debe ser único por institución
- `institution_uid` debe existir en la tabla de instituciones
- Los campos de fecha y hora se validan automáticamente

### Campos generados automáticamente:

- `accession_number`: Formato AAAAJJJNNNNN (Año + Día del año + Secuencial)
- `study_instance_uid`: OID base + timestamp + número aleatorio
- `status`: 'SCHEDULED' por defecto
- `created_at` y `updated_at`: Timestamps automáticos

## Seguridad y Multitenant

- **Autenticación**: Mediante API Key en header `X-API-KEY`
- **Autorización**: Solo se pueden acceder a worklists de la institución del usuario autenticado
- **Filtrado automático**: Todos los queries se filtran por `institution_uid`
- **Validación de pertenencia**: Al acceder a un worklist específico, se verifica que pertenezca al tenant

## Estructura de la base de datos

### Tabla: `worklist`

**Campos principales:**

- `id`: Primary key auto-incremental
- `institution_uid`: UID de la institución (multitenant)
- `accession_number`: Número de acceso único por institución
- `patient_id`: ID del paciente
- `surname`: Apellido del paciente
- `forename`: Nombre del paciente
- `modality`: Modalidad DICOM
- `exam_date_and_time`: Fecha y hora del examen
- `study_instance_uid`: UID único del estudio
- `status`: Estado del worklist
- `scheduled_aet`: AE Title del equipo programado

**Índices optimizados:**

- Índice único: `institution_uid` + `accession_number`
- Índices individuales: `patient_id`, `study_instance_uid`, `modality`, `exam_date_and_time`, `status`, `hospital_name`, `scheduled_aet`
- Índices compuestos: `institution_uid` + `status` + `exam_date_and_time`, `institution_uid` + `modality`
