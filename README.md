# StoryboardPro AI

Aplicacion web que convierte un brief creativo en un storyboard visual de 4 escenas
(2 planos por escena) con presentacion descargable en PDF. Arquitectura multi-proveedor:
el usuario conecta su propia API key del servicio que prefiera, sin depender de un
unico modelo.

## Arquitectura

- **Frontend**: HTML5 + Tailwind CSS (CDN) + JavaScript Vanilla (ES5)
- **IA Narrativa**: Multi-proveedor — API OpenAI-compatible generica + Gemini nativo
- **Generacion Visual**: Multi-proveedor con fallback en cascada (HF FLUX, DALL-E,
  Gemini Imagen, Pollinations gratis, Canvas placeholder)
- **Exportacion**: jsPDF (CDN) — empaquetado 100% en el navegador del cliente
- **Servidor**: Node.js unificado (puerto 3000) — estaticos + proxy texto + proxy imagenes

## Estructura de archivos

```
/
  server.js     -- Servidor unificado
  index.html    -- Interfaz con panel de configuracion de APIs
  app.js        -- Logica completa
  README.md     -- Documentacion
```

## Inicio rapido

```bash
# 1. Iniciar servidor
node server.js

# 2. Abrir navegador
http://localhost:3000

# 3. Configurar APIs
Click en "Configurar APIs" > elegir proveedor > pegar API key > Guardar

# 4. Escribir brief y generar
```

## Proveedores soportados

### Texto (8 proveedores)

| Proveedor | Modelo sugerido | Formato |
|-----------|-----------------|---------|
| OpenAI (GPT-4o) | gpt-4o | OpenAI |
| Google Gemini | gemini-2.0-flash | Gemini nativo |
| OpenCode Zen | deepseek-v4-flash-free | OpenAI |
| DeepSeek | deepseek-chat | OpenAI |
| Groq | llama-3.3-70b-versatile | OpenAI |
| Together AI | Llama-3.3-70B-Instruct-Turbo | OpenAI |
| OpenRouter | openai/gpt-4o | OpenAI |
| Personalizado | Cualquier endpoint | OpenAI |

### Imagenes (8 proveedores)

| Proveedor | Requiere key | Calidad |
|-----------|:---:|:---:|
| Google Gemini Imagen | Si (Gemini key) | Muy alta |
| HuggingFace FLUX.1 Schnell | Si (HF Token) | Alta |
| HuggingFace FLUX.1 Dev | Si (HF Token) | Muy alta |
| HuggingFace SDXL Turbo | Si (HF Token) | Alta |
| OpenAI DALL-E 3 | Si (OpenAI Key) | Muy alta |
| OpenAI DALL-E 2 | Si (OpenAI Key) | Alta |
| Pollinations.ai | No (gratis) | Media |
| Personalizado | Depende | Variable |

## Flujo de generacion

```
Brief
  └→ API de Texto (guion JSON: 4 escenas x 2 planos)
       └→ API de Imagenes (8 imagenes)
            ├─ Proveedor configurado (con key)
            ├─ Pollinations.ai (fallback gratuito automatico)
            └─ Canvas placeholder (ultimo recurso)
                 └→ PDF descargable con jsPDF
```

## Errores inteligentes

Cada error indica exactamente que API fallo y el motivo:

- `API de Texto (OpenAI): API key invalida o sin creditos.`
- `API de Texto (Gemini): Limite de uso alcanzado.`
- `API de Imagenes (HF FLUX): sin respuesta. Cambiando a Pollinations gratis...`

Status en tiempo real durante generacion:

```
Imagen 1/8: HF FLUX.1 Schnell
Imagen 2/8: Pollinations.ai (gratis)
...
Completado. Imagenes: 6x HF FLUX.1 Schnell, 2x Pollinations.ai
```

## Seguridad

- Las API keys se guardan en **localStorage** del navegador del usuario
- El servidor proxy **no almacena** ninguna key — solo forwardea peticiones
- Las keys nunca se envian a servidores de terceros no configurados por el usuario
- El token nunca se incluye en la URL del remote de git

## Deploy a produccion

Servidor unificado compatible con cualquier plataforma Node.js:

| Plataforma | Comando |
|-----------|---------|
| Render.com | `node server.js` |
| Railway | `node server.js` |
| Fly.io | `node server.js` |
| VPS / Hostinger | `node server.js` |

Puerto configurable via variable de entorno `PORT` (defecto: 3000).

## Licencia

Proyecto de generacion de storyboards con IA — fierroduque.com
