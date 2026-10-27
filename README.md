# Chat local con Gemma 2B · web-llm

Un demo autocontenido: un chat que ejecuta **Gemma 2B por completo dentro del navegador**
con WebGPU (vía [web-llm](https://github.com/mlc-ai/web-llm)), sin servidores ni claves de API,
y con **búsqueda web** para responder con resúmenes citados.

## Qué hace

- Ejecuta el modelo en la GPU del visitante (web-llm, MLC AI). Nada sale de su equipo.
- Descarga los pesos una sola vez (≈1,4 GB para Gemma 2B) y los guarda en la caché del navegador:
  las siguientes visitas arrancan en segundos.
- Búsqueda web antes de responder (API abierta de Wikipedia: primero en español, respaldo en inglés)
  y resumen con citas [1], [2]… enlazadas bajo cada respuesta.
- Panel de instrumentos: selector de modelos, temperatura, top-p, máximo de tokens,
  prompt de sistema, medidor de tokens/s y botón de parar.

## Requisitos del visitante

- Chrome o Edge (o un Safari muy reciente): hace falta **WebGPU** activado.
- Una GPU con ≈1,9 GB de VRAM para Gemma 2B. Para comprobar rápido que todo funciona
  está el selector con **SmolLM2 360M** (≈0,4 GB).

## Publicar en GitHub Pages

1. Crea un repositorio público nuevo en GitHub, por ejemplo `webllm-gemma-2b`.
2. Sube a la raíz `index.html` y `README.md` (comandos abajo).
3. En el repositorio: **Settings → Pages → Build and deployment → Source: Deploy from a branch**,
   rama `main`, carpeta `/ (root)` → **Save**.
4. En un minuto queda en `https://TU_USUARIO.github.io/webllm-gemma-2b/`.

```bash
git init
git add .
git commit -m "Demo de web-llm con Gemma 2B"
git branch -M main
git remote add origin https://github.com/TU_USUARIO/webllm-gemma-2b.git
git push -u origin main
```

## Probar en local antes de publicar

```bash
python3 -m http.server 8000
# abre http://localhost:8000
```

(No hace falta ningún build ni dependencia: `index.html` es la aplicación entera;
web-llm se importa desde CDN en tiempo de ejecución.)

## Detalles técnicos

- Los pesos del modelo no se alojan aquí: web-llm los descarga de HuggingFace a cada visitante,
  así que el hosting solo sirve este HTML (unos KB).
- La búsqueda consulta las APIs abiertas de MediaWiki/Wikipedia, que permiten CORS sin claves;
  por eso una página estática puede buscar en la web sin servidor propio.
- Motor: [mlc-ai/web-llm](https://github.com/mlc-ai/web-llm) (Apache-2.0). Modelo por defecto:
  `gemma-2-2b-it-q4f16_1-MLC`.