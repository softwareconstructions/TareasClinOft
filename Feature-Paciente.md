# API de Gestión de Pacientes - Requerimientos Backend

## Descripción General
Sistema de gestión de pacientes para una fundación médica que realiza cirugías oftalmológicas. El backend debe ser desarrollado en .NET Core con Entity Framework para proporcionar endpoints que permitan la gestión completa de pacientes, categorización, priorización y programación de citas.

## Estructura de Base de Datos Requerida

### Entidad Base

#### EntityBase (Entidad Base)
```csharp
public abstract class EntityBase
{
    public bool IsDeleted { get; set; }
    public DateTime CreateAt { get; set; }
    public DateTime UpdateAt { get; set; }
}
```

### Entidades Principales

#### CategoriaPaciente (Categoría de Paciente)
```csharp
public class CategoriaPaciente : EntityBase
{
    public string CodCatPaciente { get; set; } // Primary Key
    public string DescCatPaciente { get; set; }
    public string ColorCatPaciente { get; set; } // Color para identificación visual
    
    // Relaciones
    public ICollection<Paciente> Pacientes { get; set; }
}
```

#### MotivoPrioridad (Motivo de Prioridad)
```csharp
public class MotivoPrioridad : EntityBase
{
    public string CodMotPrioridad { get; set; } // Primary Key
    public string DescMotPrioridad { get; set; }
    
    // Relaciones
    public ICollection<PrioridadPaciente> PrioridadesPaciente { get; set; }
}
```

#### Paciente (Paciente)
```csharp
    public string CodCatPaciente { get; set; } // Foreign Key
    public string Comentario { get; set; } // Campo adicional para comentarios
    
    public CategoriaPaciente CategoriaPaciente { get; set; }
    public ICollection<PrioridadPaciente> PrioridadesPaciente { get; set; }
```

#### PrioridadPaciente (Prioridad de Paciente)
```csharp
public class PrioridadPaciente : EntityBase
{
    public int Id { get; set; } // Primary Key
    public string CodPriorPaciente { get; set; }
    public string CodMotPrioridad { get; set; } // Foreign Key
    public int IdPaciente { get; set; } // Foreign Key
    public string Observaciones { get; set; }
    public int MedicoId { get; set; } // Foreign Key
    public string CodTipoCirugia { get; set; }
    public DateTime FechaEspera { get; set; } // Fecha desde que está en espera
    public DateTime FechaAsignación { get; set; } // Fecha desde que está en espera

    // Relaciones
    public MotivoPrioridad MotivoPrioridad { get; set; }
    public Paciente Paciente { get; set; }
    public Medico Medico { get; set; }
}
```

## Historias de Usuario y Endpoints Requeridos

### US-001: Gestión de Categorías de Pacientes
**Como** administrador del sistema  
**Quiero** gestionar las categorías de pacientes  
**Para** clasificar pacientes según criterios de la fundación

#### Criterios de Aceptación:
- CRUD completo para categorías de pacientes
- Código único para cada categoría
- Asignación de color para identificación visual
- Soft delete implementado

#### Endpoints Requeridos:
```
GET /api/categorias-paciente - Obtener todas las categorías
GET /api/categorias-paciente/{codigo} - Obtener categoría por código
POST /api/categorias-paciente - Crear nueva categoría
PUT /api/categorias-paciente/{codigo} - Actualizar categoría
DELETE /api/categorias-paciente/{codigo} - Eliminar categoría (soft delete)
```

#### Métodos de API Específicos:
- **CRU - UpdateIsDeletePorCodCatPaciente**: Método para soft delete por código de categoría

#### Modelo de Response:
```json
{
  "codCatPaciente": "CAT001",
  "descCatPaciente": "Prioridad Alta",
  "colorCatPaciente": "#FF0000",
  "isDeleted": false,
  "createAt": "2024-01-15T10:30:00Z",
  "updateAt": "2024-01-15T10:30:00Z"
}
```

### US-002: Gestión de Motivos de Prioridad
**Como** personal médico  
**Quiero** gestionar los motivos de prioridad  
**Para** justificar la priorización de pacientes

#### Criterios de Aceptación:
- CRUD completo para motivos de prioridad
- Código único para cada motivo
- Descripción clara del motivo
- Soft delete implementado

#### Endpoints Requeridos:
```
GET /api/motivos-prioridad - Obtener todos los motivos
GET /api/motivos-prioridad/{codigo} - Obtener motivo por código
POST /api/motivos-prioridad - Crear nuevo motivo
PUT /api/motivos-prioridad/{codigo} - Actualizar motivo
DELETE /api/motivos-prioridad/{codigo} - Eliminar motivo (soft delete)
```

#### Métodos de API Específicos:
- **CRU - UpdateIsDeletePorCodMotPrioridad**: Método para soft delete por código de motivo

#### Modelo de Response:
```json
{
  "codMotPrioridad": "MOT001",
  "descMotPrioridad": "Urgencia médica",
  "isDeleted": false,
  "createAt": "2024-01-15T10:30:00Z",
  "updateAt": "2024-01-15T10:30:00Z"
}
```

### US-003: Gestión de Pacientes
**Como** personal administrativo  
**Quiero** Gestionar la categoría de paciente y agregar observaciones
**Para** mantener un registro completo y actualizado

#### Criterios de Aceptación:
- Asignación de categoría de paciente
- Campo de comentarios libre

#### Endpoints Requeridos:
```
PATCH /api/pacientes/updateCategoriaPaciente
PATCH /api/pacientes/updateComentarioPaciente
```

### US-004: Gestión de Prioridades de Pacientes
**Como** administrativo  
**Quiero** gestionar la priorización de los pacientes  
**Para** Priorizar la atención

#### Criterios de Aceptación:
- CRUD completo para prioridades de pacientes
- Asignación de motivo de prioridad
- Observaciones específicas por prioridad
- Ordenamiento por diferentes criterios
- Soft delete implementado

#### Endpoints Requeridos:
```
GET /api/prioridadesPaciente - Obtener todas las prioridades
POST /api/prioridadesPaciente - Crear nueva prioridad
PUT /api/prioridadesPaciente/{id} - Actualizar prioridad
DELETE /api/prioridadesPaciente/{id} - Eliminar prioridad (soft delete)
GET /api/prioridadesPaciente/ordenadas - Obtener prioridades ordenadas
```

## Datos Iniciales Sugeridos

### CategoriaPaciente:
```sql
INSERT INTO CategoriaPaciente VALUES 
('CAT001', 'Prioridad Alta', '#FF0000'),
('CAT002', 'Prioridad Media', '#FFA500'),
('CAT003', 'Prioridad Baja', '#008000')
```

### MotivoPrioridad:
```sql
INSERT INTO MotivoPrioridad VALUES 
('MOT001', 'Urgencia médica'),
('MOT002', 'Complicaciones post-operatorias'),
('MOT003', 'Edad avanzada'),
('MOT004', 'Condición socioeconómica')
```

## Consideraciones Adicionales

- Implementar auditoría completa con campos CreateAt/UpdateAt
- Soft delete en todas las entidades principales
- Validación de integridad referencial
- Logs detallados para operaciones críticas
- Backup automático de datos de pacientes
- Cumplimiento con regulaciones de privacidad de datos médicos
