# MLOPSII-tp4

## Mini-TP 4 — Inferencia en streaming

Notebook: [`mini-tp4/mini_tp4_resuelto.ipynb`](mini-tp4/mini_tp4_resuelto.ipynb)

Streaming sobre mi modelo de la Sesión 1 (`gout-demanda-rf` v1.0.0): un productor emite eventos a
ritmo fijo, con un shock inflacionario a mitad del flujo (+60% de precio, promociones de 20% a 50%),
y un consumidor los puntúa online y calcula métricas por ventana (throughput, latencias p95, drift).

| | resultado |
|---|---|
| frescura (mediana / p95) | streaming 1,8 s / 2,9 s · batch 8,0 s / 15,2 s |
| costo por evento | streaming 11,6 ms · batch 0,022 ms (536x) |
| capacidad vs ritmo del productor | 83 ev/s contra 100 ev/s: saturación, p95 de 407 ms a 2,9 s |
| micro-lotes (hasta 32 eventos) | p95 de 25 ms sosteniendo 248 ev/s |
| drift | `precio_unitario` a 1,76 σ en la primera ventana post-shock |

**Qué noté:** la demanda predicha casi no se movió con el shock (74–76), así que un monitoreo que
sólo mirara la salida no habría visto nada: un random forest no extrapola precios fuera del rango
de entrenamiento. El drift hay que vigilarlo en las features. Y la alerta de latencia no pedía
cambiar el modelo sino la infraestructura: micro-lotes o más consumidores.
