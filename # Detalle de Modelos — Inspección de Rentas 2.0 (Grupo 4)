# Detalle de Modelos — Inspección de Rentas 2.0 (Grupo 4)

Versión: 0.1 (autogenerado)
Fecha: 6 de noviembre de 2025

Este archivo contiene tablas con los campos principales extraídos de los modelos Sequelize del backend y un diagrama de arquitectura mermaid. Está pensado para copiar/pegar en la plantilla de diseño.

---

## Diagrama de arquitectura (Mermaid)

```mermaid
flowchart LR
  Mobile[Mobile App (inspector)] -->|Sube evidencias / solicita inspección| Backend[Backend API (Node.js / Express)]
  Web[Web App (Next.js)] -->|Consulta / valida / genera reportes| Backend
  Backend -->|Persistencia| DB[(Postgres)]
  Backend -->|Archivos| Storage[S3 / GCS]
  Backend -->|Auth| Auth0[(Auth0)]
  Backend -->|Servicio IA| AI[Servicio IA: AI_API_URL]
  Backend -->|ERP / Firma| ERP[ERP / Firmagob]
  Backend -->|Mensajería| FCM[Firebase / Notificaciones]
  subgraph Infra
    DB
    Storage
    Auth0
    AI
    ERP
    FCM
  end
```

> Nota: este diagrama es de alto nivel. Si hay un diagrama específico que quieres conservar, indícalo y lo reemplazo.

---

## Modelos

Para cada modelo se incluye: campo — tipo — allowNull — comentario / nota.

### Branch (`src/models/branch.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID (PK) | false | default UUIDV4 |
| company_id | UUID | false | FK -> companies.id |
| location_id | UUID | false | FK -> locations.id |
| municipal_code | STRING(50) | false | único |
| branch_name | STRING(255) | false | |
| floor_office | STRING(50) | true | |
| created_at | DATE | - | timestamps |
| updated_at | DATE | - | timestamps |
| deleted_at | DATE | true | paranoid |


### CameraCalibration (`src/models/camera-calibration.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| user_id | UUID | false | unique, FK -> users.id |
| camera_matrix | JSONB | false | |
| dist_coefs | JSONB | false | |
| rms_error | FLOAT | false | |
| rms_relative | FLOAT | false | |
| focal_length | JSONB | false | |
| optical_center | JSONB | false | |
| quality | STRING | true | |
| quality_level | STRING | true | |
| quality_recommendation | STRING | true | |
| num_images_used | INTEGER | true | |
| total_images_processed | INTEGER | true | |
| successful_images | INTEGER | true | |
| image_size | JSONB | true | |
| calibration_config | JSONB | true | |
| timestamp | DATE | true | |
| distortion_model | STRING | true | |


### Company (`src/models/company.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID (PK) | false | default UUIDV4 |
| rut | STRING(20) | false | único, validación RUT |
| legal_name | STRING(255) | false | |
| trade_name | STRING(255) | true | |
| business_activity | TEXT | false | |
| legal_representative_rut | STRING(20) | false | validación RUT |
| legal_representative_name | STRING(255) | false | |
| created_at | DATE | - | timestamps |
| updated_at | DATE | - | timestamps |
| deleted_at | DATE | true | paranoid |


### Fine (`src/models/fine.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID (PK) | false | |
| inspection_request_id | UUID | false | único, FK -> inspection_requests.id |
| status | ENUM | false | valores: pending_authorization, authorized, sent |
| infraction_number | INTEGER | false | unique, autoincrement |
| violation_date | DATEONLY | false | |
| violation_time | TIME | false | |
| violation_location_street | STRING(500) | false | |
| violation_location_number | STRING(10) | false | |
| violation_type | ENUM | true | (varios) |
| violation_type_other | STRING(500) | true | |
| violation_property | ENUM | false | commercial/office/residence/o.b.n.u.p |
| court_type | ENUM | false | first_court/second_court |
| court_location | STRING(255) | true | default valor |
| hearing_date | DATEONLY | false | |
| hearing_time | TIME | false | |
| reception_* fields | VARIOS | true/false | campos de recepción |
| formulario_denuncio, salida_denuncio, etc. | STRING(500) | true | paths a PDFs |
| authorized_by, authorized_at, sent_at | STRING/DATE | true | auditoría |


### InspectionMediaCategory (`src/models/inspection-media-category.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| name | STRING(100) | false | |
| slug | STRING(100) | false | único |
| description | TEXT | true | |
| is_active | BOOLEAN | false | default true |


### InspectionMedia (`src/models/inspection-media.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| inspection_id | UUID | false | FK -> inspection_requests.id |
| inspection_result_id | STRING(64) | true | |
| storage_key | TEXT | false | ruta en S3/GCS |
| mime_type | STRING(64) | false | |
| byte_size | BIGINT | false | |
| sha256 | STRING(64) | false | |
| width | INTEGER | false | |
| height | INTEGER | false | |
| captured_at | DATE | true | |
| received_at | DATE | true | |
| lat, lng, accuracy, altitude, heading, speed | DOUBLE/DOUBLE | true | geolocalización |
| provider | STRING(32) | true | |
| polygon_json, exif_json, tags, ai_result_json | JSONB | true | datos AI/EXIF/etiquetas |
| notes | TEXT | true | |
| client_media_id | UUID | true | unique |
| status | ENUM | false | valores: pending, UPLOADED, ANALYZED, validated_by_admin, cancelled, reported_by_inspector |


### InspectionReason (`src/models/inspection-reason.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| name | STRING(255) | false | |
| description | TEXT | true | |
| default_priority | ENUM | false | urgent/high/medium/low |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


### InspectionRequest (`src/models/inspection-request.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| branch_id | UUID | false | FK -> branches.id |
| reason_id | UUID | false | FK -> inspection_reasons.id |
| inspector_id | STRING(255) | true | referencia a user/id_auth0 |
| request_date | DATEONLY | false | |
| priority | ENUM | false | urgent/high/medium/low |
| initial_notes | TEXT | true | |
| attachments | JSONB | true | |
| request_status | ENUM | false | pending/assigned/in_progress/... |
| request_type | ENUM | false | first/second/followup/extraordinary |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


### InspectionResult (`src/models/inspection-result.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| request_id | UUID | false | único, FK -> inspection_requests.id |
| assignment_date | DATE | true | |
| inspection_date | DATE | true | |
| court_summons_date | DATE | true | |
| municipal_court | ENUM | true | JPL1/JPL2 |
| infraction_number | STRING(50) | true | |
| inspection_summary | TEXT | true | |
| notes | TEXT | true | |
| requires_followup | BOOLEAN | true | default false |
| result_documents | JSONB | true | |
| inspection_type | ENUM | false | initial/second/follow_up/extraordinary |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


### InspectorLocation (`src/models/inspector-location.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| user_id | STRING(255) | false | referencia a inspector/aut0 |
| latitude | DECIMAL(10,7) | false | |
| longitude | DECIMAL(10,7) | false | |
| timestamp | DATE | false | default NOW |


### IrregularInspection (`src/models/irregular-inspection.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| inspector_id | STRING(255) | false | |
| municipal_code | STRING | true | |
| recorded_at | DATE | false | |
| company_name | STRING(255) | true | |
| company_rut | STRING(20) | true | validación RUT |
| legal_rep_name | STRING(255) | true | |
| legal_rep_rut | STRING(20) | true | validación RUT |
| activity_description | TEXT | false | |
| address | STRING(255) | false | |
| latitude | DECIMAL | true | |
| longitude | DECIMAL | true | |
| notes | TEXT | true | |
| attachments | JSONB | true | |
| regularization_status | ENUM | false | pending_review/reviewed/formal_request_created |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


### Location (`src/models/location.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| address | STRING(255) | false | único |
| latitude | DECIMAL(10,8) | false | |
| longitude | DECIMAL(11,8) | false | |
| postal_code | STRING(10) | true | |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


### NotificationToken (`src/models/notification-token.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | INTEGER | false | PK autoincrement |
| auth0_user_id | STRING(255) | false | |
| fcm_token | TEXT | false | |
| platform | STRING(20) | false | web/ios/android |
| created_at, updated_at | DATE | false | |

Índices: unique (auth0_user_id, platform)


### Signature (`src/models/signature.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| user_id | STRING(255) | false | único |
| storage_key | TEXT | false | ruta S3/GCS |
| file_name | STRING(255) | false | |
| mime_type | STRING(50) | false | default image/png |
| byte_size | BIGINT | false | |
| sha256 | STRING(100) | true | |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


### SystemSetting (`src/models/system-setting.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | UUID | false | PK |
| key | STRING(100) | false | único |
| value | JSONB | false | |
| description | TEXT | true | |
| last_modified_at | DATE | - | |
| modified_by | STRING(255) | false | referencia a User |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


### UserNotification (`src/models/user-notification.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id | INTEGER | false | PK autoincrement |
| auth0_user_id | STRING | false | |
| title | STRING | false | |
| body | STRING | false | |
| data | JSONB | true | |
| read_at | DATE | true | |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


### User (`src/models/user.model.js`)

| Campo | Tipo | allowNull | Notas |
|---|---|---:|---|
| id_auth0 | STRING(255) | false | PK (id en Auth0) |
| full_name | STRING(255) | false | |
| email | STRING(255) | false | único |
| phone_number | STRING(20) | true | solo dígitos |
| last_login | DATE | true | |
| created_at, updated_at, deleted_at | DATE | - | timestamps/paranoid |


---

## Observaciones y siguientes pasos

- He extraído los campos principales y tipos desde los modelos del backend. Si quieres, puedo generar una tabla más detallada con constraints (índices, unique, defaultValue) y ejemplos JSON para modelos complejos (por ejemplo `InspectionMedia`, `CameraCalibration`).

- Puedo añadir estas tablas directamente al `Borrador_Documentacion_Inspeccion_Rentas_Grupo4.md` o dejar este archivo separado (recomendado para revisión antes de pegar en Word).

- Próximo paso sugerido: generar `.env.example` con las variables detectadas y un archivo de ejemplos curl (o Postman collection) para los endpoints principales.

Si quieres que haga eso ahora, dime y procedo (genero `.env.example` y ejemplos curl + Postman).
