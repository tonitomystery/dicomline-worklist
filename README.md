# API de Worklist DICOM

Documentación de la API REST para la gestión de worklists DICOM en el sistema multitenant.

## Autenticación

Todas las peticiones requieren autenticación mediante API Key:

```
X-API-KEY: your_api_key_here
```

## Endpoints

### Obtener lista de worklists

```
GET /v1/worklists
```

**Parámetros de consulta:**
- `status` (opcional): Filtrar por estado (SCHEDULED, IN_PROGRESS, COMPLETED, CANCELED)
- `patient_id` (opcional): Filtrar por ID de paciente
- `modality` (opcional): Filtrar por modalidad (CT, MR, etc.)
- `date_from` (opcional): Filtrar por fecha desde (formato YYYY-MM-DD)
- `date_to` (opcional): Filtrar por fecha hasta (formato YYYY-MM-DD)

**Ejemplo de respuesta exitosa (200 OK):**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "accession_number": "ACC12345678",
      "study_instance_uid": "1.2.840.113619.2.1.9999.1",
      "patient_id": "PID12345",
      "patient_name": "GONZALEZ^JUAN",
      "patient_birth_date": "1980-01-15",
      "patient_sex": "M",
      "modality": "CT",
      "scheduled_procedure_step_start_date": "2025-07-23",
      "scheduled_procedure_step_start_time": "09:00:00",
      "status": "SCHEDULED",
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

### Crear un nuevo worklist

```
POST /api/v1/worklists
```

**Cuerpo de la petición (JSON):**
```json
{
  "patient_id": "PID12345",
  "patient_name": "GONZALEZ^JUAN",
  "patient_birth_date": "1980-01-15",
  "patient_sex": "M",
  "modality": "CT",
  "scheduled_procedure_step_start_date": "2025-07-23",
  "scheduled_procedure_step_start_time": "09:00:00",
  "study_description": "CT ABDOMEN CON CONTRASTE"
}
```

**Campos requeridos:**
- `patient_id`
- `patient_name`
- `modality`
- `scheduled_procedure_step_start_date`
- `scheduled_procedure_step_start_time`

### Actualizar un worklist

```
PUT /api/v1/worklists/{id}
```

**Parámetros de ruta:**
- `id`: ID único del worklist

**Cuerpo de la petición (JSON):**
```json
{
  "status": "IN_PROGRESS",
  "study_instance_uid": "1.2.840.113619.2.1.9999.1"
}
```

### Eliminar un worklist

```
DELETE /api/v1/worklists/{id}
```

**Parámetros de ruta:**
- `id`: ID único del worklist

## Códigos de estado HTTP

- `200 OK`: Petición exitosa
- `201 Created`: Recurso creado exitosamente
- `400 Bad Request`: Error en los datos de la petición
- `401 Unauthorized`: No autenticado
- `403 Forbidden`: No autorizado para acceder al recurso
- `404 Not Found`: Recurso no encontrado
- `500 Internal Server Error`: Error del servidor

## Validación de datos

La API valida los siguientes aspectos de los datos DICOM:
- Formato correcto de fechas (YYYY-MM-DD)
- Formato correcto de horas (HH:MM:SS)
- Valores permitidos para campos enumerados (ej: sexo, modalidad)
- Campos requeridos según el tipo de operación
- Unicidad de identificadores (accession_number, study_instance_uid)

## Seguridad

- Todas las conexiones deben usar HTTPS

