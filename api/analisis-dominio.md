# ANÁLISIS DEL DOMINIO - SISTEMA DE RESERVAS GENÉRICO

## 1.Tablas de la BBDD que usaré

Para el CRUD de reservas necesitaré las tablas:

- `RESERVA` (principal)
- `USUARIO` (relación por FK)
- `RECURSO` (relación por FK)

## 2. Campos de la tabla RESERVA y mapeo de la API

| Campo          | Tipo        | ¿En API? | Razón                                    |
| -------------- | ----------- | -------- | ---------------------------------------- |
| idReserva      | INT         | SÍ       | Para identificar cada reserva            |
| fechaReserva   | DATE        | SÍ       | Saber en qué fecha es la reserva         |
| horaInicio     | TIME        | SÍ       | Saber cuándo empieza la reserva          |
| horaFin        | TIME        | SÍ       | Saber cuándo acaba la reserva            |
| numPlazas      | INT         | SÍ       | Saber para cuánta gente es la reserva    |
| Motivo         | VARCHAR     | SÍ       | Descripción de la reserva, opcional      |
| Observaciones  | VARCHAR     | SÍ       | Observaciones sobre la reserva, opcional |
| idUsuario      | VARCHAR(FK) | SÍ       | Saber quién reserva                      |
| idRecurso      | VARCHAR(FK) | SÍ       | Saber qué reserva                        |
| estado         | ENUM        | SÍ       | pendiente/confirmada/cancelada           |
| creada_en      | TIMESTAMP   | SÍ       | Cuándo se creó                           |
| actualizada_en | TIMESTAMP   | SÍ       | Última modificación                      |
| deleted_at     | TIMESTAMP   | NO       | solo información interna                 |

**Campos auto-generados por el backend:**

De estos campos, el backend generará automáticamente:

- `idReserva` (AUTO_INCREMENT)
- `estado` (por defecto `'pendiente'`)
- `creada_en`, `actualizada_en`, `deleted_at` (timestamps automáticos)

**Información adicional que incluiré** (JOIN con tabla `RECURSO`):

Además de los campos de la tabla `RESERVA`, en las respuestas de la API incluiré el nombre del recurso para facilitar la lectura. Para ello, haré un JOIN con la tabla `RECURSO` y expondré el campo:

- `nombreRecurso`: Para mostrar "Recurso C" en lugar de solo el ID

## 3. Validaciones principales

Al crear/modificar reservas validaré:

**Campos obligatorios:**

- `idRecurso`, `fechaReserva`, `horaInicio`, `horaFin`, `numPlazas`

**Lógica:**

- `horaInicio` < `horaFin`
- `fechaReserva` >= HOY (no permitir fechas pasadas)
- `numPlazas` >= 1
- `numPlazas` <= capacidad del recurso
- No puede haber solapamiento de horarios en el mismo recurso

**Estados permitidos:**

Los estados los gestionará el backend, pero las opciones válidas serán:

- Solo: `'pendiente'`, `'confirmada'`, `'cancelada'`
- Al crear siempre `'pendiente'`

## 4. Ejemplos de JSON

**REQUEST** (crear reserva):

La petición que el cliente enviaría para crear una reserva podría ser:

```json
{
  "idRecurso": 3,
  "fechaReserva": "2026-07-27",
  "hora_inicio": "18:00",
  "hora_fin": "19:30",
  "numPlazas": 7,
  "motivo": "Cumpleaños",
  "observaciones": "Alergia a anacardos"
}
```

**RESPONSE** (reserva creada):

La respuesta que el backend devolvería al crear la reserva incluiría los campos generados automáticamente y el nombre del recurso:

```json
{
  "id": 12345,
  "idUsuario": 1536,
  "idRecurso": 3,
  "nombreRecurso": "Recurso C",
  "fecha_inicio": "2026-07-27",
  "hora_inicio": "18:00",
  "hora_fin": "19:30",
  "num_asistentes": 7,
  "motivo": "Cumpleaños",
  "observaciones": "Alergia a anacardos",
  "estado": "pendiente",
  "creada_en": "2026-03-02T20:30:00Z",
  "actualizada_en": "2026-03-02T20:30:00Z"
}
```

## Conclusión

Necesito 13 campos de la tabla reserva (expongo 12 en la API) y uno de la tabla recurso. Aplicaré 5 validaciones básicas para garantizar integridad de datos. El MVP permitirá crear, listar, modificar y eliminar reservas.

## Imagen del SwaggerUI

![Imagen de SwaggerUI](./CapturaSwagger.png)
