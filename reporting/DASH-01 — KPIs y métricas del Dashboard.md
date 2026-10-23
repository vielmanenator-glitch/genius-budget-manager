# DASH-01 — KPIs y métricas del Dashboard

Oct 23, 2026 · @maxi

El dashboard de Genius para SueñoSimple y TechStore muestra 15 indicadores: 7 de presupuesto, 4 comparativas y 4 de landings y leads. El indicador principal es el % de ejecución sobre todas las campañas (74,4 % al 23/10/2026).

## KPIs de presupuesto

Los 4 KPIs base ya existen como medidas en el Power BI; faltan 3, y uno de ellos necesita la tabla de Gastos. Valores en ARS, calculados sobre las 6 campañas.

| # | KPI | Medida DAX | Cálculo | Valor | Visual | Estado |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | Presupuesto asignado | presupuesto total | SUM(Campañas\[Presupuesto\]) | 630.000 | Tarjeta | Hecho |
| 2 | Presupuesto gastado | Gasto total | SUM(Campañas\[Gastado\]) | 469.000 | Tarjeta | Hecho |
| 3 | Presupuesto disponible | saldo disponible | SUM(Campañas\[Disponible\]) | 161.000 | Tarjeta | Hecho |
| 4 | % de ejecución (principal) | % ejecucion | DIVIDE(\[Gasto total\], \[presupuesto total\], 0) | 74,4 % | Medidor | Hecho |
| 5 | Campañas activas | Campañas activas (nueva) | Cantidad con Estado = "active"; aparte, las vencidas sin cerrar | 3 según la API, 0 vigentes por fecha | Tarjeta | Falta (datos nuevos) |
| 6 | Campañas en alerta | Campañas en alerta (nueva) | Cantidad con Gastado / Presupuesto ≥ 90 % | 2 (Black Friday, Google Ads Marzo) | Tarjeta + color en barras | Falta (datos actuales) |
| 7 | Gasto sin respaldo | Gasto sin respaldo (nueva) | \[Gasto total\] − SUM(Gastos\[Monto\]) | 195.600 (Google Ads Marzo) | Tarjeta | Falta (datos nuevos) |

Las medidas 1 a 4 están hechas, pero se muestran en gráficos de barras: falta pasarlas a tarjetas. El KPI 5 necesita las fechas de cada campaña para separar las vencidas, y el 7 la tabla de Gastos; ambos datos están en la versión de Adrian del `extract.py`.

## Comparativas y filtros

Las comparativas por campaña ya están en el dashboard; por cliente se puede hacer hoy, y categoría y tiempo necesitan la tabla de Gastos.

| # | Métrica | Qué muestra | Visual | Estado |
| --- | --- | --- | --- | --- |
| 8 | Ejecución por campaña | Gastado vs. presupuesto y % de cada campaña, con color de alerta (el color aún falta) | Barras horizontales | Hecho |
| 9 | Ejecución por cliente | Asignado vs. gastado: SueñoSimple 380.000 / 273.400 (71,9 %), TechStore 250.000 / 195.600 (78,2 %) | Barras agrupadas | Falta (datos actuales) |
| 10 | Gasto por categoría | Medios 152.800, creatividad 47.200, honorarios 40.000, herramientas 33.400 | Barras o torta | Falta (datos nuevos) |
| 11 | Gasto en el tiempo | Gasto registrado por mes, según la fecha de cada gasto | Línea | Falta (datos nuevos) |

**Filtros (segmentadores):** cliente, estado de campaña, tipo de campaña y período. Cada gráfico lleva título.

## KPIs de landings y leads

Estos 4 KPIs salen de Landing CRM, que hoy no está en el Power BI: hay que sumar sus datos al `extract.py` en otra tarea.

| # | KPI | Cálculo | Valor | Visual | Estado |
| --- | --- | --- | --- | --- | --- |
| 12 | Landings activas | Landings con estado active / total | 2 de 4 (50 %) | Tarjeta | Falta (datos nuevos) |
| 13 | Leads totales | Cantidad de leads, por cliente y por landing | 3, todos de SueñoSimple | Tarjeta + barras por landing | Falta (datos nuevos) |
| 14 | % de landings con leads | Landings con al menos un lead / total | 1 de 4 (25 %) | Tarjeta | Falta (datos nuevos) |
| 15 | Costo por lead (referencial) | Gastado del cliente / leads del cliente | SueñoSimple 91.133 | Tabla por cliente | Falta (datos nuevos) |

Dato a destacar: las 2 landings activas no tienen leads, y los 3 leads están en una landing en borrador.

## Reglas y definiciones

- **% de ejecución principal:** se calcula sobre todas las campañas (74,4 %). El 78,8 % de la API, que solo cuenta activas, se muestra como referencia y rotulado.
- **Campaña activa:** estado "active" en Budget Manager. Se muestran aparte las vencidas sin cerrar (estado active con fecha de fin pasada): hoy son las 3.
- **Umbrales de alerta:** amarillo desde 90 % de ejecución, rojo por encima de 100 %. Son los de la versión de Adrian, pendientes de validar con el cliente.
- **Costo por lead:** solo por cliente y como referencia. No hay un campo que vincule landing con campaña, y las fechas de campañas (nov-2025 a mar-2026) no coinciden con las de los leads (abr-2026).
- **Moneda:** ARS en todos los montos.

## Fuentes de datos y pendientes

| Fuente | Tablas | Cubre los KPIs |
| --- | --- | --- |
| `report.xlsx` actual (`extract.py` de `main`) | Campañas, Resumen | 1–4, 6, 8, 9 |
| `report.xlsx` de la versión de Adrian | tblCampanas, tblGastos, tblGastosCategoria, tblResumenCliente | Suma 5, 7, 10, 11 |
| Landing CRM (`localhost:3000`) | Landings, Leads | 12–15; falta agregarlo al `extract.py` |

Pendientes para otras tareas:

- [ ] Pasar el Power BI a la versión de Adrian del `report.xlsx`.
- [ ] Agregar landings y leads de Landing CRM al `extract.py`.
- [ ] Reemplazar la ruta fija al Excel por un parámetro, para que el `.pbix` funcione en otras computadoras.
