# hello-world
primera tarea
Hi, my name is Rodrigo Rencoret, i am 20 years old and im studying engineering.
# REPORTE DE VIOLACIONES DE CLEAN CODE
## Proyecto Shin-Megami-Tensei

### 📋 Resumen de Puntuaciones
- **Meaningful Names (Cap. 2):** -1.0 puntos
- **Functions (Cap. 3):** -2.0 puntos  
- **Objects and Data Structures (Cap. 6):** -2.0 puntos
- **Classes (Cap. 10):** -1.5 puntos
- **Patrón MVC:** -0.5 puntos
- **TOTAL:** -7.0 puntos

---

## 🔴 CAPÍTULO 2 - MEANINGFUL NAMES (-1.0 puntos)

### Game.cs
| Línea | Nombre Actual | Problema | Nombre Sugerido |
|-------|---------------|----------|-----------------|
| Múltiples | `datosEquipo` | Español/Inglés mezclado | `teamData` o `teamUnits` |
| 18 | `_teamsFolder` | Inconsistente con nomenclatura | `_teamsDirectory` |
| 119-123 | `condicion1`, `condicion2`, `condicion3` | Nombres genéricos no descriptivos | `isValidInput`, `isNegativeNumber`, `exceedsMaxFiles` |
| 159 | `var (jugador1Data, jugador2Data)` | Español/Inglés mezclado | `var (player1Data, player2Data)` |
| 279 | `data` en `UnitVerifierData` | Nombre genérico | `verificationData` |
| 523 | `samuraiJson`, `monstrersJson` | Typo en "monsters" | `monstersJson` |

### Jugador.cs
| Línea | Nombre Actual | Problema | Nombre Sugerido |
|-------|---------------|----------|-----------------|
| 17-20 | `UnidadActiva_1`, `UnidadActiva_2`, etc. | Español/números, no descriptivo | `PrimarySamurai`, `FirstMonster`, `SecondMonster`, `ThirdMonster` |
| 25-30 | `NumeroFullTurns`, `NumeroBlinkingTurns` | Español/Inglés mezclado | `FullTurnsCount`, `BlinkingTurnsCount` |
| 313-317 | Variables `count`, `i` | Nombres genéricos en contexto complejo | `activeUnitsCount`, `unitIndex` |

### Ronda.cs
| Línea | Nombre Actual | Problema | Nombre Sugerido |
|-------|---------------|----------|-----------------|
| 78 | `cond` | Abreviación no descriptiva | `shouldContinueTurn` |
| 57 | `numero_ronda` | Guión bajo inconsistente | `roundNumber` |

### ClasesAuxiliares
| Archivo | Problema | Solución |
|---------|----------|----------|
| Múltiples | Variables de una letra (`h`, `s`, `u`, `m`) | Usar nombres descriptivos como `skill`, `samurai`, `unit`, `monster` |

---

## 🔴 CAPÍTULO 3 - FUNCTIONS (-2.0 puntos)

### Funciones Demasiado Largas

#### Game.cs
| Función | Líneas | Problema | Solución |
|---------|--------|----------|----------|
| `PrepararJugadores()` | ~80 | Múltiples responsabilidades | Dividir en: `LoadTeamFiles()`, `CreatePlayersFromData()`, `ValidateTeamData()` |
| `VerifyUnit()` | ~40 | Validación compleja | Extraer: `ValidateUnitFormat()`, `CheckDuplicates()`, `ValidateSamuraiSkills()` |
| `CrearSamurai()` | ~25 | Constructor manual muy largo | Usar patrón Builder o Factory |
| `CrearMonstruo()` | ~30 | Constructor manual muy largo | Usar patrón Builder o Factory |
| `RevisarEquiposSeanValidos()` | ~50 | Lógica de validación compleja | Dividir en validaciones específicas |

#### Jugador.cs
| Función | Líneas | Problema | Solución |
|---------|--------|----------|----------|
| `ConsumirTurno()` | ~80 | Switch case muy largo | Usar patrón Strategy para cada tipo de afinidad |
| `ConsumirTurnoPorAfinidad()` | ~30 | Switch case complejo | Extraer cada caso a método específico |

#### InvocadorMonstruos.cs
| Función | Líneas | Problema | Solución |
|---------|--------|----------|----------|
| `SeleccionarMonstruoInvocar()` | ~50 | Comentario "ACORTAR" presente | Dividir en: `GetAvailableMonsters()`, `ShowMonsterMenu()`, `ProcessSelection()` |
| `InvocarMonstruo()` | ~60 | Múltiples responsabilidades | Separar lógica de: posicionamiento, validación, efectos |

### Funciones con Demasiados Parámetros

#### Constructores Problemáticos
```csharp
// Samurai.cs - 17 parámetros
public Samurai(string name, int hp, int mp, int str, int skl, int mag, int spd, int lck, 
               string phys, string gun, string fire, string ice, string elec, string force, 
               string light, string dark, List<Habilidad> habilidades, View view)

// Monster.cs - 17 parámetros  
public Monster(string name, int hp, int mp, int str, int skl, int mag, int spd, int lck,
               string phys, string gun, string fire, string ice, string elec, string force,
               string light, string dark, List<Habilidad> habilidades, View view)
```

**Solución:** Usar objetos de configuración o patrón Builder.

---

## 🔴 CAPÍTULO 6 - OBJECTS AND DATA STRUCTURES (-2.0 puntos)

### Violaciones de Encapsulación

#### Unidad.cs
```csharp
// ❌ PROBLEMA: Todas las propiedades son públicas
public int HP { get; set; }
public int MP { get; set; }
public int Str { get; set; }
// ... etc

// ✅ SOLUCIÓN: Encapsular con métodos
public int HP { get; private set; }
public void TakeDamage(int amount) { /* lógica */ }
public void Heal(int amount) { /* lógica */ }
```

#### Samurai.cs y Monster.cs
**Problema:** Redeclaran TODAS las propiedades ya definidas en `Unidad`
```csharp
// ❌ En Samurai.cs - líneas 8-32
public string Name { get; set; }  // Ya existe en Unidad
public int HP { get; set; }       // Ya existe en Unidad
// ... todas las propiedades duplicadas

// ❌ En Monster.cs - líneas 9-33  
// Misma duplicación
```

**Solución:** Eliminar propiedades duplicadas, usar solo las de la clase base.

#### HabilidadInfo.cs
```csharp
// ❌ PROBLEMA: Sin validación ni encapsulación
public string name { get; set; }
public int cost { get; set; }

// ✅ SOLUCIÓN: Validar en setters
public string Name 
{ 
    get => _name; 
    set => _name = !string.IsNullOrEmpty(value) ? value : throw new ArgumentException();
}
```

### Estructura vs Objetos
**Problema:** Las clases mezclan ser estructuras de datos (getters/setters públicos) con objetos (métodos de comportamiento).

---

## 🔴 CAPÍTULO 10 - CLASSES (-1.5 puntos)

### Violaciones del Single Responsibility Principle

#### Game.cs (645 líneas)
**Responsabilidades múltiples:**
1. **Manejo de archivos:** `LeerArchivoDeEquipo()`, `CargarSamuraisDesdeJson()`
2. **Creación de unidades:** `CrearSamurai()`, `CrearMonstruo()`
3. **Validación:** `RevisarEquiposSeanValidos()`, `VerifyUnit()`
4. **Control de flujo:** `Play()`, `JugarRondas()`
5. **UI:** Múltiples llamadas a `_view.WriteLine()`

**Solución:** Dividir en:
- `GameController`
- `TeamFileManager` 
- `UnitFactory`
- `TeamValidator`

#### Ronda.cs (973 líneas)
**Responsabilidades múltiples:**
1. **Estado del juego:** Manejo de turnos
2. **UI:** Mostrar menús y estado
3. **Validación:** Verificar ganadores
4. **Coordinación:** Entre jugadores y unidades

#### Jugador.cs (361 líneas)  
**Responsabilidades múltiples:**
1. **Estado del jugador:** Unidades, stats
2. **Lógica de turnos:** Sistema complejo de turnos
3. **Validación:** `PuedeContinuarJugando()`

### Clases Anidadas Problemáticas

#### Game.cs
```csharp
// ❌ Líneas 183-220: Clase anidada que debería estar separada
public class SeparadorDatosJugadores
{
    // 40 líneas de código que deberían estar en archivo separado
}

// ❌ Líneas 275-285: Clase de datos interna
private class UnitVerifierData 
{
    // Debería ser una clase independiente
}
```

### Métodos Demasiado Largos en Clases

| Clase | Método | Líneas | Problema |
|-------|--------|--------|----------|
| Game | `PrepararJugadores()` | ~80 | Hace demasiadas cosas |
| Ronda | `EjecutarCicloDeTurnos()` | ~50 | Lógica compleja en un solo método |
| Jugador | `ConsumirTurno()` | ~80 | Switch case gigante |

---

## 🔴 NO IMPLEMENTA MVC (-0.5 puntos)

### Problemas Arquitecturales

#### Mezclado de Responsabilidades
```csharp
// ❌ En Game.cs - Lógica de negocio con UI
public void Play()
{
    // Lógica de negocio
    List<Jugador> jugadores = PrepararJugadores();
    
    // UI mezclada
    _view.WriteLine("Error: Al menos uno de los jugadores es nulo.");
    
    // Más lógica de negocio  
    Ronda Partida = new Ronda(PrimerJugador, SegundoJugador, _view);
}
```

#### Ausencia de Controladores
**Problema:** No hay separación clara entre:
- **Modelo:** Clases de dominio (`Jugador`, `Unidad`, `Habilidad`)
- **Vista:** Interface de usuario (`View`)
- **Controlador:** Lógica de coordinación

**Actual:** Todo mezclado en `Game.cs` que actúa como "god object"

### Solución MVC Recomendada

```
📁 Controllers/
   ├── GameController.cs      // Control principal del juego
   ├── BattleController.cs    // Control de batallas/rondas  
   ├── UnitController.cs      // Control de unidades
   └── MenuController.cs      // Control de menús

📁 Models/
   ├── Game.cs               // Solo estado del juego
   ├── Player.cs             // Solo estado del jugador
   ├── Unit.cs               // Solo estado de unidades
   └── Battle.cs             // Solo estado de batalla

📁 Views/
   ├── IGameView.cs          // Interface para vistas
   ├── ConsoleView.cs        // Implementación consola
   └── GuiView.cs            // Implementación GUI
```

#### Patrón Observer Faltante
**Problema:** Comunicación directa entre capas
**Solución:** Implementar eventos para notificaciones entre Model-View-Controller

---

## 📊 RESUMEN PRIORIZADO DE CORRECCIONES

### 🔥 **CRÍTICO (Impacto Alto)**
1. **Dividir `Game.cs`** en múltiples clases especializadas
2. **Refactorizar funciones largas** (>20 líneas) en funciones más pequeñas
3. **Encapsular propiedades** en `Unidad`, `Samurai`, `Monster`
4. **Eliminar duplicación** en constructores de clases derivadas

### ⚠️ **IMPORTANTE (Impacto Medio)**
5. **Implementar patrón MVC** separando responsabilidades
6. **Renombrar variables** con nombres descriptivos en inglés
7. **Extraer clases anidadas** a archivos separados
8. **Reducir parámetros** en constructores usando builders

### 💡 **MEJORAS (Impacto Bajo)**
9. **Aplicar patrón Strategy** para manejo de afinidades
10. **Implementar patrón Observer** para comunicación entre capas
11. **Agregar validación** en setters de propiedades
12. **Consistencia** en nomenclatura español vs inglés

---

## 🛠️ Herramientas de Refactoring Recomendadas

- **Rider/Visual Studio:** Refactoring automático
- **SonarQube:** Análisis de calidad de código
- **ReSharper:** Sugerencias de mejoras
- **CodeMaid:** Limpieza automática de código

---

*Total de violaciones identificadas: 47*  
*Tiempo estimado de corrección: 8-12 horas*
