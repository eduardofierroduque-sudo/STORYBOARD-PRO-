# StoryboardPro AI

Aplicacion web que convierte un brief creativo en un storyboard visual de 4 escenas
con presentacion descargable en PDF. Arquitectura multi-proveedor: el usuario conecta
su propia API key del servicio que prefiera.

## Arquitectura

- **Frontend**: HTML5 + Tailwind CSS (CDN) + JavaScript Vanilla
- **IA Narrativa**: API OpenAI-compatible via proxy generico (OpenAI, DeepSeek, Groq, etc.)
- **Generacion Visual**: Multi-proveedor (HF FLUX, DALL-E, Pollinations gratis, personalizado)
- **Exportacion**: jsPDF (CDN) — empaquetado en el navegador del cliente
- **Servidor**: Node.js unificado — sirve estaticos, proxy de texto y proxy de imagenes

## Estructura de archivos

```
/
  server.js     -- Servidor unificado (puerto 3000)
  index.html    -- Interfaz con panel de configuracion de APIs
  app.js        -- Logica completa (multi-proveedor, PDF, placeholders)
  README.md     -- Documentacion
```

## Requisitos

1. Node.js instalado (v16+)
2. Una API key de tu proveedor preferido (OpenAI, DeepSeek, HuggingFace, etc.)

## Instalacion y uso

### 1. Iniciar el servidor

```bash
node server.js
```

El servidor arranca en `http://localhost:3000`.

### 2. Configurar APIs

Abre `http://localhost:3000` en el navegador:

1. Haz clic en **"Configurar APIs"**
2. En **API de Texto**: elige tu proveedor, pega tu API key, verifica el modelo
3. En **API de Imagenes**: elige tu proveedor, pega tu key (o deja Pollinations para usar el servicio gratuito)
4. Haz clic en **"Guardar configuracion"**

La configuracion se guarda en localStorage del navegador (no se envia a ningun servidor).

### 3. Generar storyboard

Escribe un brief creativo en el campo de texto y haz clic en **"Generar storyboard PDF"**.

## Proveedores soportados

### Texto (API OpenAI-compatible)

| Proveedor | Modelo sugerido |
|-----------|-----------------|
| OpenAI | gpt-4o |
| OpenCode Zen | deepseek-v4-flash-free |
| DeepSeek | deepseek-chat |
| Groq | llama-3.3-70b-versatile |
| Together AI | meta-llama/Llama-3.3-70B-Instruct-Turbo |
| OpenRouter | openai/gpt-4o |
| Personalizado | Cualquier endpoint compatible |

### Imagenes

| Proveedor | Requiere API key |
|-----------|:---:|
| HuggingFace FLUX.1 Schnell | Si (HF Token) |
| HuggingFace FLUX.1 Dev | Si (HF Token) |
| HuggingFace SDXL Turbo | Si (HF Token) |
| OpenAI DALL-E 3 | Si (OpenAI Key) |
| OpenAI DALL-E 2 | Si (OpenAI Key) |
| Pollinations.ai | No (gratis) |
| Personalizado | Depende |

## Flujo de generacion

```
Brief → API de Texto (guion JSON con 4 escenas)
      → API de Imagenes (8 imagenes, 2 por escena)
        ├─ Proveedor configurado (si hay key)
        ├─ Pollinations.ai (fallback gratuito)
        └─ Canvas placeholder (ultimo recurso)
      → PDF con jsPDF
```

## Deploy a produccion

El servidor unificado se despliega en cualquier plataforma que soporte Node.js:

| Plataforma | Comando |
|-----------|---------|
| **Render.com** | `node server.js` |
| **Railway** | `node server.js` |
| **Fly.io** | `node server.js` |
| **VPS propio** | `node server.js` |

El puerto se configura con la variable de entorno `PORT` (por defecto 3000).

## Seguridad

- Las API keys se guardan en **localStorage** del navegador del usuario
- El servidor proxy **no almacena** ninguna key — solo forwardea peticiones
- Las keys nunca se envian a servidores de terceros no configurados por el usuario

## Licencia

Uso interno. Proyecto de generacion de storyboards con IA.
