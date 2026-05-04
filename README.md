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
7. [Schema de datos](#-schema-de-datos)
8. [Assets necesarios](#-assets-necesarios)
9. [Roadmap de desarrollo](#-roadmap-de-desarrollo-4-semanas)
10. [Plan de monetización](#-plan-de-monetización)
11. [KPIs y métricas](#-kpis-y-métricas)
12. [Recursos y referencias](#-recursos-y-referencias)

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

---

## 🎲 Concepto y mecánicas core

### Loop principal del jugador

```
1. COMPRAR huevo en máquina del Hub
       ↓
2. ABRIR huevo (animación cinematográfica)
       ↓
3. OBTENER monstruo random según rareza
       ↓
4. COLOCAR monstruo en slot vacío del plot
       ↓
5. GENERAR dinero pasivo automáticamente
       ↓
6. RECOLECTAR de la hucha central
       ↓
7. REINVERTIR en mejores huevos / upgrades
       ↓
        (vuelve a 1)
```

### Sistemas de profundidad

- **Fusión:** Combina monstruos para obtener variantes más poderosas (Normal → Dorado → Arcoíris → Void)
- **Rebirth:** Reset progresivo que da bonus permanente (+25% generación por rebirth)
- **Upgrades:** Mejoras del plot (más slots, multipliers, suerte, capacidad)
- **Eventos limitados:** Contenido temporal con monstruos exclusivos

---

## 🎮 Sistemas del juego

### 1. Sistema de huevos (Gacha)

Los jugadores compran huevos en máquinas del Hub. Cada huevo tiene una tabla de drop con probabilidades por rareza. Drops de Mythic+ activan anuncio global en chat.

**Tipos de huevos:**

| Huevo | Precio | Moneda | Rareza dominante |
|---|---|---|---|
| Básico | $50 | Cash | Common 70% |
| Avanzado | $500 | Cash | Rare 50% |
| Premium | $5.000 | Cash | Epic 50% |
| Cósmico | $50.000 | Cash | Legendary 45% |
| Divino | $500.000 | Cash | Legendary/Mythic |
| VIP | 199 R$ | Robux | Legendary 50% |

### 2. Sistema de plot personal

Cada jugador tiene un plot privado asignado al entrar:

- **Slots de monstruos:** empieza con 4, máximo 50+ con upgrades
- **Hucha central:** acumula generación pasiva, click para recolectar
- **Cofre de inventario:** monstruos no colocados
- **Estación de fusión:** combina monstruos para mejores variantes
- **Decoraciones cosméticas:** personalización del plot

### 3. Generación pasiva

Cada monstruo genera $X cada 3 segundos según rareza:

| Rareza | Generación | % drop |
|---|---|---|
| Common | $1 / 3s | 60% |
| Rare | $5 / 3s | 25% |
| Epic | $25 / 3s | 10% |
| Legendary | $150 / 3s | 4% |
| Mythic | $1.000 / 3s | 0,9% |
| Secret | $10.000 / 3s | 0,09% |
| Divine | $100.000 / 3s | 0,01% |

**Generación offline:** hasta 2h por defecto (24h con gamepass).

### 4. Sistema de fusión (HOOK ÚNICO)

| Fusión | Input | Output | Coste |
|---|---|---|---|
| Subir tier | 3× misma rareza | 1× rareza superior | Gratis |
| Forjar Dorado | 2× mismo monstruo (Normal) | Dorado (×2 gen) | $10.000 |
| Forjar Arcoíris | 2× Dorado mismo | Arcoíris (×5 gen) | $250.000 |
| Forjar Void | 2× Arcoíris mismo | Void (×15 gen) | $5.000.000 |

### 5. Sistema de Rebirth

Reset progresivo que se desbloquea al acumular cierto dinero total:

| Rebirth | Coste | Bonus permanente |
|---|---|---|
| 1 | $10M | +25% gen global |
| 2 | $50M | +50% gen global |
| 3 | $250M | +100% gen global + huevo exclusivo |
| 5 | $5B | +250% gen + monstruos exclusivos R5 |
| 10 | $1T | +1000% gen + título dorado |
| 25 | $100T | Plot exclusivo + skin jugador |
| 50 | $10Q | Monstruo Mítico Eterno garantizado |

### 6. Sistema de upgrades

Mejoras del plot con coste creciente:

- **Multiplicador global** — +10%/nivel, máx 10
- **Suerte de huevos** — +5%/nivel, máx 5
- **Capacidad hucha** — +50%/nivel, máx 8
- **Velocidad recolección** — +20%/nivel, máx 5
- **Tiempo offline** — +30 min/nivel, máx 5

### 7. Sistemas de retención

- **Daily login bonus:** ciclo de 7 días con recompensas crecientes
- **Eventos limitados:** cada 3-4 semanas, 7-14 días de duración
- **Anuncios globales:** drops Mythic+ se anuncian a todos los jugadores
- **Pet Index:** colección visible con silhouettes de monstruos no descubiertos

### 8. Sistema de robo (v2 — post-launch)

⚠️ **NO incluir en lanzamiento inicial.** Implementar tras validar retención base.

- Opción inicial: solo robar cash de la hucha si no se recoge
- Opción avanzada: robar monstruos colocados con cooldown y defensas

---

## 💰 Balance económico

### Curva de progresión F2P estimada

| Hito | Tiempo aprox. |
|---|---|
| Llenar 8 slots iniciales | Hora 1 |
| Primer Épico | Hora 1-2 |
| Primer Legendario | Hora 5 |
| Primer Mítico | Hora 15 |
| Primer Rebirth | Hora 30 |
| Rebirth 5 | Hora 100 |
| Endgame (Voids, R25+) | Hora 500+ |

### Slots desbloqueables

| Slots | Coste |
|---|---|
| 5º | $1.000 |
| 6º | $5.000 |
| 8º | $50.000 |
| 12º | $500.000 |
| 20º | $5M |
| 30º | $50M |
| 50º (máx pre-rebirth) | $500M |

---

## 🎨 Estética y naming

### Concepto visual

Mundo flotante de **plataformas en el cielo**, cada plot es una isla privada. Hub central con estética de **bazar/feria nocturna** con neones. Mezcla entre Adopt Me (cuteness), Pet Simulator 99 (juice exagerado), y Steal a Brainrot (humor absurdo).

### Paleta de colores

```
Fondo principal:    #1A0B2E (morado profundo)
Acento neón rosa:   #FF2E88
Acento neón cian:   #00F0FF
Premium dorado:     #FFB800
Arcoíris:           animado dinámico
```

### Tipografía

- **Títulos UI:** Bebas Neue / Anton
- **Body:** Inter / Poppins
- **Números/contadores:** Orbitron / JetBrains Mono
- **Logo:** Display chunky tipo Fredoka One

### Naming de monstruos por rareza

**Common (chusta):** Goblin Roto, Sapo Triste, Pollo Glitch, Rata WiFi, Gusano Cansado, Hongo Pixel

**Rare:** Goblin Dorado, Pingüino Hacker, Tortuga Turbo, Murciélago Emo, Zorro Glitch, Axolotl Punk

**Epic:** Dragón Bebé, Robot Roto, Calamar Cósmico, Conejo Demonio, Gato Void

**Legendary:** Skibidi Dragón, Hidra Glitch, Fénix Pixelado, Lobo Espacial

**Mythic:** Capybara Galáctico, Kraken de Bolsillo, Tigre Holográfico, Pulpo Reactor

**Secret:** Cthulhu Confundido, El Bicho™, Void Worm

**Divine:** ÁNIMA, GLITCH PRIME, EL ELEGIDO, NULL *(en mayúsculas para épicos)*

### Animaciones clave (juice)

- Apertura de huevo cinematográfica con cámara y slow-mo
- Drops Epic+ con beam de luz vertical
- Generación de cash con texto flotante "+$X" con bounce
- Recolección con efecto magnetic
- Fusión con explosión de luz
- Rebirth con animación cósmica + fanfarria
- Anuncio global mítico con banner deslizante

---

## 🏗 Estructura técnica

### Stack tecnológico

- **Lenguaje:** Lua (Luau específicamente)
- **Engine:** Roblox Studio
- **Versionado:** Git + Rojo (sync Studio ↔ filesystem)
- **Backend:** Roblox DataStore (persistencia)
- **Analytics:** GameAnalytics + dashboard nativo Roblox

### Configuración inicial obligatoria

- ✅ API Services activado
- ✅ HTTP Service activado
- ✅ Team Create activado
- ✅ Dispositivos: PC, Móvil, Tablet, Consola
- ❌ VR desactivado (no compatible con simulator)
- ❌ Intercambio de datos con IA Roblox (recomendado off para proteger mecánica de fusión)

### Estructura de carpetas en Studio

```
game/
│
├── Workspace/                          # 🌍 Mundo físico/visible (service nativo)
│   ├── Hub/                            # Zona común con todos los jugadores
│   │   ├── BaseStructure               # Plataforma central, suelo, paredes
│   │   ├── EggMachines/                # 5-6 máquinas de huevo
│   │   │   ├── EggMachine_Basic
│   │   │   ├── EggMachine_Advanced
│   │   │   ├── EggMachine_Premium
│   │   │   ├── EggMachine_Cosmic
│   │   │   ├── EggMachine_Divine
│   │   │   └── EggMachine_VIP
│   │   ├── ShopNPC                     # NPC vendedor de gamepasses
│   │   ├── DailyRewardKiosk            # Kiosko para reclamar daily
│   │   ├── LeaderboardDisplay          # Top jugadores visible
│   │   ├── Decorations/                # Estatuas, fuentes, neones, props
│   │   ├── Lighting/                   # Luces puntuales del Hub (PointLight, SpotLight)
│   │   └── Teleporters/                # Hacia VIP Lounge, eventos, etc.
│   │
│   ├── Plots/                          # Plots privados (se generan dinámicamente)
│   │   ├── Plot_1                      # Asignado a player 1
│   │   ├── Plot_2
│   │   └── ... (uno por jugador del server)
│   │
│   ├── EventArea/                      # Zona temporal de eventos limitados
│   │   └── (vacío hasta que haya evento activo)
│   │
│   ├── SpawnLocation                   # Punto de aparición inicial
│   ├── Baseplate                       # Suelo base (eliminar si tienes mapa custom)
│   ├── Terrain                         # Terreno editable (si lo usas)
│   └── Camera                          # Cámara del juego (no tocar)
│
├── Lighting/                           # 💡 Iluminación global (service nativo)
│   ├── Atmosphere                      # Niebla, densidad, color del aire
│   ├── Sky                             # Skybox personalizado (cielo nocturno con estrellas)
│   ├── Bloom                           # Efecto bloom para neones brillantes
│   ├── ColorCorrection                 # Saturación, contraste, tinte general
│   ├── DepthOfField                    # Desenfoque de profundidad (opcional)
│   ├── SunRays                         # Rayos del sol (suaves, no abusar)
│   └── BlurEffect                      # Para fade-out cuando se abre menú
│   # Configuración recomendada:
│   # - Brightness: 1.5
│   # - ClockTime: 19 (atardecer permanente)
│   # - GlobalShadows: true
│   # - Technology: Future (mejor calidad)
│
├── ReplicatedFirst/                    # ⚡ Carga ANTES que el resto (service nativo)
│   ├── LoadingScreen                   # Pantalla de carga inicial
│   │   ├── ScreenGui
│   │   └── LoadingScript               # LocalScript que la oculta cuando todo está listo
│   └── EssentialAssets/                # Assets críticos que deben estar al instante
│
├── ReplicatedStorage/                  # 📦 Compartido cliente↔servidor
│   ├── Modules/                        # Configuración compartida (read-only)
│   │   ├── MonsterData
│   │   ├── EggData
│   │   ├── RarityConfig
│   │   ├── FusionRecipes
│   │   ├── UpgradeData
│   │   ├── RebirthData
│   │   ├── GamepassData
│   │   ├── ProductData
│   │   ├── DailyRewards
│   │   └── Utils/
│   │       ├── Format                  # Formatear $1.5K, $2.3M, $4.7B
│   │       ├── Random                  # Weighted random selection
│   │       └── Signal                  # Sistema de eventos custom
│   │
│   ├── RemoteEvents/                   # Comunicación cliente↔servidor (async)
│   │   ├── BuyEgg
│   │   ├── PlaceMonster
│   │   ├── RemoveMonster
│   │   ├── CollectCash
│   │   ├── FuseMonsters
│   │   ├── BuyUpgrade
│   │   ├── DoRebirth
│   │   ├── SellMonster
│   │   ├── ClaimDailyReward
│   │   └── DataUpdated                 # Server → Client cuando cambian datos
│   │
│   ├── RemoteFunctions/                # Queries síncronas
│   │   ├── GetPlayerData
│   │   ├── GetFusionPreview
│   │   └── GetServerStats
│   │
│   └── Assets/
│       ├── Monsters/                   # Modelos de cada monstruo
│       │   ├── goblin_roto
│       │   ├── sapo_triste
│       │   └── ... (50-80 modelos)
│       ├── Eggs/                       # Modelos de huevos
│       │   ├── egg_basic
│       │   ├── egg_advanced
│       │   └── ...
│       ├── VFX/                        # Particle systems prefabricados
│       │   ├── DropCommon
│       │   ├── DropRare
│       │   ├── DropEpic
│       │   ├── DropLegendary
│       │   ├── DropMythic              # Beam vertical + screen shake
│       │   ├── DropSecret
│       │   ├── DropDivine
│       │   ├── FusionExplosion
│       │   ├── RebirthAscension
│       │   ├── CashCollect             # Magnetic effect
│       │   └── Sparkles_Variants/      # Trails para Golden/Rainbow/Void
│       ├── Animations/                 # Animaciones de monstruos y UI
│       │   ├── MonsterIdle
│       │   ├── MonsterEat
│       │   ├── EggHatch
│       │   ├── EggIdle                 # Tiembla en máquina
│       │   └── UIBounce
│       └── UI/                         # Iconos, imágenes, decals
│           ├── RarityIcons/
│           ├── MonsterThumbnails/      # Para inventory display
│           └── CurrencyIcons/
│
├── ServerStorage/                      # 🔒 Privado del servidor
│   ├── PlotTemplate                    # El plot vacío que se clona por jugador
│   ├── HubModels/                      # Modelos del hub que el cliente no necesita por adelantado
│   ├── EventTemplates/                 # Templates de eventos limitados
│   │   ├── HalloweenEvent
│   │   ├── ChristmasEvent
│   │   └── ...
│   └── AdminTools/                     # Solo para devs (comandos, debug)
│
├── ServerScriptService/                # ⚙️ Lógica del servidor
│   ├── Main                            # Entry point del servidor
│   ├── Services/
│   │   ├── DataService                 # CRÍTICO: load/save/migration/cache
│   │   ├── PlotService                 # Asignación y gestión de plots
│   │   ├── MonsterService              # Spawn/remove monstruos en plots
│   │   ├── EggService                  # Lógica de compra y drop
│   │   ├── EconomyService              # Generación pasiva, recolección
│   │   ├── FusionService               # Lógica de fusiones
│   │   ├── UpgradeService              # Compra y aplicación de upgrades
│   │   ├── RebirthService              # Lógica de rebirth
│   │   ├── GamepassService             # Detectar y aplicar gamepasses
│   │   ├── ProductService              # Developer products consumibles
│   │   ├── DailyRewardService          # Login diario
│   │   ├── NotificationService         # Anuncios globales (drops míticos)
│   │   ├── BoostService                # Boosts temporales activos
│   │   ├── EventService                # Gestión de eventos limitados
│   │   ├── LeaderboardService          # Top jugadores global
│   │   ├── AnalyticsService            # Tracking de eventos a GameAnalytics
│   │   └── AntiExploitService          # Validaciones anti-cheat
│   └── Handlers/                       # Conectan RemoteEvents a Services
│       ├── BuyEggHandler
│       ├── PlaceMonsterHandler
│       ├── CollectCashHandler
│       ├── FuseMonstersHandler
│       └── ...
│
├── StarterPlayer/                      # 👤 Configuración del jugador
│   ├── StarterPlayerScripts/           # LocalScripts que se clonan al jugador
│   │   ├── Main                        # Entry point del cliente
│   │   ├── Controllers/
│   │   │   ├── UIController            # Gestiona qué UI está abierta
│   │   │   ├── PlotController          # Interacción con slots, hucha
│   │   │   ├── EggController           # Animación de apertura de huevo
│   │   │   ├── InventoryController
│   │   │   ├── FusionController
│   │   │   ├── ShopController
│   │   │   ├── NotificationController
│   │   │   ├── TutorialController
│   │   │   ├── CameraController        # Efectos de cámara (shake, zoom)
│   │   │   ├── SoundController         # Reproduce SFX según contexto
│   │   │   └── InputController         # Inputs de PC, móvil, gamepad
│   │   └── State/
│   │       └── PlayerState             # Cache local de datos del player
│   │
│   ├── StarterCharacterScripts/        # Scripts que se clonan al CHARACTER (cuerpo)
│   │   └── (vacío por defecto, scripts custom de movimiento aquí)
│   │
│   └── StarterCharacter (opcional)     # Modelo custom del personaje (si reemplazas avatar)
│   # Configuración recomendada:
│   # - CharacterWalkSpeed: 16 (default)
│   # - CharacterJumpHeight: 7.2
│   # - EnableMouseLockOption: false
│   # - LoadCharacterAppearance: true
│
├── StarterPack/                        # 🎒 Tools que se dan al jugador (vacío en simulator)
│   └── (vacío — no usamos tools)
│
├── StarterGui/                         # 🖥 UIs que se clonan al jugador al entrar
│   ├── HUD                             # Cash, gemas, rebirth count siempre visible
│   ├── InventoryGui
│   ├── ShopGui                         # Tienda de gamepasses + dev products
│   ├── FusionGui
│   ├── UpgradeGui
│   ├── RebirthGui
│   ├── DailyRewardGui
│   ├── NotificationGui                 # Banners de drops míticos
│   ├── TutorialGui                     # Tutorial primeros 30s
│   ├── PetIndexGui                     # Colección de monstruos descubiertos
│   ├── SettingsGui                     # Música, SFX, idioma
│   ├── EventGui                        # UI de evento limitado activo
│   └── ChatGui (default Roblox)        # No tocar, viene por defecto
│
├── SoundService/                       # 🔊 Sonidos globales (service nativo)
│   ├── Music/                          # Música de fondo
│   │   ├── HubMusic                    # Loop ambient del Hub
│   │   ├── PlotMusic                   # Música más relajada en plot
│   │   └── EventMusic                  # Música de evento activo
│   ├── SFX/                            # Efectos de sonido
│   │   ├── EggCrack
│   │   ├── EggHatch
│   │   ├── DropCommon
│   │   ├── DropMythic                  # Sonido épico
│   │   ├── CashCollect
│   │   ├── CashEarn                    # Pequeño "ding" cada generación
│   │   ├── Fusion
│   │   ├── Rebirth                     # Fanfarria
│   │   ├── ButtonClick
│   │   ├── ButtonHover
│   │   ├── PurchaseSuccess
│   │   ├── ErrorSound
│   │   └── NotificationDing
│   └── (configurar RespectFilteringEnabled, AmbientReverb si quieres)
│
├── MaterialService/                    # 🎨 Materiales custom (service nativo, opcional)
│   └── (solo si usas materiales personalizados)
│
├── TextChatService/                    # 💬 Chat moderno (service nativo)
│   ├── ChatWindowConfiguration
│   ├── ChannelTabsConfiguration
│   └── TextChannels/
│       └── RBXGeneral                  # Canal global por defecto
│   # Personalización: tags VIP, colores por rebirth tier, comandos /trade, etc.
│
└── Teams/                              # 👥 Sistema de equipos (no usar en simulator)
    └── (vacío)
```

### Decisiones arquitectónicas clave

- **Service-oriented:** cada sistema es un módulo independiente, testeable aisladamente
- **Handlers separados de Services:** los Handlers reciben RemoteEvents y validan, los Services hacen lógica
- **PlayerState centralizado en cliente:** evita fetches duplicados, todos los Controllers leen del cache
- **Pcall siempre en DataStore + UpdateAsync sobre SetAsync** para evitar race conditions
- **Save cada 60s + BindToClose**, nunca por acción individual

### Configuración de services nativos

Los services siguientes vienen por defecto en cada experiencia de Roblox. No se crean — se **configuran** desde el panel Properties cuando los seleccionas en el Explorer.

#### 🌍 Workspace — el mundo del juego

Aquí va todo lo físico y visible. Configuración recomendada en Properties:

- **Gravity:** 196.2 (default — no cambiar a menos que quieras física rara)
- **FilteringEnabled:** true (siempre, es lo que protege de exploits)
- **StreamingEnabled:** true (CRÍTICO en simulators con muchos plots) — descarga partes del mapa que el jugador no ve, mejora rendimiento brutal en móvil
  - StreamingTargetRadius: 1024
  - StreamingMinRadius: 256
- **InsertPoint:** dejar por defecto

**Estructura interna que tú creas dentro de Workspace:**

- `Hub/` → la zona común (tu primer trabajo de mapa)
- `Plots/` → los plots se generan dinámicamente al entrar jugadores (lo hace `PlotService`)
- `EventArea/` → vacío hasta que haya evento activo
- `SpawnLocation` → punto de aparición inicial (la baseplate trae uno por defecto)

#### 💡 Lighting — iluminación global

La estética visual del juego sale 50% de aquí. Configuración para tu paleta nocturna con neones:

| Propiedad | Valor recomendado | Por qué |
|---|---|---|
| Brightness | 1.5-2 | Iluminación general |
| ClockTime | 19 (atardecer) o 0 (noche) | Para tu tema nocturno |
| GeographicLatitude | 41.5 | Posición del sol |
| GlobalShadows | true | Sombras realistas |
| Technology | Future | Mejor calidad de iluminación |
| Ambient | (40, 40, 60) RGB | Tinte morado en sombras |
| OutdoorAmbient | (50, 50, 70) RGB | Tinte cielo nocturno |
| EnvironmentDiffuseScale | 0.5 | Reflejos del entorno |
| EnvironmentSpecularScale | 0.5 | Brillos del entorno |

**Hijos que añadir dentro de Lighting:**

- **Atmosphere** → niebla y densidad del aire
  - Density: 0.3
  - Color: morado (~#1A0B2E)
  - Decay: morado claro
  - Glare: 0
  - Haze: 1
- **Sky** → skybox personalizado (busca "night sky" o "stars" en Toolbox)
- **Bloom** → efecto bloom para hacer que neones brillen
  - Intensity: 0.4
  - Size: 24
  - Threshold: 1.5
- **ColorCorrection** → tinte global
  - Saturation: 0.2
  - Contrast: 0.1
  - TintColor: (255, 230, 255) ligero tinte rosa
- **DepthOfField** (opcional) → desenfoque cinematográfico
- **SunRays** → solo si quieres sol visible
- **BlurEffect** (Enabled: false por defecto) → para activar cuando se abre menú

#### 🔊 SoundService — sonidos globales

Configuración:

- **AmbientReverb:** Hangar (da sensación de espacio amplio) o NoReverb si no quieres efecto
- **DistanceFactor:** 3.33
- **DopplerScale:** 1
- **RolloffScale:** 1
- **RespectFilteringEnabled:** true (importante para anti-cheat de sonidos)

**Estructura de hijos:**

- **Music/** (carpeta Folder)
  - HubMusic → Sound con SoundId, Looped: true, Volume: 0.3
  - PlotMusic → ídem, Volume: 0.25
  - EventMusic → ídem
- **SFX/** (carpeta Folder) → todos los efectos de sonido como objetos Sound

Los sonidos se reproducen desde el cliente (LocalScript) usando `SoundService.SFX.EggHatch:Play()`.

#### ⚡ ReplicatedFirst — pantalla de carga

Lo que metas aquí se descarga ANTES que el resto. Úsalo para:

- LoadingScreen (ScreenGui con tu logo, animación, barra de progreso)
- LocalScript que oculta el default loading de Roblox y muestra el tuyo
- Después espera con `game:IsLoaded()` y oculta el LoadingScreen cuando todo esté listo

```lua
-- ReplicatedFirst/LoadingScript (LocalScript)
local ReplicatedFirst = game:GetService("ReplicatedFirst")
ReplicatedFirst:RemoveDefaultLoadingScreen()

-- Mostrar tu loading custom
local loadingScreen = script.Parent.LoadingScreen
loadingScreen.Parent = game.Players.LocalPlayer.PlayerGui

-- Esperar a que todo cargue
if not game:IsLoaded() then game.Loaded:Wait() end

-- Animar fade-out y eliminar
loadingScreen:Destroy()
```

#### 💬 TextChatService — sistema de chat

El chat moderno de Roblox. Configuración recomendada:

- **CreateDefaultCommands:** true (comandos /me, /w, etc.)
- **CreateDefaultTextChannels:** true
- **ChatVersion:** TextChatService (no LegacyChatService, es viejo)

**Personalizaciones útiles:**

- Tags por rebirth tier (server-side script que añade prefix `[R5]` al nombre)
- Color de nombre VIP (dorado para gamepass VIP)
- Anuncios globales de drops míticos van por aquí (system message en RBXSystem channel)
- Comandos custom: `/trade @player`, `/inv`, `/stats`

#### 🎨 MaterialService — materiales custom

Solo si vas a usar materiales personalizados. Para empezar puedes ignorarlo y usar los materiales por defecto de Roblox (Plastic, Neon, Metal, etc.). Los Neon son perfectos para tu estética con neones.

#### 👥 Teams, StarterPack — no usar

- **Teams:** vacío. Los simulators no usan equipos.
- **StarterPack:** vacío. No damos tools/herramientas al jugador (no hay armas ni inventario tipo Minecraft).

### Orden de creación recomendado en Studio

Cuando empieces a construir, sigue este orden para no perderte:

1. **Lighting** → configurar para que el ambiente visual esté antes de modelar
2. **Workspace/Hub** → construir el Hub básico (plataforma, decoraciones, máquinas)
3. **ServerStorage/PlotTemplate** → diseñar el plot vacío (slots, hucha, altar de fusión)
4. **ReplicatedStorage/Modules** → todos los ModuleScripts de configuración
5. **ReplicatedStorage/Assets** → importar y organizar los modelos
6. **SoundService** → meter música y SFX
7. **ServerScriptService/Services** → empezar con DataService, luego PlotService
8. **StarterGui** → las UIs después de que la lógica funcione
9. **StarterPlayerScripts/Controllers** → los controllers conectan UIs con lógica
10. **ReplicatedFirst** → loading screen al final, cuando todo lo demás está

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

⚠️ **Los IDs en snake_case sin acentos.** Una vez publicado, NO se pueden cambiar sin migración.

### Schema de save por jugador (DataStore)

```lua
{
    version = 1,                    -- Para migrations futuras

    currencies = {
        cash = 0,
        gems = 0,
    },

    inventory = {                   -- Array de instancias UNICAS con UUID
        -- { uuid, monsterId, variant, obtainedAt, locked }
    },

    placedMonsters = {              -- Map slotIndex → uuid
        -- [1] = "abc123-..."
    },

    plot = {
        slotsUnlocked = 4,
        vaultBalance = 0,
        plotSkin = "default",
    },

    upgrades = {
        global_multiplier = 0,
        luck = 0,
        vault_capacity = 0,
        collect_speed = 0,
        offline_time = 0,
    },

    rebirth = {
        count = 0,
        totalEarnings = 0,
    },

    gamepasses = {                  -- Cache local
        -- ["auto_collect"] = true
    },

    daily = {
        lastClaim = 0,
        currentStreak = 0,
    },

    stats = {
        eggsOpened = 0,
        monstersObtained = 0,
        fusionsCompleted = 0,
        cashEarned = 0,
        playtime = 0,
        firstJoinDate = 0,
        lastJoinDate = 0,
    },

    discovered = {                  -- Set para Pet Index
        -- ["goblin_roto"] = true
    },

    flags = {
        tutorialCompleted = false,
        firstEggOpened = false,
        firstMonsterPlaced = false,
        firstCollect = false,
        firstFusion = false,
        firstRebirth = false,
    },

    settings = {
        musicVolume = 1.0,
        sfxVolume = 1.0,
        autoSell = { enabled = false, rarities = {} },
    },
}
```

### Decisiones críticas del schema

- **UUIDs por monstruo individual** — permite variantes independientes y fusión limpia
- **placedMonsters separado de inventory** — los monstruos colocados también están en inventory, no se duplican datos
- **version desde día 1** — sistema de migrations preparado para updates futuras
- **discovered separado** — para Pet Index aunque ya no tengas el monstruo
- **flags para tutorial y achievements** — booleanos one-shot

### DataStore versionado

```lua
local PlayerDataStore = DataStoreService:GetDataStore("PlayerData_v1")
local TempDataStore = DataStoreService:GetDataStore("TempData_v1")
local LeaderboardStore = DataStoreService:GetOrderedDataStore("Cash_Leaderboard_v1")
```

⚠️ Si en algún momento necesitas reset total, cambias el nombre (`_v2`) — los datos viejos siguen disponibles para restauración.

---

## 📦 Assets necesarios

### Stack mínimo viable (0€ de inversión)

#### Sistema base
- **Tycoon Kit gratis** (Creator Store) — plot system, datastore base, botones de upgrade
- **Simulator Pack v1.3** (BuiltByBit) — daily rewards, egg hatchers con rarity values, pet inventory UI

#### Modelos de monstruos
- **Cigatronix Free Pet Pack** (DevForum) — 300+ pets de simulator
- **Aziuus Pet Pack** — 28 pets cartoon
- **Nobuser Pet Pack** — 5 básicos + 5 dorados + 1 huevo básico + 1 dorado
- **Eggs and Pets No Scripts FREE** (Creator Store) — pack mixto huevos + pets

> Filtra y selecciona 50-80 monstruos con estilo coherente. Calidad > cantidad inicial.

#### UI
- **FramedBlake Free Simulator UI Kit** — gratis
- **Biscuit's Free UI Pack V2** — alternativa
- **Lua Box Roblox UI Pack** — inventory grid, shop UI, upgrades UI con tiered layout, Pet Index

#### Mapa y decoración
- **Tycoon Map and Assets** (DevForum) — gratis, sin scripts
- Modelos low-poly fantasy market — para bazar del Hub
- Skybox packs en Toolbox

#### Audio y VFX
- Marketplace de Roblox: SFX gratis (egg crack, coin pickup, level up, magic spell)
- Particle Effects Pack en Creator Store
- Beam Effects para drops míticos

### Inversión opcional ($30-100)

- **TMATS Pet Pack** o BuiltByBit pet pack — calidad consistente
- **GFX Comet Simulator UI Kit** — UI profesional con 15 frames (Codes, Daily, Egg, HUD, Pets, Quest, Rebirth, etc.)
- VFX pack premium para drops míticos

### Lo que NO se ensambla — hay que crearlo

- ✅ Sistema de fusión (tu hook diferenciador)
- ✅ Lógica de generación pasiva por monstruo en plot
- ✅ Balance y configuración de todos los datos
- ✅ Naming, lore, presentación visual coherente
- ✅ Tutorial primeros 30 segundos

---

## 🗓 Roadmap de desarrollo (4 semanas)

> Asumiendo 2-3h/día de media con turnos rotativos. Total estimado: 55-65 horas.

### SEMANA 1 — Foundation (~12-15h)

**Objetivo:** Studio configurado, assets importados, estructura del proyecto lista.

#### Día 1-2: Setup técnico
- [ ] Configurar Studio + Rojo + repo Git en GitHub privado
- [ ] Activar API Services + HTTP Service en Game Settings
- [ ] Estructura de carpetas en disco para sync con Studio

#### Día 3-4: Importar assets base
- [ ] Importar Tycoon Kit y estudiar su estructura (sin modificar aún)
- [ ] Importar 3 pet packs gratis → seleccionar 50-60 monstruos coherentes
- [ ] Importar UI Kit en StarterGui (deshabilitado hasta uso)

#### Día 5-6: Estructura de datos
- [ ] Crear `MonsterData` ModuleScript completo
- [ ] Crear esqueletos de `EggData`, `RarityConfig`, `FusionRecipes`
- [ ] Backup inicial: publish + commit a Git

#### Día 7: Hub básico
- [ ] Plataforma central flotante
- [ ] 3-4 zócalos para máquinas de huevos
- [ ] SpawnLocation y pasarelas hacia plots
- [ ] Sin obsesión con estética — solo funcional

**🎯 Hito:** Entras al juego, ves el Hub vacío y los espacios para plots. 50+ monstruos en ReplicatedStorage. Código base preparado.

---

### SEMANA 2 — Core Loop (~15-18h)

**Objetivo:** Loop completo funcionando end-to-end.

#### Día 8-9: Sistema de plots
- [ ] Adaptar Tycoon Kit con slots discretos (no droppers)
- [ ] Sistema de asignación de plot al jugador al entrar
- [ ] LocalScript para detectar click en slot vacío
- [ ] RemoteEvent → Server valida y coloca modelo

#### Día 10-11: Sistema de huevos
- [ ] 3 máquinas de huevo en Hub (Básico, Avanzado, Premium)
- [ ] ProximityPrompt o ClickDetector para comprar
- [ ] Server valida cash → resta → ejecuta drop random
- [ ] Animación de apertura cinematográfica
- [ ] Efectos según rareza (Common simple → Mythic+ con beam y screen shake)

#### Día 12-13: Generación pasiva + recolección
- [ ] Server Script con loop cada 1s (cálculo de cash por plot)
- [ ] Texto flotante "+$X" sobre cada monstruo
- [ ] Hucha central con BillboardGui mostrando cash
- [ ] Click → transferencia a wallet con animación de monedas

#### Día 14: Save/Load + UI inventario
- [ ] DataStore con load/save funcional + pcall + retry
- [ ] Auto-save cada 60s + BindToClose
- [ ] UI de inventario con grid scrollable
- [ ] Botones colocar/vender en cada item

**🎯 Hito:** Juego JUGABLE end-to-end. Compras huevo → abres → colocas → ganas → recolectas → guarda al salir → restaura al volver.

---

### SEMANA 3 — Polish + Hooks (~15-18h)

**Objetivo:** Mecánicas diferenciadoras y juice.

#### Día 15-16: Sistema de fusión
- [ ] Modelo de altar de fusión en plot
- [ ] UI con 3 slots para meter monstruos
- [ ] Validación server (misma rareza, mismo monstruo)
- [ ] Animación: monstruos giran, se funden, sale el nuevo
- [ ] Sistema de variantes (Normal, Dorado, Arcoíris, Void)
- [ ] Material/textura/efectos diferentes según variante

#### Día 17: Rebirth + Upgrades
- [ ] Botón/NPC de Rebirth con coste y bonus visible
- [ ] Reset completo + bonus permanente aplicado
- [ ] 5 botones de upgrade del plot (cada uno con coste creciente)

#### Día 18-19: Juice (NO SALTARSE)
- [ ] Particle emitters en monstruos Legendary+
- [ ] Beams de luz vertical en Mythic+
- [ ] **Anuncio global mítico** — banner para todos los jugadores cuando alguien saca Mythic+
- [ ] SFX para todas las acciones (comprar, abrir, drop, recolectar, fusión, rebirth)
- [ ] Música ambient suave en Hub
- [ ] **Tutorial primeros 30s** — flechas animadas guiando al jugador (CRÍTICO)

#### Día 20-21: Daily login + UI polish
- [ ] Sistema de daily login con ciclo de 7 días
- [ ] UI con calendario visual
- [ ] Pulir todas las UIs (consistencia, animaciones TweenService, hover states, mobile-friendly)

**🎯 Hito:** Juego se siente COMPLETO. Profundidad (fusión/rebirth/upgrades), juice (VFX/sonidos/anuncios), retención (daily/tutorial).

---

### SEMANA 4 — Launch (~12-15h)

**Objetivo:** Monetizar, testear, presentar bien, publicar.

#### Día 22-23: Monetización
- [ ] Crear 5 gamepasses Tier 1 en dashboard de Roblox
- [ ] Crear 7-10 Developer Products iniciales
- [ ] Configurar imágenes (CRÍTICO para conversión)
- [ ] MarketplaceService listeners + aplicación de efectos
- [ ] UI de tienda in-game funcional

#### Día 24-25: Testing + balance
- [ ] Test multiplayer real con 2-3 personas
- [ ] Anotar bugs, confusiones, momentos donde algo no se siente bien
- [ ] Test obligatorio en móvil + PC
- [ ] Balance pass: ¿se llega a Rebirth en ~30h? Ajustar precios
- [ ] Bug fixing intensivo (datastore, restas en server, etc.)

#### Día 26-27: Presentación visual
- [ ] **Thumbnail principal** 1920×1080 (monstruo grande, dinero alrededor, texto enorme)
  - Si no tienes habilidad de diseño, contratar 1 freelance por 10-30€ (Fiverr/BuiltByBit)
- [ ] **Icon del juego** 512×512 (versión simplificada)
- [ ] **Trailer 15-30s** grabado con OBS, editado con CapCut
  - Hook visual fuerte (0-3s) → drops míticos (4-15s) → CTA (16-30s)
  - Música trending de TikTok

#### Día 28: Soft launch
- [ ] Configurar página del juego (nombre público, descripción con keywords, tags)
- [ ] Hacer público en Game Settings
- [ ] Crear Discord del juego con canales (#anuncios, #updates, #bugs, #suggestions)
- [ ] **Push de tráfico inicial:**
  - Subir 3-5 videos a TikTok (#roblox #robloxgames #robloxsimulator #brainrot)
  - Postear en r/roblox y r/RobloxGameDev (sin spam)
  - Contactar YouTubers/streamers pequeños (1-10k subs) ofreciendo Robux

**🎯 Hito final:** Juego PUBLICADO, jugable, monetizado, presentado, con primera tracción (50-500 jugadores día 1 si thumbnail/trailer son decentes).

### Reglas del roadmap

#### Lo que NO hacer en estas 4 semanas
- ❌ Sistema de robo / PvP (déjalo para v2)
- ❌ Trading entre jugadores (v2)
- ❌ Eventos limitados (post-launch)
- ❌ Más de 60-80 monstruos
- ❌ Mapa precioso (los simulators top tienen mapas feos)
- ❌ Sistema de pets que sigue al jugador (los monstruos viven en plot)

#### Si vas atrasado, prioridad de corte
1. Daily login bonus → quitar
2. Fusión Arcoíris/Void → solo Dorado
3. Trailer → solo thumbnail e icon
4. Rebirth → "Coming Soon" + meterlo en update semana 1 post-launch

#### NO se puede recortar bajo ningún concepto
- ✅ Loop core funcionando (semana 2)
- ✅ Save/Load funcional
- ✅ Tutorial primeros 30s
- ✅ Anuncio global de míticos
- ✅ Thumbnail decente

### Post-launch (semanas 5-8)

- **Semana 5:** Hot-fixes de bugs, balance basado en datos reales
- **Semana 6:** Primera update grande (5-10 monstruos nuevos, evento limitado de 1 semana)
- **Semana 7:** Sistema de robo / PvP si retención lo justifica
- **Semana 8:** Trading + leaderboards globales

---

## 💵 Plan de monetización

### Filosofía

**Lo que SÍ se monetiza:**
- ✅ Conveniencia / quality of life (auto-collect, slots extra, skip)
- ✅ Multiplicadores de progreso (×2 cash, ×2 luck)
- ✅ Cosméticos (skins, efectos, títulos)
- ✅ Acceso a contenido exclusivo (huevo VIP)
- ✅ Boosts temporales

**Lo que NO se monetiza:**
- ❌ Monstruos exclusivos imposibles de conseguir gratis
- ❌ Ventajas permanentes irreversibles
- ❌ Lockear contenido core detrás de paywall
- ❌ Loot boxes con chances ocultas

**Principio de oro:** un jugador F2P debe llegar al endgame igualmente, solo tarda más. Pago = 20-30% más cómodo, NO 200% más fuerte.

### TIER 1 — Gamepasses esenciales (LANZAMIENTO)

| Pase | Precio | Beneficio | Conversión esperada |
|---|---|---|---|
| Auto Collect | 99 R$ | Recoge cash auto cada 5s | 8-15% |
| VIP | 499 R$ | ×2 cash + slot extra + tag | 3-5% |
| Suerte ×2 | 399 R$ | Duplica chance rarezas altas | 4-7% |
| Inventario Expandido | 199 R$ | 100 → 250 slots inventario | 5-8% |
| Generación Offline 24h | 299 R$ | 2h → 24h offline gen | 3-6% |

### TIER 2 — Expansión (semana 2-4 post-launch)

| Pase | Precio | Beneficio |
|---|---|---|
| Triple Hatch | 249 R$ | Abre 3 huevos al precio de 1 |
| Auto Hatch | 199 R$ | Compra y abre huevos automáticamente |
| Speed Coil | 149 R$ | +50% velocidad de movimiento |
| Fusion Discount | 349 R$ | -50% coste de fusiones |
| Lucky Block Pet | 599 R$ | Mascota cosmética + 5% luck |

### TIER 3 — Premium (mes 2+)

| Pase | Precio | Beneficio |
|---|---|---|
| Ultra VIP | 1499 R$ | ×3 cash + 3 slots + auto-collect + triple hatch + tag exclusivo |

### Developer Products (consumibles)

#### Currency Packs (Gems)
| Pack | Precio | Gems | $/Gem |
|---|---|---|---|
| Pequeño | 49 R$ | 100 | 0.49 |
| Mediano (POPULAR) | 199 R$ | 500 | 0.40 |
| Grande (MEJOR VALOR) | 499 R$ | 1500 | 0.33 |
| Mega | 999 R$ | 3500 | 0.28 |

#### Boosts temporales
| Boost | Precio | Efecto | Duración |
|---|---|---|---|
| Cash ×2 | 49 R$ | Duplica generación | 30 min |
| Cash ×3 | 99 R$ | Triplica generación | 1 hora |
| Suerte ×3 | 79 R$ | ×3 chance rarezas altas | 30 min |
| Suerte ×5 | 149 R$ | ×5 chance rarezas altas | 30 min |
| All ×2 | 199 R$ | Cash + Suerte | 1 hora |

#### Packs de huevos
| Pack | Precio | Contenido |
|---|---|---|
| 10 Huevos Básicos | 29 R$ | ~$500 valor |
| 50 Huevos Avanzados | 199 R$ | ~$25K valor |
| 25 Huevos Premium | 399 R$ | ~$125K valor |
| 10 Huevos Cósmicos | 599 R$ | ~$500K valor |

⚠️ Estos packs deben dar **un poco menos** que comprar cash y huevos por separado. No romper economía interna.

#### Cash directo
| Pack | Precio | Cash |
|---|---|---|
| $10K | 49 R$ | $10.000 |
| $100K | 199 R$ | $100.000 |
| $1M | 499 R$ | $1.000.000 |
| $10M | 999 R$ | $10.000.000 |
| $100M | 1999 R$ | $100.000.000 |

### Triggers contextuales (las ventas más efectivas)

Estos prompts aparecen cuando el jugador tiene un problema específico:

- Inventario al 85% → "Inventario Expandido (+150 slots)"
- Boost activo expira → "¿Renovar tu Boost Cash ×2?"
- Le falta cash al comprar → "Te faltan $5K. ¿Pack de cash?"
- Después de fundir → "Triple Hatch para conseguir más doradas rápido"
- Primer Rebirth → "¿Acelerar el siguiente con VIP?"

**Convierten 5-10× mejor que la tienda pasiva.**

### UX de monetización — qué NO hacer

- ❌ Popups al entrar al juego
- ❌ Notificaciones de compra cada 30s
- ❌ Banners ocupando media pantalla durante gameplay
- ❌ Botones rojos parpadeantes con timers falsos
- ❌ Bloquear interfaces hasta cerrar anuncio
- ❌ Robux Reminder en chat ("¡tu amigo X ha comprado VIP!")

### Eventos limitados (mes 2+)

- Duración: 7-14 días, cada 3-4 semanas
- Currency exclusiva del evento (Caramelos en Halloween, etc.)
- Tienda exclusiva con monstruos NO obtenibles después → FOMO real
- Pack de Robux exclusivo (~999 R$): monstruo Mythic + currency + skin
- Battle Pass del evento (avanzado, mes 2+): track gratis + premium 799 R$

### Calendario de despliegue

| Fase | Qué se lanza |
|---|---|
| Día 1 | 5 gamepasses Tier 1 + 7-10 dev products básicos |
| Semana 2 | Triple Hatch, Auto Hatch, más boosts, packs grandes huevos |
| Semana 3-4 | Speed Coil, Fusion Discount, packs de cash, skip cooldown |
| Mes 2 | Primer evento limitado completo, Lucky Block Pet |
| Mes 3+ | Ultra VIP, Battle Pass, Trading + fees |

### Premium Payouts (pasivo)

Roblox paga automáticamente por minutos jugados de usuarios Premium. Maximizar con:
- Engagement (sistemas ya diseñados para esto)
- Premium-exclusive perks: +10% cash, 1 huevo gratis al día, slot extra
- Los perks de Premium NO deben ser tan fuertes como VIP gamepass

---

## 📊 KPIs y métricas

### Métricas core a vigilar

| Métrica | Objetivo decente | Top simulators |
|---|---|---|
| ARPU | 5-15 R$/usuario activo | 30-100+ R$ |
| ARPPU | 200-1000 R$ | 1000-5000 R$ |
| Conversion Rate | 3-5% | 8-12% |
| D1 Retention | 25-35% | 40-50% |
| D7 Retention | 8-15% | 20-25% |
| Session Length | 15-25 min | 30-45 min |

### Métricas de monetización específicas

- **Top selling pass:** debería ser Auto Collect. Si no, revisar precio/imagen.
- **Pass más rentable:** suele ser VIP por su precio alto.
- **Conversion por trigger:** medir qué triggers contextuales convierten.
- **Whales:** top 5% pagadores aportan 40-60% de ingresos. Diseñar productos para ellos.

### Estimaciones realistas de ingresos

| Tier de juego | CCU | Ingresos/mes |
|---|---|---|
| Mediocre | 50-200 | $100-500 |
| Decente | 500-2000 | $1.000-5.000 |
| Top | 5000-20.000 | $10K-50K |
| Top viral | 50.000+ | $100K+ |

> El 90% de juegos en Roblox no superan los 100 R$/día. Llegar a 500-2000 CCU sostenidos es ya un éxito objetivo. Objetivo realista para 3 meses: 100-500 CCU sostenidos, $200-1000/mes.

### Herramientas de analytics

- **GameAnalytics** (free) — estándar en Roblox
- **Dashboard nativo de Roblox** — métricas básicas
- **PlayFab** (Microsoft) — más complejo, para fase avanzada
- **Custom analytics propios** con HTTPService a backend propio (avanzado)

---

## 📚 Recursos y referencias

### Documentación oficial
- [Roblox Developer Hub](https://create.roblox.com/docs)
- [Lua/Luau Reference](https://luau-lang.org/)
- [DataStore Best Practices](https://create.roblox.com/docs/cloud-services/data-stores)
- [MarketplaceService](https://create.roblox.com/docs/reference/engine/classes/MarketplaceService)

### Assets gratuitos
- [Roblox Creator Store](https://create.roblox.com/store)
- [DevForum Community Resources](https://devforum.roblox.com/c/community-resources)
- [BuiltByBit Roblox Resources](https://builtbybit.com/resources/roblox/)

### Inspiración (juegos a estudiar)
- **Pet Simulator 99** — referencia de juice y progresión
- **Adopt Me** — referencia de UI cute y monetización suave
- **Steal a Brainrot** — referencia del público objetivo y humor
- **Lucky Block Simulator** — referencia de drops y rarezas

### Herramientas
- **Rojo** — sync Studio ↔ filesystem para Git workflow
- **GameAnalytics** — analytics gratuito
- **GitHub** — versionado privado del código
- **OBS + CapCut** — grabación y edición de trailer
- **Fiverr / BuiltByBit** — freelances para thumbnail/icon ($10-30)

### Marketing
- **TikTok** — el canal #1 para Roblox simulators
  - Hashtags: #roblox #robloxgames #robloxsimulator #brainrot
- **YouTube** — para trailer oficial y partnerships con creators
- **Reddit** — r/roblox, r/RobloxGameDev (con cuidado, no spam)
- **Discord** — comunidad propia del juego

---

## 📝 Notas finales

### Estado del documento

Este README es el **documento maestro de referencia** del proyecto. Se actualiza con cada decisión importante de diseño o cambio en sistemas.

### Próximos pasos inmediatos

1. ✅ Crear experiencia en Roblox Studio (HECHO)
2. 🔲 Setup de Rojo + repositorio Git
3. 🔲 Importar Tycoon Kit y estudiar estructura
4. 🔲 Importar pet packs gratuitos
5. 🔲 Crear `MonsterData` ModuleScript con 50-60 monstruos
6. 🔲 Construir Hub básico funcional

### Decisiones aún pendientes

- [ ] Nombre final del juego (público vs interno)
- [ ] Logo y branding completo
- [ ] Selección final de qué pet packs usar
- [ ] Diseño exacto del altar de fusión
- [ ] Música del juego (royalty-free o licenciada)

### Contacto

- **Dev principal:** Ruxyen (Rubén Teruel)
- **Brand:** Northycal
- **Discord del juego:** _(pendiente de crear)_

---

**Versión del documento:** 1.1
**Última actualización:** Mayo 2026
**Estado del proyecto:** Pre-desarrollo — preparando entorno

**Changelog:**
- v1.1 — Estructura de carpetas ampliada con todos los services nativos (Workspace, Lighting, SoundService, ReplicatedFirst, TextChatService) + sección de configuración visual de cada uno + orden de creación recomendado
- v1.0 — Documento inicial
