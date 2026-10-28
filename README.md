# Chat local con Gemma 2B · web-llm

**Ver el demo en marcha: https://lmatas.github.io/webgpu/**

Un demo autocontenido: un chat que ejecuta **Gemma 2B por completo dentro del navegador**
con WebGPU (vía [web-llm](https://github.com/mlc-ai/web-llm)), sin servidores ni claves de API,
y con **búsqueda web** para responder con resúmenes citados.

## Qué hace

- Ejecuta el modelo en la GPU del visitante (web-llm, MLC AI). Nada sale de su equipo.
- Descarga los pesos una sola vez (≈1,4 GB para Gemma 2B) y los guarda en la caché del navegador:
  las siguientes visitas arrancan en segundos.
- Búsqueda web antes de responder: **LA Referencia** (casi 6 millones de artículos y tesis
  de repositorios académicos de Latinoamérica, consultada directamente y sin claves) o
  el **índice de Google** (con tu clave gratuita). El modelo redacta la respuesta articulando
  y contrastando varias fuentes (hasta 5) y las cita como [1], [2]… con los enlaces bajo
  cada respuesta.
- Panel de instrumentos: selector de modelos, temperatura, top-p, máximo de tokens,
  extensión de la respuesta (breve, normal o extensa, que ajusta también los tokens),
  prompt de sistema, medidor de tokens/s y botón de parar.

## Búsqueda web: LA Referencia y Google

**Por defecto la búsqueda usa LA Referencia**, la red federada de repositorios
académicos de Latinoamérica: su buscador (VuFind) expone una API REST abierta con CORS,
así que el navegador la consulta directamente, sin claves ni registro. Los resultados son
artículos, tesis y trabajos académicos con enlaces a sus repositorios.

Si prefieres el índice general de Google, elige «Google» como fuente y configura una clave
gratuita:

1. Crea un buscador programable en https://programmablesearchengine.google.com
   con «Buscar en toda la web» activada, y copia su **ID del buscador** (`cx`).
2. En https://console.cloud.google.com habilita la **Custom Search API** y crea una **clave de API**.
3. Elige Google en «Fuente de búsqueda web» (franja inferior) y pega ambas.

Las claves se guardan solo en el localStorage de tu navegador: no viajan a ningún servidor
ni quedan publicadas en el repo. Sin clave de Google, esa fuente responde sin contexto
y lo indica en el pie de cada respuesta.

## Requisitos del visitante

- Chrome o Edge (o un Safari muy reciente): hace falta **WebGPU** activado.
- Una GPU con ≈1,9 GB de VRAM para Gemma 2B. Para comprobar rápido que todo funciona
  está el selector con **SmolLM2 360M** (≈0,4 GB).
- En el grupo "Modelos grandes" del selector hay 7B-9B (Gemma 2 9B, Llama 3.1 8B,
  Qwen3 8B, Mistral 7B, Qwen3.5 9B): necesitan entre ≈4,6 y ≈6,4 GB de VRAM libres
  y generan notablemente más despacio que los modelos pequeños.

## Cómo está publicado

GitHub Pages, desde la rama `main` del repositorio
[github.com/lmatas/webgpu](https://github.com/lmatas/webgpu). Para replicarlo en otro sitio:

1. Sube `index.html` y este `README.md` a la raíz de un repositorio público.
2. **Settings → Pages → Build and deployment → Source: Deploy from a branch**, rama `main`,
   carpeta `/ (root)` → **Save**.

## Probar en local

```bash
python3 -m http.server 8000
# abre http://localhost:8000
```

(No hace falta ningún build ni dependencia: `index.html` es la aplicación entera;
web-llm se importa desde CDN en tiempo de ejecución.)

## Detalles técnicos

- Los pesos del modelo no se alojan aquí: web-llm los descarga de HuggingFace a cada visitante,
  así que el hosting solo sirve este HTML (unos KB).
- La búsqueda consulta LA Referencia (API REST v1 de su VuFind, abierta y con CORS:
  `buscador.lareferencia.info/api/v1/search?lookfor=…&type=AllFields`) o Google
  (Custom Search JSON API con la clave del visitante, en localStorage, nunca en el repo).
- Motor: [mlc-ai/web-llm](https://github.com/mlc-ai/web-llm) (Apache-2.0). Modelo por defecto:
  `gemma-2-2b-it-q4f16_1-MLC`.