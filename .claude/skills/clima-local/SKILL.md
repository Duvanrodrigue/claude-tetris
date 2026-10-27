---
name: clima-local
description: Consulta el clima actual y el pronóstico de los próximos días para Bucaramanga, Colombia (ubicación por defecto) o para una ciudad indicada. Úsala cuando el usuario pregunte por el clima, la temperatura, si va a llover o el pronóstico.
argument-hint: "[ciudad opcional]"
allowed-tools: Bash(curl *)
---

# Clima local

Obtiene el clima con [wttr.in](https://wttr.in), que no requiere API key.

## Pasos

1. Si el usuario dio una ciudad (`$ARGUMENTS`), úsala. Si no, usa por defecto `Bucaramanga,Colombia`.
2. Ejecuta (reemplaza `CIUDAD`; en ciudades con espacios usa `+`, por ejemplo `Buenos+Aires`):

   ```bash
   curl -s "https://wttr.in/CIUDAD?format=j1&lang=es"
   ```

   Por defecto: `curl -s "https://wttr.in/Bucaramanga,Colombia?format=j1&lang=es"`

3. Del JSON, usa estos campos:
   - `nearest_area[0].areaName[0].value` y `country[0].value`: lugar detectado.
   - `current_condition[0]`: `temp_C`, `FeelsLikeC`, `humidity`, `windspeedKmph`, `winddir16Point`, `precipMM`, `lang_es[0].value` (descripción).
   - `weather[0..2]`: `date`, `mintempC`, `maxtempC` y `hourly[*].chanceofrain` para el pronóstico de 3 días.

## Respuesta

Responde en español, breve:

- Lugar y descripción actual.
- Temperatura, sensación térmica, humedad y viento.
- Pronóstico de hoy y los dos días siguientes: mínima, máxima y probabilidad máxima de lluvia.

## Errores

- Si `curl` falla o devuelve algo que no es JSON, informa que no se pudo consultar el servicio y no inventes datos.
- Si la ciudad no existe, pide al usuario que la revise.
