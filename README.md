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

**Parámetros de consulta opcionales:**

- `status`: Filtrar por estado (SCHEDULED, IN_PROGRESS, COMPLETED, CANCELED)
- `patient_id`: Filtrar por ID de paciente
- `modality`: Filtrar por modalidad (CT, MR, US, etc.)
- `exam_date_and_time`: Filtrar por fecha y hora del examen
- `surname`: Filtrar por apellido del paciente
- `forename`: Filtrar por nombre del paciente

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

**Campos requeridos:**

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
- `404 Not Found`: Recurso no encontrado
- `422 Unprocessable Entity`: Error de validación
- `500 Internal Server Error`: Error del servidor

## Validación de datos

La API valida los siguientes aspectos:

### Longitud máxima de campos:

- `patient_id`, `surname`, `forename`: 64 caracteres
- `referring_physician`, `performing_physician`: 64 caracteres
- `exam_description`: 128 caracteres
- `hospital_name`: 128 caracteres
- `exam_room`: 32 caracteres
- `title`: 16 caracteres
- `sex`: 1 carácter

### Valores permitidos:

- `status`: SCHEDULED, IN_PROGRESS, COMPLETED, CANCELED
- `modality`: CR, CT, MR, US, XA, RF, DX, MG, NM, PT
- `sex`: M (Masculino), F (Femenino), O (Otro)

### Formato de fechas:

- `date_of_birth`: YYYY-MM-DD
- `exam_date_and_time`: YYYY-MM-DD HH:MM:SS

### Campos generados automáticamente:

- `accession_number`: Se genera automáticamente un número único
- `study_instance_uid`: Se genera automáticamente si no se proporciona
- `status`: Por defecto es 'SCHEDULED'
- `created_at` y `updated_at`: Fechas de creación y actualización

## Seguridad

- **Autenticación**: Todas las peticiones requieren una API Key válida
- **Autorización**: Solo se puede acceder a los worklists de su institución
- **HTTPS**: Se recomienda usar conexiones seguras en producción

## Limitaciones

- **Paginación**: Los resultados están limitados a 20 elementos por página
- **Filtros**: Se pueden aplicar múltiples filtros simultáneamente
- **Unicidad**: El número de acceso debe ser único dentro de cada institución
