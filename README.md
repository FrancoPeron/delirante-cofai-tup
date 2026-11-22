# COFAI — Aplicación de café de especialidad

> Trabajo Práctico Integrador y Progresivo (TPI)
> Organización Empresarial · Tecnicatura Universitaria en Programación a Distancia · UTN

**Cofia** es una aplicación Expo + React Native Web para explorar métodos de café, usar una calculadora de recetas, guardar recetas propias y consultar un asistente de IA. Fue pensada para Café Delirante (tostadora y cafeterías de especialidad de Bariloche) y acerca el café de especialidad a quienes recién empiezan.

## Enlaces

- Repositorio público: [https://github.com/FrancoPeron/Cofia](https://github.com/FrancoPeron/delirante-cofai-tup)

## Integrantes

| Integrante | Usuario de GitHub | Rol |
|---|---|---|
| Franco Matías Perón Mora | [@FrancoPeron](https://github.com/FrancoPeron) | Líder de proyecto, desarrollo Frontend e integración de IA |
| matko841 | [@matko841](https://github.com/matko841) | Backend y base de datos, QA y revisión de código |

> Equipo reducido a efectos del TPI: el segundo integrante corresponde a una segunda cuenta utilizada para la revisión de pares.

**Docente titular:** Prof. Gabriela Martínez · **Docente tutor:** Pablo García

## Stack

- Expo, React 19, React Native / React Native Web
- TypeScript
- Firebase Auth
- Express (proxy/backend local)
- Gemini API, accedida a través del backend

## Scripts

| Comando | Qué hace |
|---|---|
| `npm run dev` | Inicia Expo |
| `npm run web` | Inicia Expo en web |
| `npm run android` / `npm run ios` | Inicia Expo para Android / iOS |
| `npm run server` | Inicia el backend local (`server/index.js`) |
| `npm run lint` | Ejecuta `tsc --noEmit` |
| `npm run export:web` | Exporta el build web estático |
| `npm run export:android` | Exporta assets para Android |
| `npm run export:all` | Exporta la app completa con Expo |

## Ejecutar localmente

Requisitos: Node.js y npm.

1. Instalar dependencias: `npm install`
2. Crear `.env.local` en la raíz con al menos:
   ```
   EXPO_PUBLIC_API_BASE_URL=http://localhost:8787
   GEMINI_API_KEY=tu_api_key
   REQUIRE_FIREBASE_AUTH=false
   ```
3. Iniciar el backend: `npm run server`
4. Iniciar la app: `npm run dev` (o `npm run web` para web)

**Producción**
- `GEMINI_API_KEY` debe vivir solo en el servidor; nunca en el cliente.
- Para exportar, definí `EXPO_PUBLIC_API_BASE_URL` apuntando al backend público.
- Con Firebase Auth en el backend, activá `REQUIRE_FIREBASE_AUTH=true` y configurá las credenciales del servicio.
- En el emulador de Android usá `http://10.0.2.2:8787` si `localhost` no responde.

## Estructura del proyecto

```
.
├── App.tsx                 # Shell principal: navegación, estado global, timer, asistente
├── App.styles.ts           # Estilos del shell
├── AppLogic.tsx            # Prototipo/legado de lógica UI (no es la entrada actual)
├── index.tsx               # Arranque de la app
├── types.ts                # Tipos y constantes compartidas
├── constants.tsx           # Constantes de UI y globales
├── components/
│   ├── pages/
│   │   ├── Home/           # Portada
│   │   ├── Tools/          # Calculadora, cafetera y carrusel de recetas
│   │   ├── Assistant/      # Chat con IA
│   │   ├── AddRecipe/      # Alta y edición de recetas
│   │   ├── Profile/        # Perfil, favoritos y recetas propias
│   │   ├── ViewAll/        # Listados completos
│   │   └── MethodDetail/   # Detalle de un método
│   └── ui/                 # RecipeCard, CoffeeMakerCard, ExtractionMethodCard, Typography
├── data/                   # catalog.ts (recetas, cafeteras, métodos) y news.ts
├── services/               # geminiService, firebaseAuthService, googleDriveBackupService, newsService
├── server/index.js         # Backend/proxy local para IA y flujos protegidos
├── assets/                 # Imágenes y fuentes
└── docs/
    ├── bpmn/               # Diagramas de procesos (.png)
    └── entregas/           # Entregas 1 a 3 (PDF)
```

**Cómo se relaciona con la organización de COFAI (Entrega 3)**

| Área | Dónde vive en el código |
|---|---|
| Frontend e integración de IA | `App.tsx`, `components/`, `services/geminiService.ts` |
| Backend y base de datos | `server/index.js`, `services/firebaseAuthService.ts`, `data/` |
| Servicio externo de IA | Gemini API, consumida solo desde el backend |

**Convenciones**
- El estado global principal vive en `App.tsx`.
- Tools tiene componentes propios cuando no conviene reutilizar la UI global.
- La IA consume el backend definido por `EXPO_PUBLIC_API_BASE_URL`.
- Las recetas de usuario se gestionan en el estado local y con backup/restauración en JSON.

## Modelado de procesos (BPMN 2.0)

Se modelaron los tres procesos principales como diagramas independientes.

### 1. Inicio de sesión
![Inicio de sesión](docs/bpmn/login_cofai.png)

El usuario ingresa sus credenciales, se validan y se concede o deniega el acceso. Si olvidó su contraseña, se inicia la recuperación.

### 2. Preparación de café (secuencia guiada)
![Preparación guiada](docs/bpmn/preparacion_cofai.png)

Configuración inicial, ciclo de pasos guiados (pasos comunes y paso de extracción con diagnóstico instantáneo) y cierre con "¡Preparación lista!".

### 3. Asistente de IA (globo flotante global)
![Asistente de IA](docs/bpmn/asistente_ia_cofai.png)

Desde cualquier pantalla el usuario abre el chat y envía una consulta. El backend la envía a la API externa de IA, valida la respuesta y la registra; si no es válida, se muestra un mensaje de error.

## Flujo de trabajo con Git

- `main` solo recibe cambios por Pull Request.
- Ramas secundarias con prefijo: `docs/…`, `feat/…`, `fix/…`.
- Commits descriptivos con formato `tipo: descripción en presente` (por ejemplo `docs: agrega diagrama BPMN del asistente de IA`).
- Todo PR requiere la revisión de al menos una persona antes del merge.
