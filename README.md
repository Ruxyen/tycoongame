# 🎮 Cría un Bicho Roto

> Tycoon de colección de monstruos para Roblox con sistema de fusión y mecánicas de progresión pasiva.

**Estado:** Pre-desarrollo · **Plataforma:** Roblox · **Stack:** Lua + Roblox Studio · **Target:** PC, Mobile, Tablet, Console

---

## 📑 Índice

1. [Visión del juego](#-visión-del-juego)
2. [Concepto y mecánicas core](#-concepto-y-mecánicas-core)
3. [Sistemas del juego](#-sistemas-del-juego)
4. [Balance económico](#-balance-económico)
5. [Estética y naming](#-estética-y-naming)
6. [Estructura técnica](#-estructura-técnica)
7. [Configuración de services nativos](#-configuración-de-services-nativos)
8. [Estructura interna detallada](#-estructura-interna-detallada-de-modelos-uis-y-mapa)
9. [Schema de datos](#-schema-de-datos)
10. [ModuleScripts de configuración](#-modulescripts-de-configuración-completos)
11. [Servicios del servidor](#-servicios-del-servidor)
12. [Controllers del cliente](#-controllers-del-cliente)
13. [Flujos end-to-end](#-flujos-end-to-end)
14. [Anti-cheat y validaciones](#-anti-cheat-y-validaciones)
15. [Assets necesarios](#-assets-necesarios)
16. [Roadmap de desarrollo](#-roadmap-de-desarrollo-4-semanas)
17. [Plan de monetización](#-plan-de-monetización)
18. [Marketing y lanzamiento](#-marketing-y-lanzamiento)
19. [KPIs y métricas](#-kpis-y-métricas)
20. [Post-launch y escalado](#-post-launch-y-escalado)
21. [Recursos y referencias](#-recursos-y-referencias)

---

## 🎯 Visión del juego

**Cría un Bicho Roto** es un simulator/tycoon donde los jugadores coleccionan monstruos absurdos abriendo huevos, los colocan en su plot personal para generar dinero pasivo, y los fusionan en versiones más raras y poderosas. Inspirado en el género brainrot/Steal a Brainrot pero con una mecánica de fusión que lo diferencia.

### Pilares de diseño

- **Loop core simple y satisfactorio** — comprar huevos → obtener monstruos → colocar → ganar dinero → reinvertir
- **Profundidad opcional** — el sistema de fusión añade decisiones para los jugadores que quieren optimizar
- **Progresión infinita** — rebirths permanentes mantienen el juego vivo a largo plazo
- **Viral hooks** — anuncios globales de drops míticos crean FOMO y engagement social
- **F2P-friendly, monetización justa** — el juego es completamente jugable sin pagar

### Audiencia objetivo

- **Primaria:** Niños/teens 8-16 años, fans de simulators (Pet Sim, Brainrot games)
- **Secundaria:** Casual players adultos que disfrutan idle/tycoon games
- **Idioma:** Español/Inglés (priorizar Español para Latam y España)

### Diferenciadores clave

1. Sistema de fusión de 4 niveles (Normal → Dorado → Arcoíris → Void)
2. Naming humorístico memético alineado con la audiencia brainrot
3. Estética nocturna con neones en lugar del clásico "plástico colorido"
4. Plot personal con altar de fusión visible — cosmético y funcional
5. Anuncios globales generosos desde Mythic, no solo Divine

---

## 🎲 Concepto y mecánicas core

### Loops de tiempo anidados

El juego está diseñado con **múltiples loops temporales** que retienen al jugador en distintas escalas:

| Escala | Acción | Para qué |
|---|---|---|
| 30 segundos | Comprar → Abrir → Colocar | Dopamina constante |
| 5 minutos | Subir de tier de huevo | Progresión visible |
| 30 minutos | Conseguir raro / hacer fusión | Sesión productiva |
| Diario | Login + offline gen + dailies | Volver cada día |
| Semanal | 1-2 rebirths + evento | Progresión medio plazo |
| Mensual | Evento limitado nuevo | Retención a largo plazo |

### Loop core completo

```
       ┌─────────────────────────────────────┐
       │                                     │
       ▼                                     │
┌──────────────┐                             │
│   COMPRAR    │                             │
│    huevo     │                             │
└──────┬───────┘                             │
       ▼                                     │
┌──────────────┐                             │
│    ABRIR     │     ┌──────────────────┐   │
│    huevo     │────▶│     FUSIÓN       │   │
└──────┬───────┘     │ (3 misma rareza) │   │
       ▼             └────────┬─────────┘   │
┌──────────────┐              │             │
│   OBTENER    │◀─────────────┘             │
│   monstruo   │                            │
└──────┬───────┘                            │
       ▼                                    │
┌──────────────┐                            │
│   COLOCAR    │                            │
│   en slot    │                            │
└──────┬───────┘                            │
       ▼                                    │
┌──────────────┐                            │
│   GENERAR    │                            │
│    pasivo    │                            │
└──────┬───────┘                            │
       ▼                                    │
┌──────────────┐                            │
│  RECOLECTAR  │                            │
│    hucha     │────────────────────────────┘
└──────┬───────┘     REINVERTIR
       ▼
┌──────────────┐
│   REBIRTH    │ (cada $10M+ acumulado)
│  bonus perm. │
└──────────────┘
```

### Decisiones constantes del jugador

El juego presenta estas decisiones que crean profundidad sin agobiar:

**A nivel huevo:**
- ¿Subo de tier aunque tarde más? ¿Sigo farmeando el actual?
- ¿1 huevo Premium o 10 Avanzados?
- ¿Espero a tener boost de suerte antes de abrir?

**A nivel inventario:**
- ¿Vendo este Common o lo guardo para fundir?
- ¿Hago Dorado de este monstruo o sigo intentando subir de rareza?

**A nivel plot:**
- ¿Slot extra o multiplicador?
- ¿Capacidad de hucha o velocidad de generación?

**A nivel rebirth:**
- ¿Hago rebirth ya o espero al próximo tier?
- ¿Colecciono Voids antes de rebirth o reseteo y aprovecho bonus?

---

## 🎮 Sistemas del juego

### 1. Sistema de huevos (Gacha)

#### Mecánica básica

Los jugadores se acercan a una **máquina de huevos** en el Hub, interactúan con ella (ProximityPrompt o ClickDetector), y al confirmar la compra:

1. Se valida que tengan suficiente cash
2. Se descuenta el cash en el servidor
3. Se ejecuta un weighted random sobre la tabla de drops
4. Se aplican modificadores de luck (upgrades + gamepass + boost activo)
5. Se selecciona un monstruo aleatorio dentro de la rareza obtenida
6. Se reproduce la animación de apertura
7. El monstruo se añade al inventario
8. Si la rareza es Mythic+, se anuncia globalmente

#### Tipos de huevos (catálogo completo)

| Huevo | Precio | Moneda | Common | Rare | Epic | Legendary | Mythic | Secret | Divine |
|---|---|---|---|---|---|---|---|---|---|
| Básico | $50 | Cash | 70% | 25% | 5% | – | – | – | – |
| Avanzado | $500 | Cash | 30% | 50% | 18% | 2% | – | – | – |
| Premium | $5K | Cash | – | 30% | 50% | 18% | 2% | – | – |
| Cósmico | $50K | Cash | – | – | 40% | 45% | 14% | 1% | – |
| Divino | $500K | Cash | – | – | – | 50% | 40% | 9% | 1% |
| VIP | 199 R$ | Robux | – | – | 30% | 50% | 19% | 1% | – |

#### Modificadores de luck

```
luckBonus = upgradeLuck + gamepassLuck + activeBoostLuck
finalChance = baseChance × (1 + luckBonus)
```

El `luckBonus` se aplica solo a Epic+ (no a Common/Rare). Esto mantiene el balance.

#### Animación de apertura por rareza

| Rareza | Duración | Efectos |
|---|---|---|
| Common | 1s | Tembleque suave, partículas grises, pop |
| Rare | 1.5s | Brillo verde, partículas verdes |
| Epic | 2s | Cámara zoom, slow-mo, brillo azul intenso |
| Legendary | 3s | Cámara cinematográfica, beam vertical, screen shake suave |
| Mythic | 4s | Pantalla oscurece, beam permanente, anuncio global, fanfarria |
| Secret | 5s | Brillo arcoíris, beam multicolor, anuncio especial |
| Divine | 6s | Mega brillo dorado, anuncio a todo el server, música pausa |

### 2. Sistema de plot personal

#### Asignación dinámica del plot

```
1. Player entra al juego
2. PlotService busca plot vacío en Workspace/Plots/
3. Si no hay → clona ServerStorage/PlotTemplate a posición libre
4. Asigna OwnerId = player.UserId en Configuration
5. Renombra a Plot_<UserId>
6. Spawnea al jugador en SpawnPad
7. Carga datos del jugador (DataService)
8. Reconstruye estado visual del plot:
   - Coloca monstruos guardados en sus slots
   - Aplica skin del plot
   - Activa cosmetics y decoraciones
   - Muestra cash actual en hucha
```

#### Componentes del plot (cada uno detallado en sección 8)

- Suelo y paredes (estructura, intercambiable con skins)
- 4-50 slots para monstruos
- Hucha central con BillboardGui
- Altar de fusión
- 5 botones de upgrade
- Altar de Rebirth
- Kiosko de Daily Reward
- Cartel con nombre del propietario
- SpawnPad

#### Liberación del plot

```
1. Player sale del juego
2. DataService guarda todos los datos
3. PlotService elimina el plot del workspace
4. Slot disponible para otro jugador
```

Los monstruos siguen "generando" matemáticamente mientras el jugador está offline (con cap configurable).

### 3. Generación pasiva

#### Cálculo de generación

Cada N segundos (default 3s), `EconomyService` ejecuta:

```lua
for each plot in active_plots:
    for each placed_monster in plot.placedMonsters:
        baseGen = MonsterData[monster.id].baseGen
        variantMult = RarityConfig.Variants[monster.variant].multiplier
        upgradeMult = 1 + (plot.upgrades.global_multiplier × 0.10)
        rebirthMult = RebirthData.GetTotalMultiplier(plot.rebirth)
        gamepassMult = HasVIP(plot.owner) ? 2 : 1
        boostMult = GetActiveCashBoost(plot.owner)
        
        generation = baseGen × variantMult × upgradeMult × 
                     rebirthMult × gamepassMult × boostMult
        plot.vaultBalance += generation
    
    if plot.vaultBalance > vaultCap:
        plot.vaultBalance = vaultCap
```

#### Tabla de generación con variantes

| Rareza | Normal | Dorado (×2) | Arcoíris (×5) | Void (×15) |
|---|---|---|---|---|
| Common | $1 / 3s | $2 | $5 | $15 |
| Rare | $5 / 3s | $10 | $25 | $75 |
| Epic | $25 / 3s | $50 | $125 | $375 |
| Legendary | $150 / 3s | $300 | $750 | $2.250 |
| Mythic | $1.000 / 3s | $2.000 | $5.000 | $15.000 |
| Secret | $10.000 / 3s | $20.000 | $50.000 | $150.000 |
| Divine | $100.000 / 3s | $200.000 | $500.000 | $1.500.000 |

#### Texto flotante "+$X"

BillboardGui temporal sobre cada monstruo cuando genera:

- Solo visible para el dueño
- Se acumula si genera muy rápido
- Color del texto según rareza
- Animación TweenService duración ~1.5s

#### Generación offline

Cuando el jugador vuelve después de estar offline:

```
1. offlineSeconds = os.time() - data.stats.lastJoinDate
2. cap = HasOfflineGamepass ? 86400 : 7200 (24h vs 2h)
3. effectiveSeconds = min(offlineSeconds, cap)
4. offlineGen = sum(monster.gen × effectiveSeconds / 3)
5. vaultBalance += offlineGen (capped)
6. UI: "¡Bienvenido! Has ganado $XYZ mientras estabas offline"
```

### 4. Sistema de fusión (HOOK ÚNICO)

#### Mecánica del altar

- 3 slots de input visibles
- Player interactúa → abre UI de fusión
- Mete monstruos del inventario en los slots
- Confirma → server valida + ejecuta

Validaciones del server:
1. ¿Pertenecen al jugador?
2. ¿Coinciden con alguna receta válida?
3. ¿Tiene el cash necesario para el coste?
4. Si todo OK → ejecuta fusión, devuelve resultado

#### Tipos de fusión

| Fusión | Input | Output | Coste |
|---|---|---|---|
| **Subir tier** | 3× misma rareza (cualquier monstruo) | 1× random rareza superior | Gratis |
| **Forjar Dorado** | 2× mismo monstruo (Normal) | Dorado del mismo (×2 gen) | $10K |
| **Forjar Arcoíris** | 2× mismo monstruo (Dorado) | Arcoíris (×5 gen) | $250K |
| **Forjar Void** | 2× mismo monstruo (Arcoíris) | Void (×15 gen) | $5M |

#### Animación de fusión

```
1. Monstruos se materializan sobre el altar
2. Empiezan a girar lentamente
3. Aceleración de rotación
4. Aparecen rayos conectándolos
5. Explosión de luz blanca (1s)
6. Aparece el monstruo nuevo con color de variante
7. Sonido "chime" épico
8. UI: "¡FUSIÓN EXITOSA!"
```

#### Sistema de variantes

| Variante | Apariencia | Multiplicador | Efecto extra |
|---|---|---|---|
| Normal | Color base | ×1 | – |
| Dorado | Material gold | ×2 | Sparkles dorados |
| Arcoíris | ColorSequence animado | ×5 | Trail multicolor |
| Void | Material negro | ×15 | Aura void permanente |

### 5. Sistema de Rebirth

#### Mecánica del soft reset

**Pierdes:**
- Cash actual
- Monstruos colocados (vuelven al inventario)
- Upgrades del plot
- Slots desbloqueados (vuelven a 4)

**Mantienes:**
- Monstruos del inventario
- Gamepasses y compras Robux
- Stats acumulados
- Pet Index
- Variantes (Doradas, Arcoíris, Void)

**Ganas:**
- +1 al rebirth count
- Bonus permanente acumulativo
- Acceso a contenido exclusivo de rebirth alto

#### Curva de Rebirth

| Rebirth | Coste (cash total acumulado) | Bonus permanente | Recompensa especial |
|---|---|---|---|
| 1 | $10M | +25% gen global | – |
| 2 | $50M | +50% gen global | – |
| 3 | $250M | +100% gen global | Huevo exclusivo R3 |
| 5 | $5B | +250% gen global | Monstruos exclusivos R5 |
| 10 | $1T | +1000% gen global | Título dorado en chat |
| 25 | $100T | Plot exclusivo + skin | – |
| 50 | $10Q | Mítico Eterno garantizado | – |

Fórmula intermedios: `cost = 10M × 5^(rebirth-1)` y `bonus = rebirth × 25%`.

#### Animación de Rebirth

```
1. Pantalla se oscurece
2. Partículas doradas alrededor del jugador
3. Animación de ascensión (levitación)
4. Explosión de luz blanca (3s)
5. Vuelve a aparecer en plot reseteado
6. "¡Rebirth #N completado! +X% generación permanente"
7. Fanfarria épica
8. Si rebirth con recompensa especial, popup adicional
```

### 6. Sistema de upgrades

#### Tabla de upgrades

| Upgrade ID | Nombre | Descripción | Niveles | Coste base | Scaling |
|---|---|---|---|---|---|
| `global_multiplier` | Multiplicador Global | +10% gen global | 10 | $10K | ×1.5 |
| `luck` | Suerte de Huevos | +5% chance rarezas altas | 5 | $25K | ×2.0 |
| `vault_capacity` | Capacidad de Hucha | +50% capacidad máxima | 8 | $5K | ×1.4 |
| `collect_speed` | Velocidad Recolección | +20% velocidad collect | 5 | $15K | ×1.6 |
| `offline_time` | Tiempo Offline | +30 min generación offline | 5 | $50K | ×2.0 |

#### Slots de monstruos

Costes fijos, no fórmula:

| Slot # | Coste | Slot # | Coste |
|---|---|---|---|
| 5 | $1K | 15 | $2M |
| 6 | $5K | 20 | $5M |
| 7 | $15K | 25 | $15M |
| 8 | $50K | 30 | $50M |
| 9 | $150K | 40 | $150M |
| 10 | $300K | 50 | $500M |
| 12 | $500K | | |

### 7. Sistemas de retención

#### Daily login bonus

Ciclo de 7 días, reset si saltas:

| Día | Recompensa |
|---|---|
| 1 | $100 |
| 2 | $500 |
| 3 | Huevo Básico gratis |
| 4 | $5K |
| 5 | Boost Suerte 30 min |
| 6 | $25K |
| 7 | Huevo Premium gratis |

#### Eventos limitados

- Frecuencia: cada 3-4 semanas
- Duración: 7-14 días
- Currency exclusiva del evento
- Tienda exclusiva con monstruos imposibles después
- Pack Robux con Mythic garantizado

#### Anuncios globales

```
🔥 [PlayerName] ha encontrado un [MonsterName] [Rareza]!
```

Para Mythic+, banner para todos con sonido épico. Para Divine, también mensaje en chat global.

#### Pet Index

Pantalla de colección con:
- Silueta gris si no descubierto
- Imagen real si descubierto
- Stats del monstruo
- "Obtenido X veces" tracking
- Progreso global "X/Y descubiertos"

Crea **completionismo** — los jugadores quieren llenar el índice.

### 8. Sistema de robo (POST-LAUNCH v2)

⚠️ NO incluir en lanzamiento. Validar retención base primero.

**Opción A — Robo pasivo (RECOMENDADA inicial):**
Solo se roba cash de la hucha si no recoges. Otros entran a tu plot, click en hucha → roban % del balance.

**Opción B — Robo activo (alternativa viral):**
Robar monstruos colocados con animación, posibilidad de cazar al ladrón, defensas con torretas.

### 9. Sistema de trading (POST-LAUNCH v3)

Mes 3+. Intercambio entre jugadores con:
- UI de trading con propuesta + contraoferta
- Validación server bilateral
- Fee 5-10% en gems al completarse
- Trade history para evitar scams
- Lock de monstruos (no fundir/vender accidentalmente)

---

## 💰 Balance económico

### Curva de progresión F2P

| Hito | Tiempo aprox. |
|---|---|
| Llenar 8 slots iniciales | Hora 1 |
| Primer Épico | Hora 1-2 |
| Primer Legendario | Hora 5 |
| Primer Mítico | Hora 15 |
| Primer Rebirth | Hora 30 |
| Rebirth 5 | Hora 100 |
| Endgame (Voids, R25+) | Hora 500+ |

### Diseño de la curva en 3 fases

#### Fase 1: Onboarding (horas 0-3)
- Progresión MUY rápida
- Cash a chorros
- Nuevos huevos cada 15-20 min
- Slots extra alcanzables en minutos
- **Objetivo:** dopamina constante, enganche

#### Fase 2: Mid-game (horas 3-30)
- Progresión moderada
- Necesitas estrategia (qué fundir, qué vender)
- Primeros Legendaries son hitos
- **Objetivo:** profundidad, decisiones

#### Fase 3: End-game (horas 30+)
- Progresión lenta pero constante
- Cada Mítico es celebración
- Rebirths son metas a medio plazo
- Voids son trofeos
- **Objetivo:** retener whales y completionistas

### Inflación controlada

- **Generación:** crece linealmente con monstruos colocados
- **Costes:** crecen exponencialmente (×1.5 por nivel)
- **Rebirth:** reset hace que el techo siempre quede lejos
- **Variantes Void:** ×15 gen = nuevo techo cada vez

---

## 🎨 Estética y naming

### Concepto visual

Mundo flotante de plataformas en el cielo, cada plot es una isla privada flotando entre nubes oscuras. Hub central con estética de bazar/feria nocturna con neones brillantes y carteles luminosos.

Mezcla entre **Adopt Me** (cuteness), **Pet Simulator 99** (juice exagerado), y **Steal a Brainrot** (humor absurdo).

### Paleta de colores oficial

```
Fondo principal:    #1A0B2E  (morado profundo)
Background plots:   #0F0820  (más oscuro)
Acento neón rosa:   #FF2E88
Acento neón cian:   #00F0FF
Premium dorado:     #FFB800
Premium plata:      #C0C0C0
Texto principal:    #FFFFFF
Texto secundario:   #B8B8D1
Éxito:              #4CFF7C
Error:              #FF4C4C
Warning:            #FFD700
Arcoíris:           Animación dinámica
```

### Tipografía

- **Títulos UI:** Bebas Neue / Anton (display chunky condensado)
- **Body:** Inter / Poppins (legible)
- **Números:** Orbitron / JetBrains Mono (futurista)
- **Logo:** Fredoka One (redondeado, amigable)
- **Texto 3D in-world:** Bangers (visible desde lejos)

### Naming de monstruos por rareza

#### Common (60%) — "chusta endearing"
Goblin Roto, Sapo Triste, Pollo Glitch, Rata WiFi, Gusano Cansado, Hongo Pixel, Topo Dormido, Babosa Neón, Mosquito Lento, Caracol Roto, Polilla Confusa, Lagarto Pequeño

#### Rare (25%)
Goblin Dorado, Pingüino Hacker, Tortuga Turbo, Murciélago Emo, Zorro Glitch, Axolotl Punk, Rana Cyber, Gato Neón, Buho Estudiante, Mapache Bandido

#### Epic (10%)
Dragón Bebé, Robot Roto, Calamar Cósmico, Conejo Demonio, Gato Void, León Pixelado, Pulpo Ninja, Águila Glitch

#### Legendary (4%)
Skibidi Dragón, Hidra Glitch, Fénix Pixelado, Lobo Espacial, Yeti Neón, Quimera Memé

#### Mythic (0.9%) — anuncio global
Capybara Galáctico, Kraken de Bolsillo, Tigre Holográfico, Pulpo Reactor, Cerberus Pixelado

#### Secret (0.09%)
Cthulhu Confundido, El Bicho™, Void Worm, Glitch Beast

#### Divine (0.01%) — todo en mayúsculas
ÁNIMA, GLITCH PRIME, EL ELEGIDO, NULL

### Naming de huevos

- **Huevo Básico** — gris simple
- **Huevo Avanzado** — azul brillante
- **Huevo Premium** — morado con sparkles
- **Huevo Cósmico** — galáctico con estrellas
- **Huevo Divino** — dorado con aura
- **Huevo VIP** — negro con detalles dorados (Robux)

### Plot personalizable (cosméticos desbloqueables)

- **Default** — plataforma básica con neón rosa
- **Espacial** — fondo galáctico, suelo metálico
- **Lava** — fuego, suelo magma
- **Crystal** — suelo de cristal, partículas
- **Toxic** — verde tóxico, charcos
- **Royal** — dorado y morado, fuente
- **Halloween** (limitado) — calabazas, niebla
- **Christmas** (limitado) — nieve, luces
- **Cyberpunk** (rebirth 25 unlock) — neones, hologramas

### Animaciones clave (juice)

#### UI transitions
- Apertura menús: scale + fade-in (TweenService)
- Hover botones: bounce sutil
- Click botones: scale-down brief
- Notificaciones: slide-in lateral

#### Drops Epic+
- Particle emitter permanente sobre monstruo
- Glow ambient
- Color del particle según rareza

#### Generación de cash
- Texto "+$X" floating con bounce + fade
- Color según rareza del monstruo
- Si gen grande (1000+), texto más grande

#### Recolección
- Magnetic effect: monedas vuelan desde hucha al HUD
- Sonido "cha-ching"
- HUD del cash hace bounce al recibir

#### Anuncio global mítico
- Banner aparece desde arriba con elastic
- Permanece 5 segundos
- Sale por arriba con fade
- Sonido épico (diferente según rareza)

---

## 🏗 Estructura técnica

### Stack tecnológico

- **Lenguaje:** Lua (Luau específicamente)
- **Engine:** Roblox Studio
- **Versionado:** Git + Rojo (sync Studio ↔ filesystem)
- **Backend:** Roblox DataStore (persistencia) + MemoryStore (cache)
- **Analytics:** GameAnalytics + dashboard nativo Roblox
- **Communication:** RemoteEvents (async) + RemoteFunctions (sync)

### Configuración inicial obligatoria

#### Game Settings → Security
- ✅ Allow API Services
- ✅ Allow HTTP Requests
- ✅ Studio Access to API Services

#### Game Settings → World
- ✅ StreamingEnabled (CRÍTICO para mobile)
- StreamingTargetRadius: 1024
- StreamingMinRadius: 256

#### Game Settings → Basic Info
- ✅ Computadora
- ✅ Teléfono
- ✅ Tablet
- ✅ Consola
- ❌ VR

#### Team Create
- ✅ Activado (autosave + colaboración + versionado)

#### Intercambio de datos con IA Roblox
- ❌ Desactivado

### Decisiones arquitectónicas clave

#### 1. Service-oriented architecture
Cada sistema es un ModuleScript independiente con API pública. Handlers reciben Remotes y delegan al Service apropiado.

**Ventajas:** testeable aislado, cambios no rompen otros sistemas, escalable, onboarding fácil.

#### 2. Handlers separados de Services
```
Cliente (LocalScript) → RemoteEvent:FireServer(args)
                                    ↓
                              Handler recibe
                                    ↓
                          Validación básica
                                    ↓
                          Service:Method(player, args)
                                    ↓
                          Lógica + DataService
                                    ↓
                          DataUpdated:FireClient(player, newData)
```

#### 3. PlayerState centralizado en cliente
Único ModuleScript que cachea TODOS los datos del player. Cuando server envía `DataUpdated`, este módulo se actualiza y todos los Controllers leen de aquí.

#### 4. DataStore best practices
- Pcall siempre
- UpdateAsync sobre SetAsync
- Cache en memoria + save cada 60s
- BindToClose
- DataStore versionado (`PlayerData_v1`)
- Migrations integradas

#### 5. UUIDs para monstruos
Cada instancia tiene `uuid` único. Permite variantes independientes, identificación exacta para fusión, lock individual.

### Estructura de carpetas en Studio

```
game/
│
├── Workspace/                          # 🌍 Mundo físico/visible
│   ├── Hub/                            # Zona común con todos los jugadores
│   │   ├── BaseStructure
│   │   ├── EggMachines/
│   │   ├── ShopNPC
│   │   ├── DailyRewardKiosk
│   │   ├── LeaderboardDisplay
│   │   ├── Decorations/
│   │   ├── Lighting/
│   │   └── Teleporters/
│   ├── Plots/                          # Plots dinámicos por jugador
│   ├── EventArea/
│   ├── SpawnLocation
│   ├── Baseplate
│   ├── Terrain
│   └── Camera
│
├── Lighting/                           # 💡 Iluminación global
│   ├── Atmosphere
│   ├── Sky
│   ├── Bloom
│   ├── ColorCorrection
│   ├── DepthOfField
│   ├── SunRays
│   └── BlurEffect
│
├── ReplicatedFirst/                    # ⚡ Carga ANTES que el resto
│   ├── LoadingScreen
│   └── EssentialAssets/
│
├── ReplicatedStorage/                  # 📦 Compartido cliente↔servidor
│   ├── Modules/
│   │   ├── MonsterData
│   │   ├── EggData
│   │   ├── RarityConfig
│   │   ├── FusionRecipes
│   │   ├── UpgradeData
│   │   ├── RebirthData
│   │   ├── GamepassData
│   │   ├── ProductData
│   │   ├── DailyRewards
│   │   ├── BoostData
│   │   ├── EventData
│   │   ├── PlotSkins
│   │   └── Utils/
│   │       ├── Format
│   │       ├── Random
│   │       ├── Signal
│   │       ├── UUID
│   │       ├── Validate
│   │       └── TableUtils
│   ├── RemoteEvents/
│   │   ├── BuyEgg
│   │   ├── PlaceMonster
│   │   ├── RemoveMonster
│   │   ├── CollectCash
│   │   ├── FuseMonsters
│   │   ├── BuyUpgrade
│   │   ├── BuySlot
│   │   ├── DoRebirth
│   │   ├── SellMonster
│   │   ├── SellMonsters (bulk)
│   │   ├── LockMonster
│   │   ├── ClaimDailyReward
│   │   ├── SetPlotSkin
│   │   ├── DataUpdated
│   │   ├── MonsterPlaced
│   │   ├── MonsterRemoved
│   │   ├── EggOpened
│   │   ├── GlobalAnnouncement
│   │   ├── BoostActivated
│   │   └── NotificationSent
│   ├── RemoteFunctions/
│   │   ├── GetPlayerData
│   │   ├── GetFusionPreview
│   │   ├── GetServerStats
│   │   ├── GetLeaderboard
│   │   └── GetEventStatus
│   └── Assets/
│       ├── Monsters/
│       ├── Eggs/
│       ├── VFX/
│       ├── Animations/
│       └── UI/
│
├── ServerStorage/                      # 🔒 Privado del servidor
│   ├── PlotTemplate
│   ├── HubModels/
│   ├── EventTemplates/
│   │   ├── HalloweenEvent
│   │   ├── ChristmasEvent
│   │   └── ValentineEvent
│   └── AdminTools/
│
├── ServerScriptService/                # ⚙️ Lógica del servidor
│   ├── Main
│   ├── Services/
│   │   ├── DataService
│   │   ├── PlotService
│   │   ├── MonsterService
│   │   ├── EggService
│   │   ├── EconomyService
│   │   ├── FusionService
│   │   ├── UpgradeService
│   │   ├── RebirthService
│   │   ├── GamepassService
│   │   ├── ProductService
│   │   ├── DailyRewardService
│   │   ├── NotificationService
│   │   ├── BoostService
│   │   ├── EventService
│   │   ├── LeaderboardService
│   │   ├── AnalyticsService
│   │   └── AntiExploitService
│   └── Handlers/
│       ├── BuyEggHandler
│       ├── PlaceMonsterHandler
│       ├── RemoveMonsterHandler
│       ├── CollectCashHandler
│       ├── FuseMonstersHandler
│       ├── BuyUpgradeHandler
│       ├── BuySlotHandler
│       ├── DoRebirthHandler
│       ├── SellMonsterHandler
│       ├── ClaimDailyRewardHandler
│       └── SetPlotSkinHandler
│
├── StarterPlayer/
│   ├── StarterPlayerScripts/
│   │   ├── Main
│   │   ├── Controllers/
│   │   │   ├── UIController
│   │   │   ├── PlotController
│   │   │   ├── EggController
│   │   │   ├── InventoryController
│   │   │   ├── FusionController
│   │   │   ├── ShopController
│   │   │   ├── NotificationController
│   │   │   ├── TutorialController
│   │   │   ├── CameraController
│   │   │   ├── SoundController
│   │   │   └── InputController
│   │   └── State/
│   │       └── PlayerState
│   ├── StarterCharacterScripts/
│   └── StarterCharacter (opcional)
│
├── StarterPack/                        # vacío
│
├── StarterGui/
│   ├── HUD
│   ├── InventoryGui
│   ├── ShopGui
│   ├── FusionGui
│   ├── UpgradeGui
│   ├── RebirthGui
│   ├── DailyRewardGui
│   ├── NotificationGui
│   ├── TutorialGui
│   ├── PetIndexGui
│   ├── SettingsGui
│   └── EventGui
│
├── SoundService/
│   ├── Music/
│   │   ├── HubMusic
│   │   ├── PlotMusic
│   │   └── EventMusic
│   └── SFX/
│       ├── EggCrack, EggHatch
│       ├── DropCommon, DropRare, DropEpic, DropLegendary
│       ├── DropMythic, DropSecret, DropDivine
│       ├── CashCollect, CashEarn
│       ├── Fusion, Rebirth
│       ├── ButtonClick, ButtonHover
│       ├── PurchaseSuccess, ErrorSound
│       └── NotificationDing
│
├── MaterialService/                    # opcional
│
├── TextChatService/                    # chat moderno
│
└── Teams/                              # vacío
```

---

## ⚙️ Configuración de services nativos

Los services siguientes vienen por defecto en cada experiencia. No se crean — se configuran desde el panel Properties cuando los seleccionas en el Explorer.

### 🌍 Workspace

Configuración recomendada en Properties:

| Propiedad | Valor | Por qué |
|---|---|---|
| Gravity | 196.2 | Default — no cambiar |
| FilteringEnabled | true | Siempre, protege de exploits |
| StreamingEnabled | **true** | CRÍTICO en simulators con muchos plots |
| StreamingTargetRadius | 1024 | Distancia máxima cargada |
| StreamingMinRadius | 256 | Distancia mínima cargada |

Estructura interna que tú creas dentro de Workspace:

- `Hub/` → la zona común
- `Plots/` → se generan dinámicamente al entrar jugadores
- `EventArea/` → vacío hasta que haya evento activo
- `SpawnLocation` → punto de aparición inicial

### 💡 Lighting

La estética visual del juego sale 50% de aquí.

| Propiedad | Valor recomendado | Por qué |
|---|---|---|
| Brightness | 1.5-2 | Iluminación general |
| ClockTime | 19 (atardecer) o 0 (noche) | Para tema nocturno |
| GeographicLatitude | 41.5 | Posición del sol |
| GlobalShadows | true | Sombras realistas |
| Technology | Future | Mejor calidad |
| Ambient | (40, 40, 60) | Tinte morado en sombras |
| OutdoorAmbient | (50, 50, 70) | Tinte cielo nocturno |
| EnvironmentDiffuseScale | 0.5 | Reflejos del entorno |
| EnvironmentSpecularScale | 0.5 | Brillos del entorno |

Hijos a añadir dentro de Lighting:

#### Atmosphere
- Density: 0.3
- Color: morado (#1A0B2E)
- Decay: morado claro
- Glare: 0
- Haze: 1

#### Sky
Skybox personalizado (busca "night sky" o "stars" en Toolbox)

#### Bloom
- Intensity: 0.4
- Size: 24
- Threshold: 1.5

#### ColorCorrection
- Saturation: 0.2
- Contrast: 0.1
- TintColor: (255, 230, 255) — ligero tinte rosa

#### DepthOfField (opcional)
Desenfoque cinematográfico

#### SunRays
Solo si quieres sol visible

#### BlurEffect
Enabled: false por defecto, se activa cuando se abre menú

### 🔊 SoundService

| Propiedad | Valor |
|---|---|
| AmbientReverb | Hangar |
| DistanceFactor | 3.33 |
| DopplerScale | 1 |
| RolloffScale | 1 |
| RespectFilteringEnabled | true |

Estructura de hijos:

```
SoundService/
├── Music/ (Folder)
│   ├── HubMusic (Sound, Looped: true, Volume: 0.3)
│   ├── PlotMusic (Sound, Looped: true, Volume: 0.25)
│   └── EventMusic (Sound, Looped: true, Volume: 0.3)
└── SFX/ (Folder)
    └── ... (objetos Sound individuales)
```

Reproducir desde cliente:
```lua
local SoundService = game:GetService("SoundService")
SoundService.SFX.EggHatch:Play()
```

### ⚡ ReplicatedFirst

Lo que metas aquí se descarga ANTES que el resto.

```lua
-- ReplicatedFirst/LoadingScript (LocalScript)
local ReplicatedFirst = game:GetService("ReplicatedFirst")
ReplicatedFirst:RemoveDefaultLoadingScreen()

local loadingScreen = script.Parent.LoadingScreen
loadingScreen.Parent = game.Players.LocalPlayer.PlayerGui

if not game:IsLoaded() then 
    game.Loaded:Wait() 
end

loadingScreen:Destroy()
```

### 💬 TextChatService

| Propiedad | Valor |
|---|---|
| CreateDefaultCommands | true |
| CreateDefaultTextChannels | true |
| ChatVersion | TextChatService |

Personalizaciones útiles:
- Tags por rebirth tier ([R5], [R10], etc.)
- Color de nombre VIP (dorado)
- Anuncios globales de drops míticos
- Comandos custom: /trade, /inv, /stats

### 🎨 MaterialService

Solo si vas a usar materiales personalizados. Para empezar usa los defaults de Roblox (Plastic, Neon, Metal).

### Otros services

- **Teams**: vacío. Los simulators no usan equipos.
- **StarterPack**: vacío. No damos tools al jugador.

### Orden de creación recomendado en Studio

1. **Lighting** → configurar para que ambiente esté antes de modelar
2. **Workspace/Hub** → construir el Hub básico
3. **ServerStorage/PlotTemplate** → diseñar el plot vacío
4. **ReplicatedStorage/Modules** → todos los ModuleScripts de configuración
5. **ReplicatedStorage/Assets** → importar y organizar modelos
6. **SoundService** → meter música y SFX
7. **ServerScriptService/Services** → empezar con DataService, luego PlotService
8. **StarterGui** → las UIs después de que la lógica funcione
9. **StarterPlayerScripts/Controllers** → conectan UIs con lógica
10. **ReplicatedFirst** → loading screen al final


---

## 📐 Estructura interna detallada de modelos, UIs y mapa

Esta sección documenta la **jerarquía interna** de cada elemento que tiene subestructura compleja. Los ModuleScripts, Sounds, Animations, etc. son archivos únicos sin estructura interna — no se incluyen aquí.

### 1. Modelos de monstruos

Cada monstruo en `ReplicatedStorage/Assets/Monsters/` es un Model con:

```
goblin_roto (Model)
├── PrimaryPart                         # Marca cuál es la parte principal
├── HumanoidRootPart                    # Para animaciones (si usa Humanoid)
├── Humanoid                            # Si tiene animaciones tipo personaje
│   ├── Animator
│   └── HumanoidDescription (opcional)
├── Body/                               # MeshParts del cuerpo
│   ├── Head
│   ├── Torso
│   ├── LeftArm, RightArm
│   └── LeftLeg, RightLeg
├── Animations/                         # Folder con AnimationIds
│   ├── Idle (Animation)
│   ├── Eat (Animation)                 # Cuando "genera dinero"
│   └── Special (Animation)             # Para variantes Void
├── Effects/                            # Particle emitters según rareza
│   ├── RarityGlow (PointLight)         # Color según rareza
│   ├── Sparkles (ParticleEmitter)      # Solo si rareza Epic+
│   └── VariantTrail (Trail)            # Solo Golden/Rainbow/Void
├── Sounds/                             # Sonidos del monstruo
│   ├── EatSound
│   └── HappySound
├── Attachments/                        # Para anclar VFX dinámicos
│   ├── HeadAttachment
│   ├── BackAttachment
│   └── FeetAttachment
└── Configuration                       # ⚠️ IMPORTANTE
    ├── MonsterId (StringValue)         # "goblin_roto"
    ├── BaseGen (NumberValue)           # 1
    ├── Rarity (StringValue)            # "Common"
    └── Variant (StringValue)           # "Normal" (cambia en runtime)
```

**Por qué Configuration:** es la forma "Roblox-nativa" de adjuntar metadata a un modelo. Cuando spawneas, lees su Configuration directamente sin cruzar referencias con MonsterData. Útil para debug visual.

### 2. Modelos de huevos

```
egg_basic (Model)
├── PrimaryPart
├── Shell                               # MeshPart con la cáscara
├── Glow (PointLight)                   # Brillo
├── Sparkles (ParticleEmitter)
├── Sounds/
│   ├── HoverSound                      # Cuando cursor pasa por encima
│   ├── PurchaseSound
│   └── HatchSound
├── Animations/
│   ├── Idle                            # Animación flotante
│   ├── Shake                           # Tiembla antes de eclosionar
│   └── Hatch                           # Apertura
└── Configuration
    ├── EggId (StringValue)             # "egg_basic"
    └── Price (NumberValue)             # 50
```

### 3. PlotTemplate (el más complejo)

`ServerStorage/PlotTemplate` define todo lo que hay en el plot de cada jugador:

```
PlotTemplate (Model)
├── PrimaryPart (BasePart del suelo central)
│
├── Base/                               # Estructura física
│   ├── Floor (Part o MeshPart)
│   ├── Walls/
│   │   ├── WallNorth, WallSouth
│   │   ├── WallEast, WallWest
│   │   └── (o terrain bounds invisible)
│   ├── Decorations/                    # Cosméticos (intercambiables)
│   │   ├── Lampposts/
│   │   ├── Fountain
│   │   └── Trees/
│   └── PlotBoundary (Part invisible)   # Detectar entradas/salidas
│
├── Slots/                              # Slots para colocar monstruos
│   ├── Slot_1 (Part)
│   │   ├── Indicator                   # Part transparente que brilla cuando vacío
│   │   ├── PlacementAttachment (Attachment)
│   │   ├── ClickDetector
│   │   └── Configuration
│   │       ├── SlotIndex (NumberValue) # 1
│   │       └── Unlocked (BoolValue)    # true
│   ├── Slot_2 ... Slot_50
│   └── (los slots > 4 empiezan locked)
│
├── Vault/                              # La hucha central
│   ├── ChestModel (Model)
│   │   ├── Body (MeshPart)
│   │   ├── Lid (MeshPart)
│   │   └── ClickDetector
│   ├── BillboardGui                    # Muestra cash acumulado
│   │   └── CashLabel (TextLabel)
│   ├── ParticleEmitter (cuando hay cash)
│   └── Sounds/
│       ├── CollectSound
│       └── FillSound
│
├── FusionAltar/                        # Estación de fusión
│   ├── AltarModel
│   ├── InputSlot_1, InputSlot_2, InputSlot_3 (Parts)
│   ├── ProximityPrompt
│   ├── ParticleEmitter (idle)
│   └── BillboardGui (instructions)
│
├── UpgradeButtons/                     # 5 botones de upgrade
│   ├── UpgradeButton_GlobalMultiplier
│   │   ├── ButtonPart                  # Part presionable
│   │   ├── BillboardGui
│   │   │   ├── NameLabel
│   │   │   ├── DescriptionLabel
│   │   │   ├── CostLabel
│   │   │   └── LevelLabel
│   │   ├── ProximityPrompt
│   │   └── Configuration
│   │       └── UpgradeId (StringValue)
│   ├── UpgradeButton_Luck
│   ├── UpgradeButton_VaultCapacity
│   ├── UpgradeButton_CollectSpeed
│   └── UpgradeButton_OfflineTime
│
├── RebirthAltar/                       # Para hacer rebirth
│   ├── AltarModel
│   ├── ProximityPrompt
│   ├── BillboardGui
│   │   ├── RequirementLabel
│   │   └── BonusLabel
│   └── Effects/
│       └── RebirthParticles (disabled)
│
├── DailyRewardKiosk/                   # Reclamar daily
│   ├── KioskModel
│   ├── ProximityPrompt
│   └── BillboardGui
│
├── PlotSign/                           # Cartel con nombre del jugador
│   ├── SignModel
│   └── BillboardGui
│       └── PlayerNameLabel
│
├── SpawnPad (Part)                     # Donde aparece el jugador
│
└── Configuration                       # ⚠️ Metadata del plot
    ├── PlotId (NumberValue)            # Asignado en runtime
    ├── OwnerId (NumberValue)           # UserId del jugador
    └── PlotSize (Vector3Value)
```

### 4. ScreenGuis (UIs)

#### HUD (siempre visible)

```
HUD (ScreenGui)
├── ResetOnSpawn = false                # ⚠️ Importante para que persista
├── IgnoreGuiInset = true               # Pixel-perfect
│
├── TopBar/                             # Barra superior
│   ├── CashFrame (Frame)
│   │   ├── Icon (ImageLabel)
│   │   ├── Amount (TextLabel)
│   │   └── BuyButton (TextButton "+")  # Abre tienda de cash
│   ├── GemsFrame (Frame)
│   │   ├── Icon, Amount, BuyButton
│   ├── RebirthFrame (Frame)
│   │   ├── Icon, Counter
│   └── BoostsFrame                     # Boosts activos con timer
│
├── SideButtons/                        # Botones laterales (móvil-friendly)
│   ├── InventoryButton
│   ├── ShopButton
│   ├── PetIndexButton
│   ├── SettingsButton
│   ├── DailyButton                     # Con badge si hay daily disponible
│   └── EventButton                     # Solo visible si hay evento
│
├── BottomBar/                          # Solo móvil (jump button)
│   └── JumpButton
│
└── NotificationArea/
    └── (vacío, se llena dinámicamente)
```

#### InventoryGui

```
InventoryGui (ScreenGui)
├── ResetOnSpawn = false
├── Enabled = false                     # Se activa al click
├── BackgroundDim (Frame)               # Oscurece detrás
│
├── MainFrame (Frame)
│   ├── Header/
│   │   ├── Title
│   │   ├── CloseButton
│   │   └── SearchBar
│   ├── Tabs/
│   │   ├── AllTab
│   │   ├── CommonTab
│   │   ├── RareTab
│   │   ├── EpicTab
│   │   └── ... (un tab por rareza)
│   ├── FilterPanel/
│   │   ├── SortByDropdown               # Rareza, gen, fecha
│   │   ├── ShowVariantsToggle
│   │   └── HideLockedToggle
│   ├── GridContainer (ScrollingFrame)
│   │   └── UIGridLayout
│   │       └── (items dinámicos)
│   │       └── ItemTemplate (Frame en ReplicatedStorage)
│   │           ├── MonsterIcon (ImageLabel)
│   │           ├── RarityBorder
│   │           ├── NameLabel
│   │           ├── GenLabel
│   │           ├── VariantBadge
│   │           ├── LockButton
│   │           └── ActionsMenu/
│   │               ├── PlaceButton
│   │               ├── SellButton
│   │               └── DetailsButton
│   └── Footer/
│       ├── CapacityLabel               # "150/250 (gamepass)"
│       └── BulkActionsButton
│
└── DetailsPopup (Frame, hidden default)
    ├── MonsterPreview (ViewportFrame)  # Modelo 3D del monstruo
    ├── StatsList
    ├── HistoryLabel
    └── ActionButtons
```

#### NotificationGui

```
NotificationGui (ScreenGui)
├── ResetOnSpawn = false
├── DisplayOrder = 999                  # Encima de todo
│
├── GlobalBannerTemplate (Frame, hidden)
│   ├── Background
│   ├── Icon
│   ├── PlayerName
│   ├── ActionText                      # "ha encontrado"
│   ├── ItemName                        # "Capybara Galáctico"
│   ├── RarityBadge
│   └── ParticleEmitter
│
├── ToastNotificationTemplate (Frame, hidden)
│   ├── Icon
│   ├── Message
│   └── CloseButton
│
└── ActiveNotifications (Frame)
```

### 5. Hub (mapa principal)

```
Hub (Model en Workspace)
├── Floor/
│   ├── MainPlatform (MeshPart o Parts)
│   ├── PathToShop
│   ├── PathToPlots
│   └── DecorativeFloors
│
├── Buildings/
│   ├── EggShop/
│   │   ├── Building
│   │   ├── Sign ("HUEVOS")
│   │   ├── Lights/
│   │   └── EggMachineSpawns/           # Donde se colocan máquinas
│   ├── FusionShop (futuro v2)
│   ├── TradePost (futuro v2)
│   └── EventArea
│
├── EggMachines/
│   ├── EggMachine_Basic (Model)
│   │   ├── Display                     # Pantalla con info del huevo
│   │   ├── EggPreview (Part rotando)
│   │   ├── ProximityPrompt
│   │   ├── BillboardGui
│   │   │   ├── EggNameLabel
│   │   │   ├── PriceLabel
│   │   │   └── DropChancesPanel        # Tabla de %
│   │   └── Configuration
│   │       └── EggId (StringValue)
│   └── ... (5 más)
│
├── NPCs/
│   ├── ShopNPC (Model con Humanoid)    # Para gamepasses
│   │   ├── NameTag (BillboardGui)
│   │   └── DialogScript
│   ├── DailyRewardNPC
│   ├── EventNPC (solo si evento)
│   └── TutorialNPC                     # Habla con jugadores nuevos
│
├── Lighting/                           # Iluminación local del Hub
│   ├── NeonSigns/                      # MeshParts con material Neon
│   ├── PointLights/
│   ├── SpotLights/
│   └── EmitterParticles/               # Polvo, motas flotando
│
├── Decorations/
│   ├── Statues/
│   ├── Banners/
│   ├── Fountains/
│   └── Plants/
│
├── Teleporters/
│   ├── ToVIPLounge (Part con TouchScript)
│   ├── ToEventArea
│   └── ToYourPlot                      # Atajo al plot del jugador
│
├── LeaderboardDisplay/
│   ├── BoardModel
│   └── SurfaceGui
│       ├── Title ("TOP CASH")
│       └── PlayerSlots (10 entries)
│
└── HiddenAreas/
    └── VIPLounge/                      # Solo accesible con gamepass VIP
```

### Resumen de qué tiene jerarquía interna

| Categoría | ¿Subestructura? |
|---|---|
| ModuleScripts (Modules/, Services/, Controllers/) | ❌ NO — un solo archivo .lua |
| RemoteEvents, RemoteFunctions | ❌ NO — objetos vacíos, solo nombre |
| Sounds individuales | ❌ NO — un Sound = un objeto |
| Animations | ❌ NO — una Animation = un objeto con AnimationId |
| Particle Emitters en VFX/ | ❌ NO — un objeto |
| Modelos de monstruos | ✅ SÍ — body/animations/effects/sounds/configuration |
| Modelos de huevos | ✅ SÍ — más simple pero con jerarquía |
| PlotTemplate | ✅✅ SÍ — el más complejo del juego |
| ScreenGuis | ✅✅ SÍ — frames anidados, templates |
| Hub | ✅ SÍ — buildings, NPCs, decorations, lighting local |


---

## 💾 Schema de datos

### Configuración de monstruo (MonsterData)

```lua
{
    id = "goblin_roto",        -- Inmutable, usado en DataStore
    name = "Goblin Roto",      -- Mutable, display name
    rarity = "Common",
    baseGen = 1,               -- Generación por 3s
    modelName = "GoblinRoto",  -- Referencia al modelo en Assets/Monsters
}
```

⚠️ Los IDs en snake_case sin acentos. Una vez publicado, NO se pueden cambiar sin migración.

### Schema completo de save por jugador (DataStore)

```lua
{
    version = 1,                    -- Para migrations futuras
    
    -- ============ CURRENCIES ============
    currencies = {
        cash = 0,
        gems = 0,
    },
    
    -- ============ INVENTARIO ============
    -- Array de instancias UNICAS con UUID
    inventory = {
        -- {
        --     uuid = "abc123-def456",
        --     monsterId = "goblin_roto",
        --     variant = "Normal",      -- "Normal" | "Golden" | "Rainbow" | "Void"
        --     obtainedAt = 1735000000, -- timestamp os.time()
        --     locked = false,          -- si está locked, no se puede vender/fundir
        -- }
    },
    
    -- ============ MONSTRUOS COLOCADOS ============
    -- Map slotIndex → uuid del monstruo (que también está en inventory)
    placedMonsters = {
        -- [1] = "abc123-def456",
        -- [2] = "ghi789-jkl012",
    },
    
    -- ============ ESTADO DEL PLOT ============
    plot = {
        slotsUnlocked = 4,
        vaultBalance = 0,
        plotSkin = "default",
    },
    
    -- ============ UPGRADES ============
    upgrades = {
        global_multiplier = 0,
        luck = 0,
        vault_capacity = 0,
        collect_speed = 0,
        offline_time = 0,
    },
    
    -- ============ REBIRTH ============
    rebirth = {
        count = 0,
        totalEarnings = 0,          -- total acumulado histórico
    },
    
    -- ============ GAMEPASSES (cache local) ============
    gamepasses = {
        -- ["auto_collect"] = true,
    },
    
    -- ============ DAILY LOGIN ============
    daily = {
        lastClaim = 0,
        currentStreak = 0,
    },
    
    -- ============ BOOSTS ACTIVOS ============
    activeBoosts = {
        -- { id = "cash_x2", expiresAt = 1735005000 },
    },
    
    -- ============ ESTADÍSTICAS ============
    stats = {
        eggsOpened = 0,
        monstersObtained = 0,
        fusionsCompleted = 0,
        cashEarned = 0,
        playtime = 0,
        firstJoinDate = 0,
        lastJoinDate = 0,
    },
    
    -- ============ INDICE DESCUBIERTOS ============
    -- Set de monsterIds que el jugador ha obtenido alguna vez
    discovered = {
        -- ["goblin_roto"] = true,
    },
    
    -- ============ FLAGS / TUTORIAL ============
    flags = {
        tutorialCompleted = false,
        firstEggOpened = false,
        firstMonsterPlaced = false,
        firstCollect = false,
        firstFusion = false,
        firstRebirth = false,
    },
    
    -- ============ EVENTOS ============
    events = {
        -- ["halloween_2025"] = {
        --     currency = 0,
        --     itemsClaimed = {},
        --     missionsCompleted = {},
        -- },
    },
    
    -- ============ SETTINGS ============
    settings = {
        musicVolume = 1.0,
        sfxVolume = 1.0,
        autoSell = {
            enabled = false,
            rarities = {},
        },
        language = "es",
    },
}
```

### Decisiones críticas del schema

#### 1. UUIDs por monstruo individual
Si tienes 5 "goblin_roto", son 5 entradas distintas. Permite variantes independientes y fusión limpia.

#### 2. placedMonsters separado de inventory
Los monstruos colocados también están en inventory (no se duplican datos), pero placedMonsters mapea qué slot tiene qué uuid.

#### 3. version desde día 1
Sistema de migrations preparado. Cuando añadas un campo nuevo:

```lua
local Migrations = {
    [1] = function(data) return data end,
    
    [2] = function(data)
        data.pets = {}              -- nuevo campo
        data.version = 2
        return data
    end,
    
    [3] = function(data)
        if data.upgrades.luck then
            data.upgrades.egg_luck = data.upgrades.luck
            data.upgrades.luck = nil
        end
        data.version = 3
        return data
    end,
}

function DataService:Migrate(data)
    while data.version < CURRENT_VERSION do
        local nextVersion = data.version + 1
        data = Migrations[nextVersion](data)
    end
    return data
end
```

#### 4. discovered separado del inventory
Pet Index muestra qué monstruos descubierto alguna vez, aunque ya no los tengas (por venta o fusión).

#### 5. flags para tutorial y achievements
Booleanos one-shot. Útil para mostrar tutorial primera vez, otorgar achievement, tracking de funnel.

#### 6. stats.firstJoinDate y lastJoinDate
Crítico para generación offline (calcular diferencia) y analytics de retención.

### Estructura de DataStores

```lua
local PlayerDataStore = DataStoreService:GetDataStore("PlayerData_v1")
local TempDataStore = DataStoreService:GetDataStore("TempData_v1")
local LeaderboardStore = DataStoreService:GetOrderedDataStore("Cash_Leaderboard_v1")
```

⚠️ Versionar el nombre es CRÍTICO. Si necesitas reset total, cambias el nombre (`_v2`) — los datos viejos siguen disponibles.

### Buenas prácticas obligatorias

```lua
-- SIEMPRE pcall en operaciones de DataStore
local success, result = pcall(function()
    return PlayerDataStore:GetAsync(userId)
end)

if not success then
    warn("Failed to load data: " .. tostring(result))
    -- Reintentar con backoff exponencial, o cargar defaults
end

-- SIEMPRE UpdateAsync, no SetAsync
PlayerDataStore:UpdateAsync(userId, function(oldData)
    if not oldData then return DefaultData() end
    -- Aplicar cambios sobre oldData
    return modifiedData
end)

-- NUNCA guardar en cada acción del jugador
-- Cachear en memoria, guardar cada 60s + BindToClose
```

---

## 📦 ModuleScripts de configuración completos

### RarityConfig

```lua
-- ReplicatedStorage/Modules/RarityConfig.lua
local RarityConfig = {}

RarityConfig.Order = { 
    "Common", "Rare", "Epic", "Legendary", 
    "Mythic", "Secret", "Divine" 
}

RarityConfig.Data = {
    Common = {
        displayName = "Común",
        color = Color3.fromRGB(180, 180, 180),
        glowColor = Color3.fromRGB(220, 220, 220),
        weight = 60.00,
        sellValue = 10,
        announceGlobal = false,
    },
    Rare = {
        displayName = "Raro",
        color = Color3.fromRGB(80, 220, 80),
        glowColor = Color3.fromRGB(120, 255, 120),
        weight = 25.00,
        sellValue = 100,
        announceGlobal = false,
    },
    Epic = {
        displayName = "Épico",
        color = Color3.fromRGB(80, 140, 255),
        glowColor = Color3.fromRGB(120, 180, 255),
        weight = 10.00,
        sellValue = 1000,
        announceGlobal = false,
    },
    Legendary = {
        displayName = "Legendario",
        color = Color3.fromRGB(180, 80, 255),
        glowColor = Color3.fromRGB(220, 120, 255),
        weight = 4.00,
        sellValue = 10000,
        announceGlobal = false,
    },
    Mythic = {
        displayName = "Mítico",
        color = Color3.fromRGB(255, 140, 40),
        glowColor = Color3.fromRGB(255, 180, 80),
        weight = 0.90,
        sellValue = 100000,
        announceGlobal = true,    -- 🔥 anuncio global
    },
    Secret = {
        displayName = "Secreto",
        color = Color3.fromRGB(255, 40, 200),
        glowColor = Color3.fromRGB(255, 100, 220),
        weight = 0.09,
        sellValue = 1000000,
        announceGlobal = true,
    },
    Divine = {
        displayName = "Divino",
        color = Color3.fromRGB(255, 215, 0),
        glowColor = Color3.fromRGB(255, 240, 100),
        weight = 0.01,
        sellValue = 10000000,
        announceGlobal = true,
    },
}

-- Variantes
RarityConfig.Variants = {
    Normal  = { multiplier = 1,  suffix = "" },
    Golden  = { multiplier = 2,  suffix = " ✨", color = Color3.fromRGB(255, 200, 0) },
    Rainbow = { multiplier = 5,  suffix = " 🌈" },
    Void    = { multiplier = 15, suffix = " ◾", color = Color3.fromRGB(20, 0, 30) },
}

function RarityConfig.GetNextRarity(rarity)
    for i, r in ipairs(RarityConfig.Order) do
        if r == rarity and i < #RarityConfig.Order then
            return RarityConfig.Order[i + 1]
        end
    end
    return nil
end

return RarityConfig
```

### MonsterData

```lua
-- ReplicatedStorage/Modules/MonsterData.lua
local MonsterData = {}

MonsterData.List = {
    -- ============== COMMON ==============
    { id = "goblin_roto",      name = "Goblin Roto",       rarity = "Common", baseGen = 1, modelName = "GoblinRoto" },
    { id = "sapo_triste",      name = "Sapo Triste",       rarity = "Common", baseGen = 1, modelName = "SapoTriste" },
    { id = "pollo_glitch",     name = "Pollo Glitch",      rarity = "Common", baseGen = 1, modelName = "PolloGlitch" },
    { id = "rata_wifi",        name = "Rata WiFi",         rarity = "Common", baseGen = 1, modelName = "RataWifi" },
    { id = "gusano_cansado",   name = "Gusano Cansado",    rarity = "Common", baseGen = 1, modelName = "GusanoCansado" },
    { id = "hongo_pixel",      name = "Hongo Pixel",       rarity = "Common", baseGen = 1, modelName = "HongoPixel" },
    { id = "topo_dormido",     name = "Topo Dormido",      rarity = "Common", baseGen = 1, modelName = "TopoDormido" },
    { id = "babosa_neon",      name = "Babosa Neón",       rarity = "Common", baseGen = 1, modelName = "BabosaNeon" },
    { id = "mosquito_lento",   name = "Mosquito Lento",    rarity = "Common", baseGen = 1, modelName = "MosquitoLento" },
    { id = "caracol_roto",     name = "Caracol Roto",      rarity = "Common", baseGen = 1, modelName = "CaracolRoto" },
    { id = "polilla_confusa",  name = "Polilla Confusa",   rarity = "Common", baseGen = 1, modelName = "PolillaConfusa" },
    { id = "lagarto_pequeno",  name = "Lagarto Pequeño",   rarity = "Common", baseGen = 1, modelName = "LagartoPequeno" },
    
    -- ============== RARE ==============
    { id = "goblin_dorado",    name = "Goblin Dorado",     rarity = "Rare", baseGen = 5, modelName = "GoblinDorado" },
    { id = "pinguino_hacker",  name = "Pingüino Hacker",   rarity = "Rare", baseGen = 5, modelName = "PinguinoHacker" },
    { id = "tortuga_turbo",    name = "Tortuga Turbo",     rarity = "Rare", baseGen = 5, modelName = "TortugaTurbo" },
    { id = "murcielago_emo",   name = "Murciélago Emo",    rarity = "Rare", baseGen = 5, modelName = "MurcielagoEmo" },
    { id = "zorro_glitch",     name = "Zorro Glitch",      rarity = "Rare", baseGen = 5, modelName = "ZorroGlitch" },
    { id = "axolotl_punk",     name = "Axolotl Punk",      rarity = "Rare", baseGen = 5, modelName = "AxolotlPunk" },
    { id = "rana_cyber",       name = "Rana Cyber",        rarity = "Rare", baseGen = 5, modelName = "RanaCyber" },
    { id = "gato_neon",        name = "Gato Neón",         rarity = "Rare", baseGen = 5, modelName = "GatoNeon" },
    { id = "buho_estudiante",  name = "Buho Estudiante",   rarity = "Rare", baseGen = 5, modelName = "BuhoEstudiante" },
    { id = "mapache_bandido",  name = "Mapache Bandido",   rarity = "Rare", baseGen = 5, modelName = "MapacheBandido" },
    
    -- ============== EPIC ==============
    { id = "dragon_bebe",      name = "Dragón Bebé",       rarity = "Epic", baseGen = 25, modelName = "DragonBebe" },
    { id = "robot_roto",       name = "Robot Roto",        rarity = "Epic", baseGen = 25, modelName = "RobotRoto" },
    { id = "calamar_cosmico",  name = "Calamar Cósmico",   rarity = "Epic", baseGen = 25, modelName = "CalamarCosmico" },
    { id = "conejo_demonio",   name = "Conejo Demonio",    rarity = "Epic", baseGen = 25, modelName = "ConejoDemonio" },
    { id = "gato_void",        name = "Gato Void",         rarity = "Epic", baseGen = 25, modelName = "GatoVoid" },
    { id = "leon_pixelado",    name = "León Pixelado",     rarity = "Epic", baseGen = 25, modelName = "LeonPixelado" },
    { id = "pulpo_ninja",      name = "Pulpo Ninja",       rarity = "Epic", baseGen = 25, modelName = "PulpoNinja" },
    { id = "aguila_glitch",    name = "Águila Glitch",     rarity = "Epic", baseGen = 25, modelName = "AguilaGlitch" },
    
    -- ============== LEGENDARY ==============
    { id = "skibidi_dragon",   name = "Skibidi Dragón",    rarity = "Legendary", baseGen = 150, modelName = "SkibidiDragon" },
    { id = "hidra_glitch",     name = "Hidra Glitch",      rarity = "Legendary", baseGen = 150, modelName = "HidraGlitch" },
    { id = "fenix_pixelado",   name = "Fénix Pixelado",    rarity = "Legendary", baseGen = 150, modelName = "FenixPixelado" },
    { id = "lobo_espacial",    name = "Lobo Espacial",     rarity = "Legendary", baseGen = 150, modelName = "LoboEspacial" },
    { id = "yeti_neon",        name = "Yeti Neón",         rarity = "Legendary", baseGen = 150, modelName = "YetiNeon" },
    { id = "quimera_meme",     name = "Quimera Memé",      rarity = "Legendary", baseGen = 150, modelName = "QuimeraMeme" },
    
    -- ============== MYTHIC ==============
    { id = "capy_galactico",   name = "Capybara Galáctico",rarity = "Mythic", baseGen = 1000, modelName = "CapyGalactico" },
    { id = "kraken_bolsillo",  name = "Kraken de Bolsillo",rarity = "Mythic", baseGen = 1000, modelName = "KrakenBolsillo" },
    { id = "tigre_holo",       name = "Tigre Holográfico", rarity = "Mythic", baseGen = 1000, modelName = "TigreHolo" },
    { id = "pulpo_reactor",    name = "Pulpo Reactor",     rarity = "Mythic", baseGen = 1000, modelName = "PulpoReactor" },
    { id = "cerberus_pixel",   name = "Cerberus Pixelado", rarity = "Mythic", baseGen = 1000, modelName = "CerberusPixel" },
    
    -- ============== SECRET ==============
    { id = "cthulhu_confuso",  name = "Cthulhu Confundido",rarity = "Secret", baseGen = 10000, modelName = "CthulhuConfuso" },
    { id = "el_bicho",         name = "El Bicho™",         rarity = "Secret", baseGen = 10000, modelName = "ElBicho" },
    { id = "void_worm",        name = "Void Worm",         rarity = "Secret", baseGen = 10000, modelName = "VoidWorm" },
    { id = "glitch_beast",     name = "Glitch Beast",      rarity = "Secret", baseGen = 10000, modelName = "GlitchBeast" },
    
    -- ============== DIVINE ==============
    { id = "anima",            name = "ÁNIMA",             rarity = "Divine", baseGen = 100000, modelName = "Anima" },
    { id = "glitch_prime",     name = "GLITCH PRIME",      rarity = "Divine", baseGen = 100000, modelName = "GlitchPrime" },
    { id = "el_elegido",       name = "EL ELEGIDO",        rarity = "Divine", baseGen = 100000, modelName = "ElElegido" },
    { id = "null",             name = "NULL",              rarity = "Divine", baseGen = 100000, modelName = "Null" },
}

-- Lookups O(1)
MonsterData.ById = {}
for _, monster in ipairs(MonsterData.List) do
    MonsterData.ById[monster.id] = monster
end

MonsterData.ByRarity = {}
for _, monster in ipairs(MonsterData.List) do
    if not MonsterData.ByRarity[monster.rarity] then
        MonsterData.ByRarity[monster.rarity] = {}
    end
    table.insert(MonsterData.ByRarity[monster.rarity], monster)
end

function MonsterData.Get(id)
    return MonsterData.ById[id]
end

function MonsterData.GetByRarity(rarity)
    return MonsterData.ByRarity[rarity] or {}
end

return MonsterData
```

### EggData

```lua
-- ReplicatedStorage/Modules/EggData.lua
local EggData = {}

EggData.List = {
    {
        id = "egg_basic",
        name = "Huevo Básico",
        price = 50,
        currency = "Cash",
        modelName = "EggBasic",
        rarityWeights = {
            Common = 70, Rare = 25, Epic = 5,
            Legendary = 0, Mythic = 0, Secret = 0, Divine = 0,
        },
    },
    {
        id = "egg_advanced",
        name = "Huevo Avanzado",
        price = 500,
        currency = "Cash",
        modelName = "EggAdvanced",
        rarityWeights = {
            Common = 30, Rare = 50, Epic = 18, Legendary = 2,
            Mythic = 0, Secret = 0, Divine = 0,
        },
    },
    {
        id = "egg_premium",
        name = "Huevo Premium",
        price = 5000,
        currency = "Cash",
        modelName = "EggPremium",
        rarityWeights = {
            Rare = 30, Epic = 50, Legendary = 18, Mythic = 2,
            Common = 0, Secret = 0, Divine = 0,
        },
    },
    {
        id = "egg_cosmic",
        name = "Huevo Cósmico",
        price = 50000,
        currency = "Cash",
        modelName = "EggCosmic",
        rarityWeights = {
            Epic = 40, Legendary = 45, Mythic = 14, Secret = 1,
        },
    },
    {
        id = "egg_divine",
        name = "Huevo Divino",
        price = 500000,
        currency = "Cash",
        modelName = "EggDivine",
        rarityWeights = {
            Legendary = 50, Mythic = 40, Secret = 9, Divine = 1,
        },
    },
    {
        id = "egg_robux_vip",
        name = "Huevo VIP",
        price = 199,
        currency = "Robux",
        modelName = "EggVIP",
        rarityWeights = {
            Epic = 30, Legendary = 50, Mythic = 19, Secret = 1,
        },
    },
}

EggData.ById = {}
for _, egg in ipairs(EggData.List) do
    EggData.ById[egg.id] = egg
end

function EggData.Get(id)
    return EggData.ById[id]
end

return EggData
```

### FusionRecipes

```lua
-- ReplicatedStorage/Modules/FusionRecipes.lua
local FusionRecipes = {}

FusionRecipes.Types = {
    TierUp = {
        id = "tier_up",
        name = "Subir de tier",
        description = "Combina 3 monstruos de la misma rareza para obtener uno superior",
        inputCount = 3,
        cost = 0,
        costCurrency = "Cash",
        validate = function(monsters)
            local r = monsters[1].rarity
            for i = 2, #monsters do
                if monsters[i].rarity ~= r then 
                    return false, "Las rarezas no coinciden" 
                end
            end
            return true
        end,
    },
    
    Golden = {
        id = "to_golden",
        name = "Forjar Dorado",
        description = "Combina 2 del mismo monstruo para obtener Dorado (×2 gen)",
        inputCount = 2,
        cost = 10000,
        costCurrency = "Cash",
        inputVariant = "Normal",
        outputVariant = "Golden",
        validate = function(monsters)
            if monsters[1].monsterId ~= monsters[2].monsterId then
                return false, "Deben ser el mismo monstruo"
            end
            if monsters[1].variant ~= "Normal" or monsters[2].variant ~= "Normal" then
                return false, "Deben ser ambos versión Normal"
            end
            return true
        end,
    },
    
    Rainbow = {
        id = "to_rainbow",
        name = "Forjar Arcoíris",
        description = "Combina 2 Dorados del mismo monstruo (×5 gen)",
        inputCount = 2,
        cost = 250000,
        costCurrency = "Cash",
        inputVariant = "Golden",
        outputVariant = "Rainbow",
        validate = function(monsters)
            if monsters[1].monsterId ~= monsters[2].monsterId then
                return false, "Deben ser el mismo monstruo"
            end
            if monsters[1].variant ~= "Golden" or monsters[2].variant ~= "Golden" then
                return false, "Deben ser ambos Dorados"
            end
            return true
        end,
    },
    
    Void = {
        id = "to_void",
        name = "Forjar Void",
        description = "Combina 2 Arcoíris del mismo monstruo (×15 gen)",
        inputCount = 2,
        cost = 5000000,
        costCurrency = "Cash",
        inputVariant = "Rainbow",
        outputVariant = "Void",
        validate = function(monsters)
            if monsters[1].monsterId ~= monsters[2].monsterId then
                return false, "Deben ser el mismo monstruo"
            end
            if monsters[1].variant ~= "Rainbow" or monsters[2].variant ~= "Rainbow" then
                return false, "Deben ser ambos Arcoíris"
            end
            return true
        end,
    },
}

return FusionRecipes
```

### UpgradeData

```lua
-- ReplicatedStorage/Modules/UpgradeData.lua
local UpgradeData = {}

UpgradeData.List = {
    {
        id = "global_multiplier",
        name = "Multiplicador Global",
        description = "+10% generación de TODOS los monstruos",
        maxLevel = 10,
        baseCost = 10000,
        costScaling = 1.5,
        effect = function(level)
            return { globalMultiplier = 1 + (level * 0.10) }
        end,
    },
    {
        id = "luck",
        name = "Suerte de Huevos",
        description = "+5% chance de rarezas altas en huevos",
        maxLevel = 5,
        baseCost = 25000,
        costScaling = 2.0,
        effect = function(level)
            return { luckBonus = level * 0.05 }
        end,
    },
    {
        id = "vault_capacity",
        name = "Capacidad de Hucha",
        description = "+50% capacidad máxima",
        maxLevel = 8,
        baseCost = 5000,
        costScaling = 1.4,
        effect = function(level)
            return { vaultMultiplier = 1 + (level * 0.5) }
        end,
    },
    {
        id = "collect_speed",
        name = "Velocidad de Recolección",
        description = "+20% velocidad de recolectar",
        maxLevel = 5,
        baseCost = 15000,
        costScaling = 1.6,
        effect = function(level)
            return { collectSpeed = 1 + (level * 0.20) }
        end,
    },
    {
        id = "offline_time",
        name = "Tiempo Offline",
        description = "+30 min de generación offline",
        maxLevel = 5,
        baseCost = 50000,
        costScaling = 2.0,
        effect = function(level)
            return { offlineMinutes = 120 + (level * 30) }
        end,
    },
}

UpgradeData.SlotCosts = {
    [5]  = 1000,
    [6]  = 5000,
    [7]  = 15000,
    [8]  = 50000,
    [9]  = 150000,
    [10] = 300000,
    [12] = 500000,
    [15] = 2000000,
    [20] = 5000000,
    [25] = 15000000,
    [30] = 50000000,
    [40] = 150000000,
    [50] = 500000000,
}

UpgradeData.ById = {}
for _, upgrade in ipairs(UpgradeData.List) do
    UpgradeData.ById[upgrade.id] = upgrade
end

function UpgradeData.GetCost(upgradeId, currentLevel)
    local upgrade = UpgradeData.ById[upgradeId]
    if not upgrade then return nil end
    if currentLevel >= upgrade.maxLevel then return nil end
    return math.floor(upgrade.baseCost * (upgrade.costScaling ^ currentLevel))
end

return UpgradeData
```

### RebirthData

```lua
-- ReplicatedStorage/Modules/RebirthData.lua
local RebirthData = {}

RebirthData.Tiers = {
    [1]  = { cost = 10000000,    bonus = 0.25, reward = nil },
    [2]  = { cost = 50000000,    bonus = 0.50, reward = nil },
    [3]  = { cost = 250000000,   bonus = 1.00, reward = "egg_r3_exclusive" },
    [5]  = { cost = 5000000000,  bonus = 2.50, reward = "monsters_r5_unlock" },
    [10] = { cost = 1e12,        bonus = 10.00, reward = "title_golden" },
    [25] = { cost = 1e14,        bonus = 50.00, reward = "plot_skin_premium" },
    [50] = { cost = 1e16,        bonus = 100.00, reward = "monster_eternal" },
}

function RebirthData.GetCost(rebirthNumber)
    if RebirthData.Tiers[rebirthNumber] then
        return RebirthData.Tiers[rebirthNumber].cost
    end
    return math.floor(10000000 * (5 ^ (rebirthNumber - 1)))
end

function RebirthData.GetBonus(rebirthNumber)
    if rebirthNumber == 0 then return 0 end
    if RebirthData.Tiers[rebirthNumber] then
        return RebirthData.Tiers[rebirthNumber].bonus
    end
    return rebirthNumber * 0.25
end

function RebirthData.GetTotalMultiplier(rebirthCount)
    local total = 1.0
    for i = 1, rebirthCount do
        total = total + RebirthData.GetBonus(i)
    end
    return total
end

return RebirthData
```

### GamepassData

```lua
-- ReplicatedStorage/Modules/GamepassData.lua
local GamepassData = {}

-- ⚠️ IDs reales se ponen después de crear los gamepasses en el dashboard
GamepassData.List = {
    {
        id = "auto_collect",
        name = "Auto Collect",
        passId = 0,                         -- TODO: poner ID real
        price = 99,
        description = "Recoge dinero automáticamente cada 5 segundos",
        effects = { autoCollect = true, autoCollectInterval = 5 },
    },
    {
        id = "vip",
        name = "VIP",
        passId = 0,
        price = 499,
        description = "×2 cash, slot extra, tag VIP en chat",
        effects = { cashMultiplier = 2, extraSlots = 1, chatTag = "VIP" },
    },
    {
        id = "luck_x2",
        name = "Suerte ×2",
        passId = 0,
        price = 399,
        description = "Duplica las chances de rarezas altas",
        effects = { luckMultiplier = 2 },
    },
    {
        id = "inventory_plus",
        name = "Inventario Expandido",
        passId = 0,
        price = 199,
        description = "+150 slots de inventario (100 → 250)",
        effects = { inventoryBonus = 150 },
    },
    {
        id = "offline_24h",
        name = "Offline 24h",
        passId = 0,
        price = 299,
        description = "Generación offline hasta 24 horas",
        effects = { offlineMaxMinutes = 1440 },
    },
    -- TIER 2 (semana 2-4 post-launch)
    {
        id = "triple_hatch",
        name = "Triple Hatch",
        passId = 0,
        price = 249,
        description = "Abre 3 huevos al precio de 1",
        effects = { hatchMultiplier = 3 },
    },
    {
        id = "auto_hatch",
        name = "Auto Hatch",
        passId = 0,
        price = 199,
        description = "Compra y abre huevos automáticamente",
        effects = { autoHatch = true },
    },
    {
        id = "speed_coil",
        name = "Speed Coil",
        passId = 0,
        price = 149,
        description = "+50% velocidad de movimiento",
        effects = { walkSpeedMultiplier = 1.5 },
    },
    {
        id = "fusion_discount",
        name = "Fusion Discount",
        passId = 0,
        price = 349,
        description = "-50% coste de fusiones",
        effects = { fusionDiscount = 0.5 },
    },
    {
        id = "lucky_pet",
        name = "Lucky Block Pet",
        passId = 0,
        price = 599,
        description = "Mascota cosmética + 5% luck permanente",
        effects = { hasLuckyPet = true, luckBonus = 0.05 },
    },
    -- TIER 3 (mes 2+)
    {
        id = "ultra_vip",
        name = "Ultra VIP",
        passId = 0,
        price = 1499,
        description = "×3 cash + 3 slots + auto-collect + triple hatch + tag",
        effects = { 
            cashMultiplier = 3, extraSlots = 3, 
            autoCollect = true, hatchMultiplier = 3,
            chatTag = "ULTRA"
        },
    },
}

GamepassData.ById = {}
GamepassData.ByPassId = {}
for _, pass in ipairs(GamepassData.List) do
    GamepassData.ById[pass.id] = pass
    GamepassData.ByPassId[pass.passId] = pass
end

return GamepassData
```

### ProductData (Developer Products)

```lua
-- ReplicatedStorage/Modules/ProductData.lua
local ProductData = {}

ProductData.List = {
    -- Currency Packs (Gems)
    { id = "gems_small",   productId = 0, price = 49,  type = "gems", amount = 100 },
    { id = "gems_medium",  productId = 0, price = 199, type = "gems", amount = 500, label = "POPULAR" },
    { id = "gems_large",   productId = 0, price = 499, type = "gems", amount = 1500, label = "MEJOR VALOR" },
    { id = "gems_mega",    productId = 0, price = 999, type = "gems", amount = 3500 },
    
    -- Boosts temporales
    { id = "boost_cash_x2_30m", productId = 0, price = 49, type = "boost", boost = "cash_x2", duration = 1800 },
    { id = "boost_cash_x3_1h",  productId = 0, price = 99, type = "boost", boost = "cash_x3", duration = 3600 },
    { id = "boost_luck_x3_30m", productId = 0, price = 79, type = "boost", boost = "luck_x3", duration = 1800 },
    { id = "boost_luck_x5_30m", productId = 0, price = 149, type = "boost", boost = "luck_x5", duration = 1800 },
    { id = "boost_all_x2_1h",   productId = 0, price = 199, type = "boost", boost = "all_x2", duration = 3600 },
    
    -- Packs de huevos
    { id = "pack_basic_10",    productId = 0, price = 29,  type = "eggs", egg = "egg_basic", count = 10 },
    { id = "pack_advanced_50", productId = 0, price = 199, type = "eggs", egg = "egg_advanced", count = 50 },
    { id = "pack_premium_25",  productId = 0, price = 399, type = "eggs", egg = "egg_premium", count = 25 },
    { id = "pack_cosmic_10",   productId = 0, price = 599, type = "eggs", egg = "egg_cosmic", count = 10 },
    
    -- Cash directo
    { id = "cash_10k",   productId = 0, price = 49,   type = "cash", amount = 10000 },
    { id = "cash_100k",  productId = 0, price = 199,  type = "cash", amount = 100000 },
    { id = "cash_1m",    productId = 0, price = 499,  type = "cash", amount = 1000000 },
    { id = "cash_10m",   productId = 0, price = 999,  type = "cash", amount = 10000000 },
    { id = "cash_100m",  productId = 0, price = 1999, type = "cash", amount = 100000000 },
}

ProductData.ById = {}
ProductData.ByProductId = {}
for _, product in ipairs(ProductData.List) do
    ProductData.ById[product.id] = product
    ProductData.ByProductId[product.productId] = product
end

return ProductData
```

### DailyRewards

```lua
-- ReplicatedStorage/Modules/DailyRewards.lua
local DailyRewards = {}

DailyRewards.Cycle = {
    [1] = { type = "cash",  amount = 100,        display = "$100" },
    [2] = { type = "cash",  amount = 500,        display = "$500" },
    [3] = { type = "egg",   eggId = "egg_basic", display = "Huevo Básico" },
    [4] = { type = "cash",  amount = 5000,       display = "$5K" },
    [5] = { type = "boost", boost = "luck_x3",   duration = 1800, display = "Boost Suerte 30min" },
    [6] = { type = "cash",  amount = 25000,      display = "$25K" },
    [7] = { type = "egg",   eggId = "egg_premium", display = "Huevo Premium" },
}

return DailyRewards
```


---

## ⚙️ Servicios del servidor

Documentación detallada de cada Service que vive en `ServerScriptService/Services/`.

### DataService — el más crítico

**Responsabilidad:** Cargar, guardar, cachear y migrar datos de jugadores.

**API pública:**
- `DataService:Get(player)` → devuelve los datos cacheados del player
- `DataService:UpdateData(player, mutator)` → muta y guarda los datos de forma segura
- `DataService:Save(player)` → fuerza guardado inmediato
- `DataService:GetAsync(userId)` → carga desde DataStore (uso interno)

**Comportamiento:**
- Carga datos al `PlayerAdded`
- Cachea en memoria (`cache[userId] = data`)
- Auto-save cada 60 segundos (loop)
- Save al `PlayerRemoving`
- `BindToClose` para guardar todo al cerrar server (con yield para que termine)
- Aplica migrations si `data.version < CURRENT_VERSION`
- Si DataStore falla, retry con backoff exponencial (3 intentos)
- Si tras 3 intentos sigue fallando, marca el data como "no guardar" para evitar overwrite con corrupto

**Eventos que emite:**
- Después de cada UpdateData → `DataUpdated:FireClient(player, newData)`

### PlotService

**Responsabilidad:** Asignar y gestionar plots por jugador.

**API pública:**
- `PlotService:AssignPlot(player)` → asigna un plot al jugador
- `PlotService:GetPlot(player)` → devuelve el plot del jugador
- `PlotService:GetPlotOwner(plot)` → devuelve el dueño de un plot
- `PlotService:ReleasePlot(player)` → libera el plot al salir

**Comportamiento:**
- Al `PlayerAdded`: clona PlotTemplate en posición libre, asigna OwnerId
- Carga datos de DataService y reconstruye visualmente
- Coloca cada monstruo guardado en su slot
- Aplica skin del plot
- Activa upgrades visuales (slots desbloqueados visibles)
- Spawnea al jugador en el SpawnPad
- Al `PlayerRemoving`: guarda estado, elimina plot del workspace

### MonsterService

**Responsabilidad:** Spawn/despawn de modelos de monstruos en plots.

**API pública:**
- `MonsterService:SpawnMonster(player, monsterUuid, slotIndex)` → spawn visual
- `MonsterService:RemoveMonsterFromSlot(player, slotIndex)` → despawn visual
- `MonsterService:GetMonsterInSlot(player, slotIndex)` → devuelve el modelo
- `MonsterService:UpdateMonsterVariant(player, monsterUuid)` → cambia variante visualmente

**Comportamiento:**
- Clona el modelo desde Assets/Monsters
- Aplica color/material según variante
- Añade ParticleEmitters según rareza
- Establece Configuration con metadata
- Reproduce animación Idle

### EggService

**Responsabilidad:** Lógica de compra y drop de huevos.

**API pública:**
- `EggService:BuyEgg(player, eggId)` → compra y abre un huevo
- `EggService:GetDropRates(player, eggId)` → devuelve rates ajustadas con luck

**Comportamiento:**
- Valida que tenga cash suficiente
- Calcula luck total (upgrade + gamepass + boost)
- Weighted random sobre rarityWeights ajustadas
- Selecciona monstruo random de esa rareza
- Genera UUID
- Resta cash + añade al inventory + actualiza stats + discovered
- Si Mythic+ → llama a NotificationService:GlobalAnnounce
- Llama a Analytics
- Retorna `{ success, monster, error }`

### EconomyService

**Responsabilidad:** Generación pasiva, recolección de hucha.

**API pública:**
- `EconomyService:CollectVault(player)` → recolecta cash de la hucha
- `EconomyService:GetVaultBalance(player)` → balance actual
- `EconomyService:CalculateOfflineGen(player)` → calcula gen offline al join

**Comportamiento:**
- Loop principal cada 1 segundo (configurable)
- Para cada plot activo, recorre placedMonsters y suma generación
- Aplica multiplicadores (variant, upgrade, rebirth, gamepass, boost)
- Cap el vaultBalance al máximo (vaultCapacity upgrade)
- Al `PlayerAdded`: calcula gen offline y aplica
- Al `CollectVault`: transfiere todo el balance al cash, anima

### FusionService

**Responsabilidad:** Lógica de fusiones.

**API pública:**
- `FusionService:Fuse(player, monsterUuids)` → ejecuta una fusión
- `FusionService:GetFusionPreview(monsterUuids)` → preview del resultado sin consumir

**Comportamiento:**
- Identifica qué tipo de fusión aplica
- Llama a `recipe.validate(monsters)` 
- Verifica cash suficiente para coste
- Aplica gamepass discount si aplica (FusionDiscount)
- Resta cash, elimina monstruos input del inventory
- Crea nuevo monstruo con UUID nuevo y variante apropiada
- Lo añade al inventory
- Actualiza stats.fusionsCompleted
- Retorna `{ success, newMonster, error }`

### UpgradeService

**Responsabilidad:** Compra y aplicación de upgrades del plot.

**API pública:**
- `UpgradeService:BuyUpgrade(player, upgradeId)` → compra siguiente nivel
- `UpgradeService:BuySlot(player)` → compra siguiente slot
- `UpgradeService:GetUpgradeMultipliers(player)` → multiplicadores actuales

### RebirthService

**Responsabilidad:** Lógica de rebirth.

**API pública:**
- `RebirthService:CanRebirth(player)` → check de requisitos
- `RebirthService:DoRebirth(player)` → ejecuta el rebirth

**Comportamiento:**
- Verifica que totalEarnings ≥ coste del próximo rebirth
- Resetea cash, monstruos colocados (vuelven a inventory), upgrades, slots
- Incrementa rebirth.count
- Aplica recompensa especial si tier
- Reproduce animación

### GamepassService

**Responsabilidad:** Detectar y aplicar gamepasses.

**API pública:**
- `GamepassService:HasGamepass(player, gamepassId)` → check
- `GamepassService:GetActiveGamepasses(player)` → lista
- `GamepassService:ApplyEffects(player)` → aplica efectos al joining

**Comportamiento:**
- Al `PlayerAdded`: verifica todos los gamepasses con MarketplaceService
- Cachea en data.gamepasses
- Conecta `MarketplaceService.PromptGamePassPurchaseFinished` para detectar nuevas compras
- Aplica efectos: cashMultiplier, extraSlots, autoCollect loop, walkSpeed, etc.

### ProductService

**Responsabilidad:** Developer products consumibles.

**API pública:**
- Recibe callback de `MarketplaceService.ProcessReceipt`
- Aplica efectos según ProductData
- Devuelve `Enum.ProductPurchaseDecision.PurchaseGranted`

### DailyRewardService

**Responsabilidad:** Login diario.

**API pública:**
- `DailyRewardService:CanClaim(player)` → ¿puede reclamar hoy?
- `DailyRewardService:Claim(player)` → entrega recompensa
- `DailyRewardService:GetCurrentStreak(player)` → día actual del ciclo

**Comportamiento:**
- Compara `data.daily.lastClaim` con `os.time()`
- Si > 24h sin claim → reset streak a 1
- Si < 48h → incrementa streak
- Aplica recompensa según `DailyRewards.Cycle[currentStreak]`

### NotificationService

**Responsabilidad:** Anuncios globales (drops míticos).

**API pública:**
- `NotificationService:GlobalAnnounce(player, monster)` → anuncia a todos
- `NotificationService:NotifyPlayer(player, message)` → notif privada

**Comportamiento:**
- Construye mensaje con player + monster + rarity
- `GlobalAnnouncement:FireAllClients()` con datos
- Cliente recibe y muestra banner con animación

### BoostService

**Responsabilidad:** Boosts temporales activos.

**API pública:**
- `BoostService:ActivateBoost(player, boostId, duration)` → añade boost
- `BoostService:GetActiveBoosts(player)` → lista de activos
- `BoostService:GetMultiplier(player, type)` → cash o luck multiplier

**Comportamiento:**
- Loop que cada 1s elimina boosts expirados
- Notifica al cliente cuando un boost expira

### EventService

**Responsabilidad:** Gestión de eventos limitados.

### LeaderboardService

**Responsabilidad:** Top jugadores global.

### AnalyticsService

**Responsabilidad:** Tracking a GameAnalytics.

### AntiExploitService

**Responsabilidad:** Validaciones anti-cheat.

(Detalles ampliados en sección "Anti-cheat y validaciones")

---

## 🖥 Controllers del cliente

Documentación detallada de cada Controller en `StarterPlayerScripts/Controllers/`.

### UIController

**Responsabilidad:** Gestiona qué UI está abierta.

**API:**
- `UIController:OpenMenu(menuName)` → abre y cierra otras
- `UIController:CloseAll()` → cierra todas
- `UIController:Toggle(menuName)` → toggle

**Comportamiento:**
- Solo una UI principal abierta a la vez (excepto HUD)
- Animaciones de entrada/salida con TweenService
- Bloquea movimiento del player cuando UI grande está abierta

### PlotController

**Responsabilidad:** Interacción con slots y hucha del propio plot.

**Comportamiento:**
- Click en slot vacío → abre InventoryGui en modo "place"
- Click en slot ocupado → menú "Quitar" o "Detalles"
- Click en hucha → CollectCash:FireServer()
- Hover sobre slot vacío → highlight
- Detecta cuando jugador entra a su plot vs cuando sale

### EggController

**Responsabilidad:** Animación de apertura de huevo.

**Comportamiento:**
- Recibe `EggOpened:Connect(callback)` con datos del monster
- Reproduce animación según rareza del monstruo dropeado
- CameraController:Shake() según rareza
- SoundController:Play(sound)
- Después → muestra UI "¡Has obtenido [Monster]!"

### InventoryController

**Responsabilidad:** UI de inventario.

**Comportamiento:**
- Lee `PlayerState.inventory`
- Renderiza grid con UIGridLayout
- Filtros, búsqueda, sort
- Click en monster → menú acciones
- Modo "place" cuando se invoca desde slot vacío

### FusionController

**Responsabilidad:** UI de fusión.

**Comportamiento:**
- 3 slots de input visibles
- Click en slot vacío → abre inventario en modo "select"
- Calcula y muestra preview del resultado
- Confirmar → FuseMonsters:FireServer

### ShopController

**Responsabilidad:** UI de tienda (gamepasses + dev products).

### NotificationController

**Responsabilidad:** Recibe `GlobalAnnouncement` y muestra banner.

### TutorialController

**Responsabilidad:** Tutorial primeros 30s.

**Comportamiento:**
- Si `flags.tutorialCompleted == false`:
  1. Da huevo gratis al instante
  2. Flecha animada apuntando al huevo (en HUD)
  3. Cuando lo abre → flecha apuntando al plot
  4. Cuando coloca → flecha apuntando a hucha
  5. Cuando recolecta → "¡Tutorial completado!"
- Marca flag y nunca más se muestra

### CameraController

**Responsabilidad:** Efectos de cámara.

**API:**
- `CameraController:Shake(intensity, duration)` → screen shake
- `CameraController:ZoomTo(target, duration)` → zoom in
- `CameraController:Reset()` → vuelve a cámara default

### SoundController

**Responsabilidad:** Reproducir SFX según contexto.

**API:**
- `SoundController:Play(soundName)` → reproduce 1 vez
- `SoundController:PlayMusic(track)` → cambia música
- Aplica volumen según settings del jugador

### InputController

**Responsabilidad:** Inputs de PC, móvil, gamepad.

**Comportamiento:**
- Detecta UserInputService
- Atajos de teclado (E para inventario, F para fusión, etc.)
- Botones grandes en móvil
- Soporte gamepad para Xbox

---

## 🔄 Flujos end-to-end

Esta sección muestra cómo se conectan TODAS las piezas para flujos importantes del juego.

### Flujo 1: Jugador entra al juego por primera vez

```
1. Player entra al servidor
2. Roblox crea Player object
3. ReplicatedFirst/LoadingScreen se muestra
4. Server:
   ├── PlayerAdded conecta:
   │   ├── DataService:LoadData(player)
   │   │   ├── pcall(GetAsync) con 3 retries
   │   │   ├── Si no existe → DefaultData()
   │   │   ├── Si existe → Migrate si necesario
   │   │   └── cache[userId] = data
   │   ├── PlotService:AssignPlot(player)
   │   │   ├── Clone PlotTemplate
   │   │   ├── Posición libre en grid
   │   │   ├── OwnerId = userId
   │   │   └── Spawn al jugador en SpawnPad
   │   ├── GamepassService:ApplyEffects(player)
   │   │   ├── Verifica todos los gamepasses
   │   │   └── Aplica efectos (cashMult, slots, etc.)
   │   ├── EconomyService:ApplyOfflineGen(player)
   │   │   └── vaultBalance += offlineGen
   │   └── DataUpdated:FireClient(player, data)
   └── 
5. Cliente:
   ├── PlayerState recibe data, cachea
   ├── HUD se actualiza con cash, gemas, etc.
   ├── PlotController detecta plot asignado
   ├── Si flags.tutorialCompleted == false:
   │   └── TutorialController:Start()
   ├── Si offline gen > 0:
   │   └── Popup "¡Has ganado $X mientras estabas offline!"
   └── ReplicatedFirst/LoadingScreen se oculta
```

### Flujo 2: Jugador compra y abre un huevo

```
1. Cliente:
   └── Player toca ProximityPrompt de EggMachine_Premium
   └── BuyEgg:FireServer("egg_premium")

2. Server (BuyEggHandler):
   └── Recibe → Llama a EggService:BuyEgg(player, "egg_premium")

3. EggService:
   ├── Lee EggData.Get("egg_premium") → precio = 5000
   ├── data = DataService:Get(player)
   ├── Si data.currencies.cash < 5000:
   │   └── Return { success = false, error = "Sin dinero" }
   ├── Calcula luck:
   │   ├── upgradeLuck = data.upgrades.luck × 0.05
   │   ├── gamepassLuck = HasLuckX2 ? 1.0 : 0
   │   └── boostLuck = GetActiveLuckBoost(player)
   ├── Aplica luck a rarityWeights (solo Epic+)
   ├── WeightedRandom → rareza = "Mythic" (0.5%)
   ├── MonsterData.GetByRarity("Mythic") → lista
   ├── Random pick → "capy_galactico"
   ├── newMonster = {
   │     uuid = UUID.Generate(),
   │     monsterId = "capy_galactico",
   │     variant = "Normal",
   │     obtainedAt = os.time(),
   │     locked = false
   │   }
   ├── DataService:UpdateData(player, function(data)
   │     data.currencies.cash -= 5000
   │     table.insert(data.inventory, newMonster)
   │     data.discovered["capy_galactico"] = true
   │     data.stats.eggsOpened += 1
   │     data.stats.monstersObtained += 1
   │     return data
   │   end)
   ├── Como Mythic → NotificationService:GlobalAnnounce(player, newMonster)
   │   └── GlobalAnnouncement:FireAllClients(player.Name, monster, rarity)
   ├── EggOpened:FireClient(player, newMonster)
   └── AnalyticsService:Track("egg_opened", { eggId = "egg_premium", rarity = "Mythic" })

4. Cliente:
   ├── PlayerState recibe DataUpdated, cachea
   ├── HUD actualiza cash automáticamente
   ├── EggController recibe EggOpened:
   │   ├── Reproduce animación de apertura (4s para Mythic)
   │   ├── CameraController:Shake(intensity=10, duration=4)
   │   ├── SoundController:Play("DropMythic")
   │   └── Después: muestra "¡Has obtenido Capybara Galáctico!"
   ├── NotificationController recibe GlobalAnnouncement (todos los clientes):
   │   ├── Renderiza banner con player + monster
   │   └── SoundController:Play("AnnouncementMythic")
   └── InventoryController detecta cambio en data.inventory:
       └── Refresca grid si está abierto
```

### Flujo 3: Jugador coloca un monstruo en slot

```
1. Cliente:
   ├── Player click en Slot_5 (vacío)
   ├── PlotController detecta click
   └── UIController:OpenMenu("Inventory") en modo "place_in_slot_5"

2. Cliente (InventoryController en modo place):
   └── Player click en monster del grid
   └── PlaceMonster:FireServer(monsterUuid, slotIndex=5)

3. Server (PlaceMonsterHandler):
   └── Llama a PlotService:PlaceMonster(player, monsterUuid, 5)

4. PlotService:
   ├── data = DataService:Get(player)
   ├── Validaciones:
   │   ├── ¿uuid existe en inventory? → sí
   │   ├── ¿slot 5 está unlocked? → sí
   │   ├── ¿slot 5 está vacío? → sí
   │   └── Si alguna falla → Return { success = false }
   ├── DataService:UpdateData(player, function(data)
   │     data.placedMonsters[5] = monsterUuid
   │     return data
   │   end)
   ├── MonsterService:SpawnMonster(player, monsterUuid, 5)
   │   ├── Clona modelo desde Assets/Monsters
   │   ├── Aplica variante (color, material, particles)
   │   ├── Coloca en posición de Slot_5
   │   ├── Reproduce animación Idle
   │   └── Establece Configuration con metadata
   └── MonsterPlaced:FireClient(player, slotIndex=5, monster)

5. Cliente:
   ├── Cierra UI de inventario
   ├── Cámara enfoca brevemente el slot
   └── Slot ahora tiene el modelo del monstruo visible
```

### Flujo 4: Generación pasiva tick

```
Server (EconomyService loop, cada 1 segundo):
└── Para cada plot activo:
    └── Para cada slotIndex, monsterUuid en plot.placedMonsters:
        ├── monster = data.inventory[uuid]
        ├── monsterData = MonsterData.Get(monster.monsterId)
        ├── baseGen = monsterData.baseGen
        ├── variantMult = RarityConfig.Variants[monster.variant].multiplier
        ├── upgradeMult = 1 + (data.upgrades.global_multiplier × 0.10)
        ├── rebirthMult = RebirthData.GetTotalMultiplier(data.rebirth.count)
        ├── gamepassMult = HasVIP(player) ? 2 : 1
        ├── boostMult = BoostService:GetMultiplier(player, "cash")
        ├── 
        ├── totalGen = baseGen × variantMult × upgradeMult × rebirthMult × gamepassMult × boostMult
        ├── 
        ├── DataService:UpdateData(player, function(data)
        │     data.plot.vaultBalance = math.min(
        │       data.plot.vaultBalance + totalGen,
        │       vaultMaxCapacity
        │     )
        │     return data
        │   end)
        └── (UpdateData dispara DataUpdated:FireClient)

Cliente:
├── PlayerState recibe DataUpdated
├── HUD actualiza cash si cambió
├── PlotController detecta cambio en vaultBalance:
│   ├── Actualiza BillboardGui de hucha
│   └── Si gen > 0, muestra texto "+$X" flotante sobre cada monstruo
```

### Flujo 5: Fusión de 2 Doradas → 1 Arcoíris

```
1. Cliente:
   ├── Player abre FusionGui
   ├── Mete 2 monstruos Dorados del mismo monsterId en slots 1 y 2
   ├── UI muestra preview: "Forjar Arcoíris"
   ├── Costo: $250.000 (con fusion_discount sería $125.000)
   └── Player click "Confirmar"
   └── FuseMonsters:FireServer({uuid1, uuid2})

2. Server (FuseMonstersHandler):
   └── Llama a FusionService:Fuse(player, {uuid1, uuid2})

3. FusionService:
   ├── data = DataService:Get(player)
   ├── monsters = ResolveUUIDs(data, {uuid1, uuid2})
   │   └── Retorna las instancias completas
   ├── Identifica recipe:
   │   ├── ¿2 inputs, mismo monsterId, ambos Golden? → Recipe = "Rainbow"
   │   └── Recipe = FusionRecipes.Types.Rainbow
   ├── Llama a recipe.validate(monsters):
   │   └── Devuelve true (validado)
   ├── cost = recipe.cost = 250000
   ├── Si HasGamepass("fusion_discount"): cost *= 0.5 = 125000
   ├── Si data.currencies.cash < cost:
   │   └── Return { success = false, error = "Sin dinero" }
   ├── 
   ├── newMonster = {
   │     uuid = UUID.Generate(),
   │     monsterId = monsters[1].monsterId,
   │     variant = "Rainbow",
   │     obtainedAt = os.time(),
   │     locked = false
   │   }
   ├── 
   ├── DataService:UpdateData(player, function(data)
   │     data.currencies.cash -= cost
   │     -- Eliminar inputs del inventory
   │     for i = #data.inventory, 1, -1 do
   │       if data.inventory[i].uuid == uuid1 or data.inventory[i].uuid == uuid2 then
   │         table.remove(data.inventory, i)
   │       end
   │     end
   │     -- Si alguno estaba colocado, removerlo del slot
   │     for slotIdx, uuid in pairs(data.placedMonsters) do
   │       if uuid == uuid1 or uuid == uuid2 then
   │         data.placedMonsters[slotIdx] = nil
   │         MonsterService:RemoveMonsterFromSlot(player, slotIdx)
   │       end
   │     end
   │     -- Añadir nuevo
   │     table.insert(data.inventory, newMonster)
   │     data.stats.fusionsCompleted += 1
   │     return data
   │   end)
   ├── 
   └── Return { success = true, newMonster = newMonster }

4. Cliente (FusionController recibe respuesta):
   ├── Reproduce animación de fusión:
   │   ├── 2 monstruos sobre altar girando
   │   ├── Aceleración + rayos conectándolos
   │   ├── Explosión de luz blanca (1s)
   │   └── Aparece nuevo monstruo Rainbow
   ├── Sound: "Fusion"
   ├── UI: "¡FUSIÓN EXITOSA! Has obtenido [Monster] Arcoíris"
   ├── PlayerState ya actualizado (DataUpdated)
   └── InventoryController refresca
```

### Flujo 6: Rebirth

```
1. Cliente:
   ├── Player interactúa con Altar de Rebirth
   ├── UI muestra: "Rebirth #1 - Coste: $10.000.000 - Bonus: +25% gen"
   ├── Si data.rebirth.totalEarnings >= 10M → botón activo
   └── Player confirma → DoRebirth:FireServer()

2. Server (DoRebirthHandler):
   └── Llama a RebirthService:DoRebirth(player)

3. RebirthService:
   ├── data = DataService:Get(player)
   ├── nextRebirth = data.rebirth.count + 1
   ├── cost = RebirthData.GetCost(nextRebirth) = 10M
   ├── Si data.rebirth.totalEarnings < cost:
   │   └── Return { success = false }
   ├── 
   ├── reward = RebirthData.Tiers[nextRebirth] and .reward
   ├── 
   ├── DataService:UpdateData(player, function(data)
   │     -- RESET
   │     data.currencies.cash = 0
   │     data.plot.vaultBalance = 0
   │     data.plot.slotsUnlocked = 4
   │     data.upgrades = { global_multiplier=0, luck=0, ... }
   │     -- Mover placed back to inventory
   │     for slotIdx, uuid in pairs(data.placedMonsters) do
   │       MonsterService:RemoveMonsterFromSlot(player, slotIdx)
   │     end
   │     data.placedMonsters = {}
   │     -- BONUS
   │     data.rebirth.count = nextRebirth
   │     data.flags.firstRebirth = true
   │     -- REWARD si aplica
   │     if reward then
   │       ApplyReward(data, reward)
   │     end
   │     return data
   │   end)
   ├── Return { success = true, newRebirth = nextRebirth }

4. Cliente:
   ├── Animación de Rebirth (3s)
   ├── Pantalla oscurece, partículas doradas, ascensión
   ├── Sound: "Rebirth"
   ├── UI: "¡Rebirth #1 completado! +25% generación permanente"
   └── PlayerState actualizado, todo se refresca
```


---

## 🛡 Anti-cheat y validaciones

En Roblox, el cliente NO es de confianza. Cualquier validación que solo hagas en cliente puede ser bypaseada por exploiters con executors (Synapse, KRNL, etc.). La regla de oro: **el servidor es la única fuente de verdad**.

### Reglas absolutas

#### 1. Nunca confíes en datos del cliente

❌ **MAL — el cliente decide cuánto cash tiene:**
```lua
-- Cliente envía cash actual
BuyEgg:FireServer("egg_premium", playerCash)
-- Server confía en playerCash → EXPLOITABLE
```

✅ **BIEN — el server lee desde DataService:**
```lua
-- Cliente solo envía qué quiere comprar
BuyEgg:FireServer("egg_premium")
-- Server lee data desde DataService:Get(player)
local data = DataService:Get(player)
if data.currencies.cash < eggPrice then return end
```

#### 2. Validar TODOS los inputs en el server

El cliente puede enviar cualquier cosa por RemoteEvent. Validaciones obligatorias en cada Handler:

```lua
-- Ejemplo: BuyEggHandler
BuyEgg.OnServerEvent:Connect(function(player, eggId)
    -- 1. Tipo correcto
    if typeof(eggId) ~= "string" then return end
    
    -- 2. Existe en config
    local egg = EggData.Get(eggId)
    if not egg then return end
    
    -- 3. El player puede acceder a esto en este momento
    if not PlotService:GetPlot(player) then return end
    
    -- 4. Rate limiting (máx 10 huevos/segundo)
    if not RateLimiter:Check(player, "BuyEgg", 10, 1) then return end
    
    -- 5. AHORA llamar al service
    EggService:BuyEgg(player, eggId)
end)
```

#### 3. Rate limiting agresivo

Limitar cuántas veces por segundo se puede llamar cada Remote:

```lua
-- ServerScriptService/Services/RateLimiter.lua
local RateLimiter = {}
local buckets = {} -- buckets[userId][actionId] = { count, resetTime }

function RateLimiter:Check(player, actionId, maxCalls, windowSeconds)
    local userId = player.UserId
    buckets[userId] = buckets[userId] or {}
    
    local bucket = buckets[userId][actionId]
    local now = tick()
    
    if not bucket or now > bucket.resetTime then
        buckets[userId][actionId] = { count = 1, resetTime = now + windowSeconds }
        return true
    end
    
    if bucket.count >= maxCalls then
        AnalyticsService:Track(player, "rate_limit_hit", { action = actionId })
        return false
    end
    
    bucket.count += 1
    return true
end

-- Limpieza al salir
game.Players.PlayerRemoving:Connect(function(player)
    buckets[player.UserId] = nil
end)

return RateLimiter
```

Límites recomendados por acción:

| Acción | Max calls | Window | Justificación |
|---|---|---|---|
| BuyEgg | 10 | 1s | 10 huevos/seg con triple_hatch es el máximo legítimo |
| PlaceMonster | 5 | 1s | Es manual, no debería ir más rápido |
| RemoveMonster | 5 | 1s | Igual |
| CollectCash | 3 | 1s | Manual |
| FuseMonsters | 3 | 1s | Manual |
| BuyUpgrade | 5 | 1s | Manual |
| DoRebirth | 1 | 5s | Es lento por animación |
| SellMonster | 10 | 1s | Vender en bulk |
| ClaimDailyReward | 1 | 60s | Solo 1 vez/día realmente |

#### 4. Validar pertenencia de cada UUID

Cuando el cliente manda un UUID de monstruo, el server DEBE verificar que ese UUID está en el inventory del jugador:

```lua
function FusionService:_validateOwnership(player, monsterUuids)
    local data = DataService:Get(player)
    local found = {}
    
    for _, uuid in ipairs(monsterUuids) do
        local monster = nil
        for _, m in ipairs(data.inventory) do
            if m.uuid == uuid then
                monster = m
                break
            end
        end
        if not monster then
            return false, "Monster not found in inventory"
        end
        if monster.locked then
            return false, "Monster is locked"
        end
        if found[uuid] then
            return false, "Duplicate UUID"
        end
        found[uuid] = monster
    end
    
    return true, found
end
```

#### 5. Sanity checks de cantidades

Validar que las cantidades tengan sentido:

```lua
-- Ejemplo: SellMonsters bulk
SellMonsters.OnServerEvent:Connect(function(player, monsterUuids)
    -- Tipo correcto
    if typeof(monsterUuids) ~= "table" then return end
    
    -- Cantidad razonable (no 999999 UUIDs)
    if #monsterUuids > 100 then return end
    
    -- Cada uuid es string
    for _, uuid in ipairs(monsterUuids) do
        if typeof(uuid) ~= "string" or #uuid > 50 then return end
    end
    
    EconomyService:BulkSell(player, monsterUuids)
end)
```

#### 6. Detectar acciones imposibles

Patrones que solo un exploiter haría:

```lua
-- Ejemplo: jugador tiene cash que no debería poder tener
-- Su totalEarnings dice 1M pero tiene 1B en cash → SOSPECHOSO
function AntiExploitService:CheckCashSanity(player)
    local data = DataService:Get(player)
    if data.currencies.cash > data.stats.cashEarned * 2 then
        -- Logear, posible kick o ban
        warn("[AntiExploit] " .. player.Name .. " has impossible cash")
        AnalyticsService:Track(player, "anti_exploit_triggered", {
            type = "impossible_cash",
            cash = data.currencies.cash,
            cashEarned = data.stats.cashEarned
        })
    end
end
```

#### 7. Velocidad de movimiento (para futuro Speed Coil gamepass)

```lua
local MAX_WALKSPEED = 32  -- Walk normal
local VIP_WALKSPEED = 48  -- Con Speed Coil

game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        local humanoid = character:WaitForChild("Humanoid")
        humanoid:GetPropertyChangedSignal("WalkSpeed"):Connect(function()
            local maxAllowed = HasGamepass(player, "speed_coil") and VIP_WALKSPEED or MAX_WALKSPEED
            if humanoid.WalkSpeed > maxAllowed then
                humanoid.WalkSpeed = maxAllowed
                -- Logear
            end
        end)
    end)
end)
```

#### 8. Detección de teleport

Si un jugador se teletransporta a un plot que no es suyo:

```lua
local TELEPORT_THRESHOLD = 50 -- studs por frame

game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        local rootPart = character:WaitForChild("HumanoidRootPart")
        local lastPosition = rootPart.Position
        
        local conn
        conn = game:GetService("RunService").Heartbeat:Connect(function()
            if not character.Parent then conn:Disconnect() return end
            
            local newPosition = rootPart.Position
            local distance = (newPosition - lastPosition).Magnitude
            
            if distance > TELEPORT_THRESHOLD then
                -- Teleport ilegal
                rootPart.CFrame = CFrame.new(lastPosition)
                AnalyticsService:Track(player, "teleport_detected", { distance = distance })
            end
            
            lastPosition = newPosition
        end)
    end)
end)
```

#### 9. Encriptar/ofuscar nombres de RemoteEvents (opcional)

Los exploiters buscan RemoteEvents por nombre. Renombrar a algo críptico añade fricción:

```
BuyEgg → R_E_001
PlaceMonster → R_E_002
```

Nota: NO es seguridad real (un exploiter puede listar todos los RemoteEvents igualmente), pero filtra los script kiddies.

#### 10. Server-side cooldowns en acciones críticas

Para acciones como rebirth, daily claim:

```lua
local cooldowns = {} -- cooldowns[userId][action] = expiresAt

function RebirthService:DoRebirth(player)
    local userId = player.UserId
    cooldowns[userId] = cooldowns[userId] or {}
    
    local now = tick()
    if cooldowns[userId].rebirth and now < cooldowns[userId].rebirth then
        return { success = false, error = "Espera antes de hacer otro rebirth" }
    end
    
    -- ... lógica del rebirth ...
    
    cooldowns[userId].rebirth = now + 10 -- 10 segundos cooldown
end
```

### Auditoría y logging

Todas las acciones críticas deben dejar un log:

```lua
-- ServerScriptService/Services/AuditLogService.lua
function AuditLogService:Log(player, action, details)
    local entry = {
        userId = player.UserId,
        userName = player.Name,
        action = action,
        details = details,
        timestamp = os.time()
    }
    
    -- A GameAnalytics o DataStore propio
    AnalyticsService:Track(player, "audit_" .. action, entry)
    
    -- Si es acción muy crítica, también a DataStore de auditoría
    if action == "rebirth" or action == "robux_purchase" then
        local AuditStore = DataStoreService:GetDataStore("AuditLog_v1")
        pcall(function()
            AuditStore:SetAsync(HttpService:GenerateGUID(), entry)
        end)
    end
end
```

### Sistema de reportes (futuro)

Players pueden reportar a otros:
- UI de reporte con motivos (exploit, scam, toxic)
- Server logea con contexto
- Si un usuario recibe N reportes → review manual o auto-ban temporal

### Trampas anti-cheat (honeypots)

Crear "monstruos fantasma" en client side que NO existen en server. Si un exploiter intenta venderlos/fundirlos → ban automático:

```lua
-- Cliente recibe data fake
PlayerState.fakeMonsters = { uuid = "FAKE_001", monsterId = "trap" }

-- Si cliente envía SellMonster con UUID "FAKE_001"
SellMonster.OnServerEvent:Connect(function(player, uuid)
    if uuid == "FAKE_001" then
        -- ¡Cazado!
        AnalyticsService:Track(player, "honeypot_triggered")
        player:Kick("Detected exploit attempt")
        -- O ban con DataStore de bans
    end
end)
```

---

## 🎨 Assets necesarios

Lista completa de assets a obtener/crear, organizados por prioridad para el lanzamiento.

### Modelos 3D — Monstruos

#### Recursos gratis del Creator Store/DevForum

**Cigatronix Free Pet Pack** (300+ pets simulator)
- Búsqueda en Toolbox: "Cigatronix Pet Pack"
- Cobertura: ~80% del catálogo Common/Rare/Epic
- Calidad: alta, low-poly cartoon style
- Licencia: free use

**Aziuus Pet Pack** (28 pets cartoon)
- Búsqueda: "Aziuus pets"
- Cobertura: complementa al de Cigatronix
- Estilo: similar, integrable

**Nobuser Pet Pack** (5+5 doradas + huevos)
- Para variantes Doradas pre-hechas
- También trae huevos

**Pet Simulator UGC packs** del Toolbox
- Buscar "Pet Simulator inspired"
- Cuidado con copyrights — solo usar si se marca como "free use"

#### Para nichos específicos a crear

Si no encuentras buenos para Mythic+, opciones:

1. **Comisionar a freelancer** (Fiverr, ~$10-30 por modelo)
2. **Asset Forge** (software para crear low-poly models drag-and-drop)
3. **Blender + tutorial** (1-2 días aprendiendo si no sabes)
4. **AI generation** (Meshy.ai, alpha3d.io — calidad variable)

#### Tabla de prioridad de modelos

| Prioridad | Cuáles | Cantidad | Estrategia |
|---|---|---|---|
| **P1 (lanzamiento)** | Common (12) + Rare (10) + Epic (8) | 30 | Toolbox |
| **P2 (lanzamiento)** | Legendary (6) + Mythic (5) | 11 | Toolbox o comisionar |
| **P3 (post-launch)** | Secret (4) + Divine (4) | 8 | Comisionar para que sean únicos |

### Modelos 3D — Huevos

6 modelos de huevo, uno por cada tipo:

- **Huevo Básico** — gris simple, glow blanco suave
- **Huevo Avanzado** — azul cristal, glow verde
- **Huevo Premium** — morado mate, sparkles
- **Huevo Cósmico** — galáctico con texture de estrellas
- **Huevo Divino** — dorado con cracks luminosos
- **Huevo VIP** — negro brillante con detalles dorados

Recurso: **Tycoon Kit** del Toolbox suele incluir huevos básicos. Para los premium, comisionar o modificar el básico.

### Modelos 3D — Plot

#### Estructura base

- **PlotTemplate** completo: usar **"Tycoon Kit"** del Toolbox como punto de partida
- Modificar para añadir:
  - Slots con indicador (Part transparente con neón)
  - Hucha (chest model con BillboardGui)
  - Altar de fusión (modelo distintivo, central)
  - Botones de upgrade (5 pedestales con BillboardGuis)
  - Altar de Rebirth (desbloqueable visualmente)
  - Cartel con nombre

#### Skins de plot

- **Default** — neon rosa, suelo standard
- **Espacial** — modificar materiales y añadir skybox local
- **Lava** — material magma + ParticleEmitter de fuego
- **Crystal** — Glass material + cristales decorativos
- **Toxic** — material verde + charcos animados
- **Royal** — dorado/morado + fuente
- **Halloween/Christmas/Valentine** — para eventos

Recurso: el **Tycoon Map and Assets** del Toolbox tiene varias propuestas.

### Modelos 3D — Hub

- **Plataforma central** — base flotante con neón
- **Edificios** — casetas para cada egg machine
- **Decoraciones** — fuentes, estatuas, banners, plantas
- **NPCs** — modelos humanoid simples (Toolbox tiene muchos)
- **Teleporters** — pads circulares animados
- **Leaderboard** — cartel grande con SurfaceGui

### Animaciones

#### Animaciones de monstruos

- **Idle** (todos) — movimiento sutil, respiración
- **Eat** (todos) — cuando "genera dinero" (cada 3s)
- **Special** (variantes Void) — pose épica ocasional

Recursos:
- **Roblox Animation Library** tiene packs gratis
- **Mixamo** tiene animaciones humanoid (no aplica directamente, pero útil para NPCs)
- Crear simples con el **Animation Editor** integrado de Studio

#### Animaciones de huevos

- **Idle** — flotación + rotación lenta
- **Shake** — tembleque pre-eclosión
- **Hatch** — apertura

#### Animaciones de UI

Todas con TweenService en código (no hace falta importar):
- Apertura de menú: `Scale 0 → 1` con `Easing.Back`
- Notificación: `Position offscreen → onscreen` con `Easing.Back.Out`
- Botón hover: `Size +5% → +0%` con `Easing.Quad`

### Particle Emitters (VFX)

Crear directamente en Studio o importar desde "VFX free pack" del Toolbox:

- **Rarity Glow** (uno por rareza, 7 total) — color según RarityConfig
- **Egg Hatch** — explosión de luz blanca + sparkles del color de rareza
- **Cash Collect** — magnetic effect (monedas → HUD)
- **Fusion Beam** — rayo conectando 2-3 puntos
- **Rebirth Burst** — explosión dorada épica
- **Variant Trails** — trail multicolor para Rainbow, oscuro para Void

### UI — Templates de ScreenGuis

Recursos gratis:

- **FramedBlake Free Simulator UI Kit** (DevForum) — completo
- **Biscuit's Simulator UI Kit** — alternativa
- **Lua Box UI Kit** — más minimalista
- **GFX Comet UI Kit** — para promociones/thumbnails

Personalización:
- Reemplazar colores con paleta del juego (#FF2E88, #00F0FF, etc.)
- Cambiar fonts a Bebas Neue + Inter
- Añadir efectos neón con `UIStroke`

### Iconos UI

Iconos para botones, currencies, monstruos:

- **Material Icons** (Google) — packs gratis, monocromos
- **Font Awesome** — iconos vectoriales
- **Roblox Built-in Icons** — Asset IDs nativos
- **Iconscout** — packs free para games
- Para los iconos de monstruos en inventario: renderizar cada modelo con ViewportFrame en Studio y guardar PNG

### Sonidos (SFX y Música)

#### Música

- **Roblox Audio Library** — buscar "loop ambient nocturne", "synthwave loop"
- **Pixabay** — música free para games
- **YouTube Audio Library** — algunos tracks free use
- **Buy 1 track** en AudioJungle (~$15) si quieres algo único

#### SFX

Lista crítica:
- **EggCrack** + **EggHatch**
- **DropCommon, DropRare, DropEpic, DropLegendary, DropMythic, DropSecret, DropDivine** (escalado de épica)
- **CashCollect** (cha-ching)
- **CashEarn** (ding pequeño cuando genera)
- **Fusion** (chime épico)
- **Rebirth** (fanfarria)
- **ButtonClick, ButtonHover** (UI)
- **PurchaseSuccess, ErrorSound**
- **NotificationDing**
- **AnnouncementMythic, AnnouncementSecret, AnnouncementDivine** (épicos)

Recursos:
- **Freesound.org** — banco enorme bajo CC
- **Mixkit** — packs de game SFX gratis
- **Roblox Audio Library** (después de la actualización de audio license, hay que filtrar bien)

### Imágenes — Marketing

#### Thumbnails y promo

- **Thumbnail principal** (1280×720) — el que aparece en la lista de games
- **Iconos del game** (512×512) — el cuadrado pequeño
- **In-game thumbnails** — para anuncios internos de eventos

Recursos:
- **GFX freelancer en Fiverr** ($5-25 por thumbnail)
- **Photoshop/GIMP** + render de Studio
- **GFX Comet UI Kit** tiene templates de thumbnails

#### Trailer

- 30-60 segundos
- Mostrar: gameplay loop, drops míticos, fusiones épicas, rebirth
- Música pegadiza (royalty-free)
- Editar con DaVinci Resolve (gratis) o CapCut

### Lista de assets prioritaria para sem 1

Lo MÍNIMO que necesitas para empezar a desarrollar:

```
✅ Tycoon Kit (Toolbox) — base del plot
✅ Cigatronix Pet Pack (Toolbox) — primeros 30 monstruos
✅ Aziuus Pet Pack (Toolbox) — más monstruos
✅ Nobuser Pet Pack (Toolbox) — variantes Doradas + huevos
✅ FramedBlake UI Kit — templates de UI
✅ 1 música de ambiente (Roblox Library)
✅ Pack básico de SFX (Freesound)
```

Con esto puedes desarrollar sem 1-3. Sem 4 ya inviertes en thumbnail decente y SFX premium.

---

## 📅 Roadmap de desarrollo (4 semanas)

Plan realista asumiendo **3-4 horas/día** de desarrollo. Total: ~60 horas de trabajo.

### Sem 1: Foundation (12-15 horas)

**Objetivo:** infraestructura técnica + Hub básico funcionando

#### Día 1-2 (5h): Setup

- [ ] Crear nuevo Studio Place
- [ ] Instalar **Rojo** (extensión VS Code + plugin Studio)
- [ ] Inicializar repo Git con `.gitignore` para Roblox
- [ ] Estructura de carpetas en Studio
- [ ] Configurar Game Settings (StreamingEnabled, dispositivos, etc.)
- [ ] Importar **Tycoon Kit** del Toolbox
- [ ] Importar primeros pet packs (Cigatronix + Aziuus)
- [ ] Asignar pets a MonsterData.lua con sus IDs

#### Día 3-4 (5h): Core data

- [ ] Crear todos los **ModuleScripts de configuración** (RarityConfig, MonsterData, EggData, FusionRecipes, UpgradeData, RebirthData)
- [ ] Crear todas las RemoteEvents y RemoteFunctions vacías
- [ ] Crear **Utils** (Format, Random, UUID, Validate)
- [ ] Crear **DataService** completo:
  - LoadData, SaveData, UpdateData, BindToClose
  - Migrations system
  - DefaultData()
  - Probar con Studio Test que se guarda y carga

#### Día 5-7 (4h): Hub

- [ ] Diseñar Hub básico (plataforma + suelo + neones)
- [ ] Configurar **Lighting** (Atmosphere, Sky, Bloom, ColorCorrection)
- [ ] Colocar 6 **EggMachines** con ProximityPrompt
- [ ] **PlotService** mínimo:
  - AssignPlot al joining
  - Spawn al jugador en plot
  - ReleasePlot al salir
- [ ] Probar con 2-3 jugadores en Studio Test que cada uno tiene su plot

**Hito sem 1:** jugador entra, tiene su plot vacío, puede caminar por Hub

### Sem 2: Core Loop (15-18 horas)

**Objetivo:** loop principal del juego funcionando end-to-end

#### Día 8-9 (5h): Sistema de slots

- [ ] **PlotTemplate** con 50 slots configurados
- [ ] Indicadores visuales (slots libres brillan)
- [ ] **PlotController** detecta clicks en slots
- [ ] **InventoryGui** básico (grid)
- [ ] **PlaceMonster** flow completo (cliente → server → spawn)
- [ ] **MonsterService:SpawnMonster()** con animaciones

#### Día 10-11 (5h): Sistema de huevos

- [ ] **EggService:BuyEgg()** completo con weighted random
- [ ] Animación de apertura (5 niveles según rareza)
- [ ] **CameraController** con shake y zoom
- [ ] **SoundController** con SFX de drop
- [ ] **EggController** en cliente

#### Día 12-13 (5h): Generación pasiva

- [ ] **EconomyService** con loop de generación cada 1s
- [ ] Texto flotante "+$X" con BillboardGui temporal
- [ ] **Hucha** con BillboardGui mostrando balance
- [ ] **CollectCash** flow completo
- [ ] Animación magnetic de monedas hacia HUD
- [ ] Generación offline al rejoin

#### Día 14 (3h): Save/Load + UI inventario

- [ ] **InventoryGui** completo:
  - Tabs por rareza
  - Filtros, búsqueda, sort
  - Item template con todas las acciones
- [ ] **HUD** con cash, gemas, rebirth count
- [ ] Probar persistencia (Studio Test → cerrar → reabrir → datos siguen)

**Hito sem 2:** loop completo funcional. Compras huevos, te salen monstruos, los colocas, generan dinero, recoges, lo gastas en más huevos.

### Sem 3: Polish + Hooks (15-18 horas)

**Objetivo:** sistemas que enganchan + pulido

#### Día 15-16 (5h): Sistema de fusión

- [ ] **FusionAltar** en plot
- [ ] **FusionGui** con 3 slots de input
- [ ] **FusionService** con todas las recipes
- [ ] Animación de fusión completa (girar + rayos + explosión)
- [ ] Variantes visuales (Golden material, Rainbow ColorSequence, Void dark)

#### Día 17-18 (5h): Rebirth + Upgrades

- [ ] **RebirthAltar** en plot
- [ ] **RebirthGui** con preview de coste/bonus
- [ ] **RebirthService** completo
- [ ] Animación de rebirth
- [ ] **5 upgrade buttons** físicos con BillboardGui
- [ ] **UpgradeService** completo
- [ ] **Slot purchase** flow

#### Día 19-20 (5h): Juice + Anuncios

- [ ] Particle emitters según rareza en monstruos
- [ ] Glow ambient (PointLight con rarityColor)
- [ ] **NotificationService:GlobalAnnounce** completo
- [ ] **NotificationGui** con banner animado
- [ ] Música de fondo (Hub vs Plot)
- [ ] SFX para todas las acciones
- [ ] **TutorialController** primeros 30s
- [ ] **DailyRewardService** + UI

**Hito sem 3:** juego se siente PULIDO. Drops míticos crean hype, fusiones se sienten épicas, tutorial guía a nuevos jugadores.

### Sem 4: Launch (12-15 horas)

**Objetivo:** monetización + lanzamiento

#### Día 21-22 (4h): Gamepasses

- [ ] Crear los 5 gamepasses Tier 1 en el dashboard de Roblox:
  - Auto Collect (99 R$)
  - VIP (499 R$)
  - Suerte ×2 (399 R$)
  - Inventario Expandido (199 R$)
  - Offline 24h (299 R$)
- [ ] Actualizar IDs en GamepassData.lua
- [ ] **GamepassService** con detección y aplicación
- [ ] **ShopGui** con todos los gamepasses

#### Día 23 (3h): Developer Products

- [ ] Crear Developer Products en dashboard:
  - Currency packs (4 tiers)
  - Boosts temporales (5 tipos)
  - Packs de huevos (4 tipos)
  - Cash directo (5 tiers)
- [ ] **ProductService** con ProcessReceipt
- [ ] UI de tienda completa

#### Día 24 (3h): Testing + balance

- [ ] Playtest interno (con amigos)
- [ ] Detectar bugs y crashes
- [ ] Ajustar balance de drops si parece roto
- [ ] Testear en mobile (importante!)
- [ ] Optimizar performance (StreamingEnabled, LOD)

#### Día 25-26 (3h): Marketing assets

- [ ] **Thumbnail** principal (Fiverr o Photoshop)
- [ ] **Game icon** (512×512)
- [ ] **Screenshots** in-game
- [ ] **Trailer** 30-60s
- [ ] **TikTok teaser** 15s

#### Día 27 (2h): Soft launch

- [ ] Publicar el game (Public)
- [ ] Postear en grupos de Roblox dev
- [ ] Compartir en Discord
- [ ] Primer TikTok

**Hito sem 4:** ¡juego LIVE!

### Buffer time

Importante: este plan asume todo va bien. **Realidad:** suma 30-50% más para imprevistos:

- Bugs raros de DataStore
- Toolbox assets que no funcionan
- Detalles de UI que llevan más de lo previsto
- Roblox actualiza algo y rompe cosas
- Studio crashea

**Plan realista:** 6-8 semanas para v1 completa.

---

## 💵 Plan de monetización

### Filosofía

- **F2P-friendly real:** el juego se completa sin pagar (en 500h, pero se completa)
- **Quality of life paga:** auto-collect, offline 24h, inventario expandido
- **Rapidez paga:** boosts, packs de huevos
- **Estética paga:** tags VIP, plot skins (post-launch)
- **NUNCA pay-to-win exclusivo:** todo lo Robux también es alcanzable F2P (más lento)

### Tier 1 — Lanzamiento (5 gamepasses críticos)

| Gamepass | Precio | Por qué funciona |
|---|---|---|
| **Auto Collect** | 99 R$ | Quality of life. Conversion alta porque es barato. |
| **VIP** | 499 R$ | ×2 cash + slot extra + tag. El "must have" de los whales. |
| **Suerte ×2** | 399 R$ | Doblar chances de raros. Apela a la dopamina. |
| **Inventario Expandido** | 199 R$ | Cuando llenan 100 slots, lo compran sí o sí. |
| **Offline 24h** | 299 R$ | Para jugadores casuales. Vuelven y han ganado mucho. |

### Tier 2 — Mes 1-2 (más gamepasses)

| Gamepass | Precio | Cuándo añadir |
|---|---|---|
| Triple Hatch | 249 R$ | Sem 5+ — para jugadores que abren mucho |
| Auto Hatch | 199 R$ | Sem 6+ — combina con Triple |
| Speed Coil | 149 R$ | Sem 5+ — quality of life |
| Fusion Discount | 349 R$ | Sem 7+ — para mid-game |
| Lucky Block Pet | 599 R$ | Sem 8+ — cosmético + bonus |

### Tier 3 — Mes 2+ (premium)

| Gamepass | Precio |
|---|---|
| Ultra VIP (combo) | 1499 R$ |
| Mega Plot (skin premium) | 999 R$ |

### Developer Products consumibles

#### Currency packs (gemas)

| Producto | Precio | Cantidad | Valor |
|---|---|---|---|
| Pack Pequeño | 49 R$ | 100 gems | – |
| Pack Mediano | 199 R$ | 500 gems | "POPULAR" |
| Pack Grande | 499 R$ | 1500 gems | "MEJOR VALOR" |
| Pack Mega | 999 R$ | 3500 gems | – |

#### Boosts temporales

| Boost | Precio | Duración |
|---|---|---|
| Cash ×2 30min | 49 R$ | 30 min |
| Cash ×3 1h | 99 R$ | 1 h |
| Suerte ×3 30min | 79 R$ | 30 min |
| Suerte ×5 30min | 149 R$ | 30 min |
| Todo ×2 1h | 199 R$ | 1 h |

#### Packs de huevos

| Pack | Precio | Contenido |
|---|---|---|
| 10× Básicos | 29 R$ | 10 huevos básicos |
| 50× Avanzados | 199 R$ | 50 huevos avanzados |
| 25× Premium | 399 R$ | 25 huevos premium |
| 10× Cósmicos | 599 R$ | 10 huevos cósmicos |

#### Cash directo

| Producto | Precio | Cash |
|---|---|---|
| $10K | 49 R$ | $10.000 |
| $100K | 199 R$ | $100.000 |
| $1M | 499 R$ | $1.000.000 |
| $10M | 999 R$ | $10.000.000 |
| $100M | 1999 R$ | $100.000.000 |

### Ofertas dinámicas (post-launch)

#### Bundle de bienvenida (primera compra)

- "Welcome Pack" — primera compra de Robux
- Incluye: $50K cash + 100 gems + huevo Premium gratis
- Solo aparece 24h tras primer login
- Precio: 99 R$

#### Daily deals

- Cada día, una oferta limitada del 20-50% off
- Aparece en HUD con badge urgente
- Rota entre boosts, packs, cash

#### Eventos limitados

- Cada 3-4 semanas, evento con tienda exclusiva
- Pack premium del evento (1499 R$) con monstruo único garantizado
- "Solo durante este evento" → FOMO

### Robux Group (post-launch)

Crear un grupo de Roblox y hacer que el game pertenezca al grupo permite vender:
- **Group shirts** (cosméticos)
- **Game pass within group** (mejor cut?)
- **Premium membership rewards**

### Premium Payouts (Roblox Premium)

Los jugadores con **Roblox Premium** te dan ingresos pasivos según tiempo en tu game. Activar y trackear con dashboard.

### Estimaciones realistas de ingresos

Con 1000 jugadores activos:
- ~3-5% conversion → 30-50 pagadores
- ARPU (average revenue per user) ~5-15 R$/mes
- = 150-750 R$/mes
- A 0.0035 USD/R$ = **$0.50-2.60/mes con 1000 players**

Para llegar a $1000/mes necesitas:
- 5K-15K players activos diarios consistentes
- Eventos limitados regularmente
- Buenos hooks de retención

---

## 📢 Marketing y lanzamiento

### Pre-launch (sem 4)

#### Crear assets de marketing

- **Thumbnail principal** (1280×720) — esto es lo MÁS importante
- **Game icon** (512×512)
- **3-5 screenshots** in-game con texto overlay
- **Trailer 30-60s** mostrando highlights

#### Build hype

- Postear en Discord servers de Roblox dev
- Twitter/X: anuncio con countdown
- TikTok: 2-3 teasers en sem previa

### Launch day

#### Roblox internal

- Publicar el game como **Public**
- Activar **GameAnalytics** desde día 1
- Configurar **Premium Payouts**
- Verificar Game Settings final

#### Promoción inicial

- TikTok video del lanzamiento
- Discord servers de Roblox
- Sub-Reddits relevantes (/r/RobloxGameDev, /r/Roblox — con cuidado, sin spam)
- Tu propio círculo (amigos, familia con kids)

### Estrategia TikTok

Es **el canal #1** para games de Roblox actualmente.

#### Tipos de video que funcionan

1. **POV gameplay** — "POV: Eres nuevo en este game" (highlights primeros 30s)
2. **Drops épicos** — "Mira lo que me salió!" (Mythic+ con screen shake)
3. **Fusiones** — "Combiné 2 Doradas y mira qué pasó"
4. **Memes** — naming meme + clip rápido
5. **Tutoriales** — "Cómo hacer tu primer rebirth"

#### Hashtags

```
#roblox #robloxgames #robloxedits #robloxtok #brainrot #robloxgaming
#tycoon #pet #fyp #parati
```

Si está en español: `#robloxespañol #robloxbrainrot #criaunbicho`

#### Frecuencia

- **Mínimo:** 1 video/día durante primer mes
- **Ideal:** 2-3 videos/día con variedad de formatos
- Si uno explota → análisis y replicar fórmula

### YouTubers pequeños

YouTubers con 1K-50K subs suelen aceptar promocionar games gratis a cambio del video. Pitch:

```
Hola [name],
He desarrollado un game tycoon de monstruos en Roblox y he visto que cubres este tipo de games.
¿Te interesaría hacer un video sobre el lanzamiento? Te puedo dar acceso pre-launch + cosmetic exclusivo en tu nombre.
[link al game]
```

YouTubers grandes (100K+) suelen cobrar $50-500 por video sponsored.

### Comunidad

#### Discord server propio

Crear server con:
- Channel #anuncios
- Channel #updates
- Channel #bugs
- Channel #fan-art
- Channel #leaderboard
- VIP role para gamepass owners

#### Roblox group

- Group del game
- Anuncios en wall
- Group shirts (cosméticos vendibles)

### Updates regulares

**Esto es CLAVE.** Los games sin updates mueren rápido.

- **Sem 1-4 post-launch:** hotfixes diarios
- **Sem 5-8:** features menores cada semana (1 monster nuevo, 1 huevo)
- **Mes 2-3:** primer evento limitado (Halloween/Christmas/etc.)
- **Mes 3+:** updates grandes mensuales

### Crisis management

Cuando pasen cosas malas (servidor down, bug crítico, datos perdidos):

1. **Comunicar inmediatamente** en Discord/Twitter
2. **Compensar generosamente** (gemas gratis, huevo VIP gratis a todos)
3. **Postmortem público** (qué pasó y cómo se arregla)
4. **NO ocultar** — la comunidad lo nota


---

## 📊 KPIs y métricas

### Métricas críticas a trackear

#### Adquisición

- **CCU (Concurrent Users):** jugadores simultáneos. Métrica que Roblox muestra públicamente.
- **DAU (Daily Active Users):** jugadores únicos cada día.
- **MAU (Monthly Active Users):** jugadores únicos cada mes.
- **Visit count:** total de joins históricos.
- **Source breakdown:** cuántos llegan vía TikTok vs YouTube vs feed Roblox.

#### Engagement

- **Average session time:** tiempo promedio por sesión. **Target: >15 min**
- **Sessions per DAU:** cuántas veces vuelven al día. **Target: 1.5-2.5**
- **Session distribution:** % por debajo de 5 min vs 5-30 min vs +30 min
- **Bounce rate:** % que sale en menos de 60 segundos. **Target: <30%**

#### Retención (LO MÁS IMPORTANTE)

- **D1 retention:** % de jugadores que vuelven al día siguiente. **Target: 25-35%**
- **D7 retention:** % que vuelven en 1 semana. **Target: 10-15%**
- **D30 retention:** % que vuelven en 1 mes. **Target: 5-8%**

#### Monetización

- **ARPU (Average Revenue Per User):** ingresos / total players. **Target: 5-15 R$/mes**
- **ARPPU (Average Revenue Per PAYING User):** ingresos / pagadores. **Target: 100-500 R$/mes**
- **Conversion rate:** % de jugadores que pagan al menos 1x. **Target: 3-5%**
- **Gamepass-specific conversion:** cuál vende más
- **Whale ratio:** % de pagadores que generan 80% del revenue (regla 80/20)

#### Funnel del primer minuto

Crítico para optimizar onboarding:

- % que abre 1er huevo en <60s
- % que coloca 1er monstruo en <2 min
- % que recolecta 1er cash en <3 min
- % que completa tutorial entero
- % que llega a 5 minutos de sesión

Si caes mucho en algún paso → investigar y arreglar.

#### Salud del juego

- **Crash rate:** % de sesiones con crash. **Target: <2%**
- **DataStore failure rate:** % de saves fallidos. **Target: <0.1%**
- **Average ping:** latencia del server. **Target: <100ms**
- **Anti-cheat triggers:** cuántas veces se detecta exploit/día

### Setup de GameAnalytics

```lua
-- ServerScriptService/Services/AnalyticsService.lua
local GA = require(game.ReplicatedStorage.Modules.Lib.GameAnalytics)

GA:initialize({
    gameKey = "YOUR_GAME_KEY",
    secretKey = "YOUR_SECRET_KEY",
    enableInfoLog = false,
    enableVerboseLog = false,
    build = "1.0.0",
})

local AnalyticsService = {}

function AnalyticsService:Track(player, eventName, params)
    GA:addDesignEvent(player.UserId, {
        eventId = eventName,
        value = params and params.value or nil,
    })
end

function AnalyticsService:TrackPurchase(player, currency, amount, itemType, itemId)
    GA:addBusinessEvent(player.UserId, {
        amount = amount,
        itemType = itemType,
        itemId = itemId,
        cartType = currency,
    })
end

return AnalyticsService
```

### Eventos críticos a trackear

```lua
-- Onboarding funnel
AnalyticsService:Track(player, "tutorial_started")
AnalyticsService:Track(player, "first_egg_opened")
AnalyticsService:Track(player, "first_monster_placed")
AnalyticsService:Track(player, "first_collect")
AnalyticsService:Track(player, "tutorial_completed")

-- Progresión
AnalyticsService:Track(player, "egg_purchased", { eggId = "egg_premium", rarityObtained = "Mythic" })
AnalyticsService:Track(player, "fusion_completed", { fusionType = "to_golden" })
AnalyticsService:Track(player, "rebirth_completed", { rebirthNumber = 5 })

-- Monetización
AnalyticsService:Track(player, "shop_opened")
AnalyticsService:Track(player, "gamepass_viewed", { gamepassId = "vip" })
AnalyticsService:Track(player, "gamepass_purchased", { gamepassId = "vip" })
AnalyticsService:Track(player, "product_purchased", { productId = "gems_medium" })

-- Salud
AnalyticsService:Track(player, "crash_detected")
AnalyticsService:Track(player, "datastore_save_failed")
AnalyticsService:Track(player, "anti_exploit_triggered", { type = "speed_hack" })
```

### Dashboard semanal

Cada semana revisar:

| Métrica | Pregunta |
|---|---|
| CCU peak | ¿Subió o bajó vs sem anterior? |
| D1 retention | ¿Estamos en target? |
| ARPU | ¿Sube con tiempo? |
| Top features sin usar | ¿Qué hay roto o mal explicado? |
| Top conversion paths | ¿Por qué pagan los whales? |

### Cuando preocuparse

🚨 **Señales de alarma:**
- D1 retention < 20% → tutorial roto
- Bounce rate > 50% → primera impresión mal
- Conversion rate < 1% → tienda no funciona
- DataStore failure > 1% → bug crítico
- Crash rate > 5% → bug crítico

### Estimaciones realistas de ingresos

| Status | CCU | Revenue mensual aprox |
|---|---|---|
| Mediocre | 50-200 | $100-500 |
| Decente | 500-2000 | $1K-5K |
| Top | 5000-20000 | $10K-50K |
| Mega top | 50K+ | $100K+ |

**Objetivo realista a 3 meses:** 100-500 CCU sostenidos, $200-1000/mes.

---

## 🔄 Post-launch y escalado

### Mes 1: Establecimiento

**Objetivos:**
- Resolver bugs críticos
- Estabilizar retención
- Aprender de los datos

**Actividades:**
- **Hotfixes diarios** primera semana
- **Updates pequeños semanales** (1 monstruo nuevo, 1 huevo, 1 evento de fin de semana)
- **Análisis de funnel** — dónde caen los jugadores y arreglarlo
- **Comunidad activa** — responder en Discord, recoger feedback

### Mes 2: Crecimiento

**Objetivos:**
- Aumentar D7/D30 retention
- Lanzar primer evento limitado
- Optimizar monetización

**Actividades:**
- **Primer evento limitado** (Halloween, Christmas, etc.)
- **Tier 2 de gamepasses** (Triple Hatch, Auto Hatch, Speed Coil, etc.)
- **Pet Index** completo y achievements
- **Sistema de robo v2 (opcional)** — si la base está sana

### Mes 3: Escalado

**Objetivos:**
- Llegar a 1K+ CCU
- Establecer eventos como rutina
- Lanzar features ambiciosas

**Actividades:**
- **Sistema de trading** entre jugadores (con fee)
- **Plot skins premium** (cosméticos)
- **Tier 3 de gamepasses** (Ultra VIP combo)
- **Battle pass mensual** (post-experimento)
- **Colaboración con YouTuber** mediano

### Mes 4-6: Expansión

**Objetivos:**
- Diversificar contenido
- Establecer marca

**Posibles features:**
- **Mascotas siguiendo al jugador** (cosmético + bonus)
- **Mini-games** en el Hub (rascas para gemas, etc.)
- **Sistema de clanes** (guilds)
- **PvP arena** opcional (no obligatorio)
- **Achievements** completos con recompensas

### Año 1+: Madurez

- **Sequel/expansion** ("Cría un Bicho Roto 2")
- **Crossover events** con otros games
- **Merchandise** (Roblox Avatar items)
- **Esports/competiciones** (si la comunidad lo pide)

### Cuándo cerrar el game

A veces los games dejan de crecer y mantenerlos cuesta. Señales:

- CCU sostenido < 50 durante 3 meses
- Updates ya no mueven aguja
- Comunidad muerta
- Robux ingresos no cubren ni la moderación

En ese caso: **anuncio honesto**, regalo final a la comunidad, pivote a próximo game.

### Roadmap de versiones

```
v1.0 (Launch) — Loop core + monetización básica
v1.1 (Sem 2)  — 5 monstruos nuevos + balance fixes
v1.2 (Sem 4)  — Gamepasses Tier 2
v1.3 (Mes 2)  — Primer evento limitado
v2.0 (Mes 3)  — Sistema de robo + trading
v2.1 (Mes 4)  — Plot skins + cosméticos
v3.0 (Mes 6)  — Battle pass + achievements
```

### Lecciones de games exitosos

#### Pet Simulator 99 / X
- Updates AGRESIVOS (cada 2 semanas)
- Eventos masivos con currencies exclusivas
- Trading muy importante
- Whales bien atendidos (gamepasses premium)

#### Steal a Brainrot
- Hook viral fuertísimo (TikTok)
- Naming memético
- Mecánica de robo crea drama → clips
- F2P jugable real

#### Adopt Me
- Comunidad familiar/casual
- Trading el corazón del game
- Eventos limitados con FOMO real
- Cero toxicidad permitida

**Lecciones aplicables:**
1. Updates regulares son CRÍTICOS
2. Eventos limitados con FOMO funcionan
3. Trading añade vida tras lanzamiento
4. La comunidad es tu activo más valioso

---

## 📚 Recursos y referencias

### Documentación oficial Roblox

- **Roblox Creator Hub:** https://create.roblox.com/docs
- **Luau Documentation:** https://luau-lang.org/
- **API Reference:** https://create.roblox.com/docs/reference/engine
- **DevForum:** https://devforum.roblox.com/

### Tutoriales/canales recomendados

- **AlvinBlox** (YouTube) — beginner-friendly, clásico
- **TheDevKing** — intermediate, simulator-focused
- **EgoMoose** — advanced, math-heavy
- **B Ricey** — game design + business
- **GnomeCode** — variado, calidad

### Plugins esenciales para Studio

- **Rojo** — sync filesystem ↔ Studio (CRÍTICO para usar Git)
- **Tag Editor** — gestión de CollectionService tags
- **Sound Library Plus** — buscar audio fácilmente
- **Building Plugin Pack** — herramientas de modelado mejoradas
- **Plugin Manager** — gestión de plugins
- **Studio+** — UI improvements

### Herramientas externas

- **VS Code** + extensión Luau LSP
- **Rojo CLI** instalado globalmente
- **Git** + GitHub repo privado (mientras desarrollas)
- **GameAnalytics** account
- **Trello/Notion** para gestión de tareas

### Asset stores

- **Roblox Toolbox** — primero, lo más rápido
- **Roblox Creator Store** — paid assets de calidad
- **Sketchfab** — modelos 3D externos (algunos free)
- **Mixkit** — SFX gratis
- **Freesound** — banco enorme de SFX bajo CC
- **AudioJungle** — música paid
- **Pixabay** — imágenes y video gratis

### Comunidades

- **Discord Roblox Studio** (oficial)
- **DevForum** (oficial)
- **r/RobloxGameDev** (subreddit)
- **Roblox Studio en Español** (Discord/Twitter)

### Inspiración (jugar y analizar)

- **Pet Simulator 99 / X** (BIG Games) — referencia genre
- **Adopt Me** (DreamCraft) — comunidad/trading
- **Pls Donate** — UX simple pero efectiva
- **Steal a Brainrot** — viral hooks
- **Bee Swarm Simulator** — depth + retención
- **Build a Boat for Treasure** — simplicidad efectiva
- **Tower Defense Simulator** — UI/UX impecable

### Métricas de la industria

- **Average D1 retention en Roblox:** 25-35%
- **Average ARPU en simulators:** 5-15 R$/mes
- **Conversion rate típica:** 2-5%
- **% pagadores que son whales:** ~5%
- **Revenue split de Roblox:** 30% creador, 70% Roblox (después de fees)
- **Premium Payouts:** ~$0.0035/Robux

### Books / Reading

- **The Art of Game Design** (Jesse Schell) — game design clásico
- **Theory of Fun for Game Design** (Raph Koster)
- **Blogs de retention/monetization** en Mobile gaming (aplicable a Roblox)

### Contactos profesionales (cuando crezcas)

- **GFX freelancers** en Fiverr para thumbnails
- **3D modelers** en Fiverr o ArtStation
- **Music producers** en SoundBetter
- **Roblox UGC creators** para colaboraciones
- **Developer Relations Roblox** (cuando seas top game)

### Templates útiles

- **Plot tycoon kit** — base para empezar
- **Simulator UI kit** (varios autores)
- **Pet handling system** (algunos open source en GitHub)
- **DataStore wrapper** (ProfileService, MockDataStore)

### Librerías recomendadas

```lua
-- Comunicación cliente-servidor mejorada
require("Knit")          -- Framework completo
require("Net")           -- Networking simplificado

-- Datos
require("ProfileService") -- Wrapper de DataStore robusto
require("Replion")        -- State sync cliente-servidor

-- UI
require("Fusion")         -- Reactive UI (avanzado)
require("Roact")          -- React-like (avanzado)

-- Utils
require("Promise")        -- Promises para async
require("Signal")         -- Eventos custom
require("Maid")           -- Cleanup automático
```

---

## 🎯 Resumen ejecutivo

### Qué tienes documentado

- ✅ Visión, mecánicas, sistemas, balance económico
- ✅ Estética completa, naming, paleta, tipografía
- ✅ Estructura técnica completa de Studio
- ✅ Configuración de cada service nativo
- ✅ Estructura interna de modelos, UIs, Hub, plot
- ✅ Schema de datos completo con migrations
- ✅ ModuleScripts de configuración listos para pegar
- ✅ API de cada Service del servidor
- ✅ API de cada Controller del cliente
- ✅ Flujos end-to-end de las 6 acciones críticas
- ✅ Anti-cheat y validaciones
- ✅ Lista exhaustiva de assets necesarios
- ✅ Roadmap de 4 semanas
- ✅ Plan de monetización completo (Tier 1-3)
- ✅ Estrategia de marketing y lanzamiento
- ✅ KPIs y métricas
- ✅ Roadmap post-launch a 1 año
- ✅ Recursos y referencias

### Lo más importante (TL;DR)

1. **Empieza con el loop core funcionando** (sem 1-2). Sin él, nada importa.
2. **Save/Load robusto desde día 1.** Una sola pérdida de datos masiva mata el juego.
3. **Tutorial primeros 30 segundos.** D1 retention depende de esto.
4. **Anuncios globales de míticos.** Crea hype social.
5. **Updates regulares post-launch.** Sin updates, retention se desploma.
6. **TikTok marketing es CLAVE.** 1-2 videos/día durante primer mes.
7. **F2P jugable real.** Pero monetización generosa para whales.

### Decisiones críticas no recortables

- ✅ Loop core funcionando (sem 2)
- ✅ Save/Load funcional con migrations
- ✅ Tutorial primeros 30s
- ✅ Anuncio global de míticos
- ✅ Thumbnail decente (mínimo $25 freelancer)

### Decisiones que NO incluir en lanzamiento

- ❌ Sistema de robo / PvP (déjalo para v2)
- ❌ Trading entre jugadores (v3)
- ❌ Eventos limitados (post-launch)
- ❌ Más de 60-80 monstruos
- ❌ Sistema de pets siguiendo al jugador

### Próximos pasos inmediatos

1. **Setup técnico:** Studio + Rojo + Git (1h)
2. **Importar Tycoon Kit + pet packs** (1h)
3. **Crear ModuleScripts de configuración** copy-paste de este README (2h)
4. **Implementar DataService básico** y testear save/load (3h)

Ya con eso tienes la base sobre la que construir todo lo demás.

---

**Cría un Bicho Roto** © 2025 Northycal · Desarrollado por Ruxyen
