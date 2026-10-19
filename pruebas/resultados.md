# Pruebas del bot

## Qué se midió

1. **Intención:** si el bot clasifica bien cada mensaje como `busqueda`, `reclamo`, `consulta_tecnica` u `otro`.
2. **Disponibilidad:** en las búsquedas, si informa correctamente si la pieza tiene existencias.

## Cómo se hicieron

- Se escribieron **30 mensajes de prueba** y se etiquetó a mano la respuesta correcta de cada uno ([casos.csv](casos.csv)).
- Los casos cubren: piezas con existencias, piezas con 0 existencias, referencias que **no están** en el catálogo (6312, 6209), reclamos sin la palabra "reclamo", preguntas técnicas que mencionan piezas del catálogo y saludos.
- Un workflow de n8n (`Pruebas Bot Piezas Andinas`) lee el **mismo catálogo** ([catalogo.csv](../catalogo/catalogo.csv)) y usa el **mismo prompt** que el bot en producción, envía cada mensaje a Claude, aplica la misma verificación por código y compara el resultado con lo esperado.
- Al final calcula los porcentajes automáticamente. Se puede repetir después de cada cambio del prompt o del catálogo.

## Resultados (18-oct-2026)

| Métrica | Resultado |
|---|---|
| Intención correcta | 30/30 (100 %) |
| Disponibilidad correcta, resultado final | 18/18 (100 %) |
| Disponibilidad correcta, solo la IA | 16/18 (89 %) |
| Correcciones hechas por el código | 2 |
| Tiempo total | ~55 s (~1,8 s por mensaje) |

## Hallazgo principal

En los casos 8 y 27 (referencias inexistentes), la IA no marcó la disponibilidad. El nodo de código, que consulta las existencias en la tabla `catalogo`, la fijó en "no disponible". Por eso el diseño final es: **la IA interpreta, el código decide**.

## Limitaciones

- Los mensajes fueron escritos por el autor, no por clientes reales.
- La muestra es pequeña (30 casos) y los resultados corresponden a una ejecución; el modelo puede variar entre ejecuciones.
- Siguiente paso: repetir la evaluación varias veces y agregar casos con errores de ortografía y mensajes con varias piezas.
