# Patricia Alvarado — Portafolio de Ingeniería Aeronáutica e IA

Portafolio digital bilingüe (**EN / ES**) de Patricia Alvarado, futura ingeniera aeronáutica. Reúne los proyectos de las 10 semanas del curso de Inteligencia Artificial con su descripción, código, resultados y evidencias. Su identidad visual es un **plan de vuelo**: un holograma de rayos X (un avión 3D translúcido hecho a mano en `<canvas>`, sin librerías 3D) aparece, sale de la pantalla y revela el nombre dibujado trazo a trazo. Después, cada sección es un *waypoint* de la ruta y un altímetro lateral sigue el scroll.

**🔗 Sitio desplegado:** https://TODO-tu-proyecto.vercel.app  <!-- TODO: pega aquí tu URL de Vercel -->

![Captura del portafolio](docs/screenshot-hero.png)

| Habilidades como anatomía del avión | Móvil |
| --- | --- |
| ![Habilidades](docs/screenshot-lab.png) | <img src="docs/screenshot-mobile.png" width="220" alt="Vista móvil" /> |

---

## Tecnologías

| Área | Herramientas |
| --- | --- |
| Framework | **Next.js 16** (App Router) + **React 19** |
| Lenguaje | **TypeScript** (modo estricto) |
| Estilos | **Tailwind CSS v4** (tokens de tema en `src/app/globals.css`) |
| Tipografías | Unbounded (títulos) · Instrument Sans (texto) · JetBrains Mono (datos), vía `@fontsource` |
| Paleta | Negro `#050507` · Morado `#8b9dff` · Azul `#2f5bff` |
| Íconos | lucide-react |
| Backend | Route Handlers de Next.js: `POST /api/contact`, `GET /api/projects` |
| Control de versiones | Git + GitHub |
| Despliegue | Vercel |

## Secciones

| # | Sección | Concepto visual |
| --- | --- | --- |
| 00 | **Inicio (Hero)**: nombre, foto, especialidad, frase, botones, GitHub y LinkedIn | Holograma que despega y nombre dibujado |
| 01 | **Sobre mí**: quién soy, qué estudio, intereses, objetivos y fortalezas | Pase de abordar + manifiesto de pasajera |
| 02 | **Experiencia** | Bitácora de vuelo (logbook) |
| 03 | **Proyectos**: las 5 actividades del curso | Carrusel 3D (cover-flow); la tarjeta central abre el repositorio de GitHub de cada proyecto |
| 04 | **Habilidades** por categoría | Anatomía del avión: cada categoría ilumina un sistema (lenguajes → propulsión, web → fuselaje, datos → tanques, herramientas → superficies de control, IA → cabina, personales → alas) |
| 05 | **Formación académica** | Registro de entrenamiento con checkpoints |
| 06 | **Contacto**: correo, GitHub, LinkedIn y formulario con API REST | Torre de control |

**Intro con sonido:** la animación inicial se reproduce en cada carga y recarga (siempre vuelve arriba, sin conservar scroll ni `#sección`) con el audio `public/audio/f18-flyby.mp3` sincronizado: el pico del sonido coincide con el momento en que el holograma atraviesa la pantalla (`src/lib/hologram/introAudio.ts`). Si el navegador bloquea el audio automático, comienza en el primer clic/tecla/toque ya alineado con la animación. El botón *Skip* (o Esc/Enter/Espacio) lo detiene con un fade-out. Para ajustar el sincronismo cambia `PASS_AT` en ese archivo.

Botón **EN | ES** en el encabezado: cambia todos los textos y recuerda la elección (`localStorage`).

## Instalación y ejecución

Requisitos: Node.js 20 o superior.

```bash
git clone https://github.com/TODO-tu-usuario/TODO-tu-repo.git
cd TODO-tu-repo
npm install
cp .env.example .env.local   # opcional: solo si vas a enviar correos
npm run dev                  # http://localhost:3000
```

Otros comandos:

```bash
npm run build      # build de producción (también valida tipos)
npm run start      # sirve el build de producción
npm run typecheck  # revisa los tipos de TypeScript
npm run lint       # ESLint
```

## Estructura del proyecto

```
src/
├── app/
│   ├── layout.tsx                 # layout raíz: fuentes, idioma y encabezado
│   ├── page.tsx                   # página principal (todas las secciones)
│   ├── globals.css                # Tailwind + tokens del tema rayos X
│   ├── not-found.tsx
│   └── api/
│       ├── contact/route.ts       # POST /api/contact
│       └── projects/route.ts      # GET  /api/projects
├── components/
│   ├── hologram/HoloProvider.tsx  # conecta el motor 3D con React
│   ├── layout/                    # Header (botón EN|ES), FlightRail (altímetro) y Footer
│   ├── sections/                  # Hero, About, Experience, Projects (carrusel), Skills, Education, Contact
│   └── ui/                        # Waypoint, Reveal, ProjectArt, íconos
├── data/
│   ├── profile.ts                 # ✏️ TUS datos: enlaces, sobre mí, experiencia, habilidades, formación
│   └── projects.ts                # ✏️ TUS proyectos: enlace de GitHub, portada, textos
├── i18n/                          # diccionarios EN/ES + LanguageProvider (React Context)
└── lib/
    ├── validation.ts              # validación compartida por el formulario y la API
    └── hologram/                  # model.ts (geometría), engine.ts (render), chapters.ts (poses de habilidades), nameMark.ts (nombre)
public/
├── profile-placeholder.svg        # reemplázalo por tu foto (profile.jpg)
└── covers/                        # ✏️ (opcional) capturas para las portadas del carrusel
```

## API REST

### `POST /api/contact`

```json
{ "name": "Ana", "email": "ana@correo.com", "message": "Hola, me gustó tu portafolio" }
```

| Código | Respuesta | Cuándo |
| --- | --- | --- |
| 201 | `{ "ok": true, "delivered": false }` | Mensaje válido (`delivered: true` si se envió el correo) |
| 400 | `{ "ok": false, "error": "invalid_json" }` | El cuerpo no es JSON |
| 422 | `{ "ok": false, "errors": { "email": "email_invalid" } }` | Falla alguna validación |
| 429 | `{ "ok": false, "error": "rate_limited" }` | Más de 5 mensajes en 10 minutos |
| 502 | `{ "ok": false, "error": "delivery_failed" }` | Falló el proveedor de correo |

Validaciones (en `src/lib/validation.ts`, las usan tanto el navegador como el servidor):
- nombre: de 2 a 80 caracteres;
- correo: formato válido;
- mensaje: de 10 a 2000 caracteres.

También tiene un campo trampa (honeypot) contra bots.

### `GET /api/projects?lang=es`

Devuelve los proyectos en JSON (título, resumen, tecnologías y enlace de GitHub). Pruébalo abriendo `/api/projects?lang=es` en el navegador.

## Variables de entorno

Nunca subas `.env.local` a GitHub: ya está en `.gitignore`. Copia `.env.example` y, si quieres recibir los mensajes por correo con [Resend](https://resend.com), completa:

```
RESEND_API_KEY=...
CONTACT_TO_EMAIL=tu@correo.com
```

En Vercel: **Project → Settings → Environment Variables** (las mismas claves). Sin ellas, el formulario funciona igual y los mensajes quedan en los logs de Vercel.

## Despliegue en Vercel

1. Sube el proyecto a un repositorio **nuevo** en tu cuenta de GitHub.
2. Entra a https://vercel.com, inicia sesión con GitHub y pulsa **Add New → Project**.
3. Importa el repositorio. Vercel detecta Next.js solo; no hay que cambiar nada.
4. (Opcional) Agrega las variables de entorno.
5. Pulsa **Deploy** y pega la URL pública al inicio de este README.

## Proyectos y evidencias

Cada tarjeta del carrusel abre el repositorio de GitHub del proyecto. Ahí debe estar la evidencia: código, notebooks, datasets, capturas, resultados y un README que explique problema, objetivo, solución y resultados.

En `src/data/projects.ts`, para cada proyecto:

```ts
github: "https://github.com/tu-usuario/regresion-lineal",
cover: "/covers/regresion.png",   // opcional: captura en public/covers/
```

## Lo que aprendí

<!-- TODO: escríbelo con tus palabras. Ideas: -->
- Cómo se organiza un proyecto de Next.js con App Router: páginas, componentes y rutas de API.
- Cómo se comunican el frontend y el backend con REST y JSON, y por qué la validación también debe hacerse en el servidor.
- Cómo proteger secretos con variables de entorno y desplegar en Vercel.
- TODO: lo que más disfrutaste del curso y lo más difícil que aprendiste a resolver.

## Créditos

Diseño, código y modelo 3D originales. El avión es un concepto ilustrativo genérico y no representa ninguna aeronave real.
