# ITAP AI — Cloudflare Worker

## Arquitectura
La web estática de GitHub Pages llama a este Worker mediante `POST /api/chat`. El Worker incorpora `knowledge.md` como contexto del sitio y consulta un proveedor compatible con la API de chat completions. La clave nunca se incluye en el frontend ni en Git.

## 1. Crear el Worker
1. Entra a https://dash.cloudflare.com/ y abre **Workers & Pages**.
2. Crea un Worker llamado `itap-ai`.
3. Despliega el contenido de esta carpeta usando Wrangler desde tu equipo o el editor de Cloudflare. El archivo principal es `src/index.js`, la base de conocimiento compilada está en `src/knowledge.js` y el archivo de configuración es `wrangler.toml`.

## 2. Añadir la clave como secreto
En Cloudflare, abre el Worker > **Settings > Variables and Secrets > Add** y crea el secreto:
- Nombre: `AI_API_KEY`
- Valor: tu clave privada del proveedor de IA

No pegues la clave en GitHub, en el HTML, en mensajes públicos ni en `wrangler.toml`. Si la clave no es de OpenAI, configura también las variables de texto `AI_API_URL` (endpoint compatible con OpenAI Chat Completions) y `AI_MODEL` con los valores de tu proveedor.

## 3. Configuración de seguridad
La variable `ALLOWED_ORIGINS` está configurada para `https://cristinalb2012-ui.github.io`. Si el sitio se publica en otro dominio, actualiza esa variable con el origen exacto (sin ruta final). El Worker limita el tamaño de las solicitudes y el historial enviado; para producción con tráfico elevado, añade Cloudflare Rate Limiting o Turnstile.

## 4. Conectar el frontend
Tras desplegar, copia la URL del Worker, por ejemplo `https://itap-ai.TU-SUBDOMINIO.workers.dev`. En `index.html`, sustituye el valor de `ITAP_CHAT_API_URL` por esa URL seguida de `/api/chat`. El archivo `knowledge.md` es la versión editable para lectura; `src/knowledge.js` es la copia que el Worker importa en producción. Si cambias el contenido de la web, actualiza ambos.

## 5. Probar
Envía preguntas como:
- ¿Qué herramientas ofrece ITAP?
- ¿Cómo funciona la calculadora de tamaño de muestra?
- ¿Qué diferencia hay entre una cita narrativa y una parentética en APA 7?
- ¿Pueden garantizar que una revista está indexada en Scopus?

Comprueba también que una clave incorrecta produce un error comprensible y que la clave nunca aparece en las herramientas de desarrollo del navegador.

## Nota sobre el conocimiento
El archivo `knowledge.md` contiene texto visible extraído de la web actual. No es un entrenamiento del modelo ni una verificación independiente de las afirmaciones del sitio; el Worker lo entrega como contexto en cada consulta. Actualízalo cuando cambien los servicios o contenidos importantes.
