# CLAUDE.md — Captura de Auditorías de Taller (lives-auditorias-taller)

Las reglas generales de trabajo están en el repo privado `lives-instrucciones` (CLAUDE.md). Este archivo solo describe esta herramienta.

## Qué hace
`index.html` es el **formulario para el celular** "Captura de Auditorías · Hoja + prendas observadas". Lo usa la auditora en el taller o en la planta.
- Datos de la OP:
  - La auditora pone la fecha (la semana sale sola) y elige el taller (el proceso sale solo).
  - Elige la OP del maestro; de ahí se llenan grupo, estilo, color, cliente y tela.
- Procesos con fotos:
  - Por cada proceso se elige el tipo de documento y se toman las fotos.
  - Las fotos de hojas se enderezan y se realzan en el celular. Las fotos de prendas solo se reducen.
- Funciona sin señal: guarda una cola en IndexedDB (`lives_auditorias_fotos`) y la reenvía sola. Cada envío queda PENDIENTE, ENVIADO o ERROR.
- Un envío ya hecho se puede corregir (OP y color) o borrar.
- Antes de enviar exige fecha, taller, OP con color, al menos un proceso con foto y el tipo de documento.
- Lo capturado se ve en `lives-auditorias-visor`.

## Base de datos (Supabase lives-datos)
- `fotos_inicio`: catálogos y datos de la usuaria. Lee `cat_personal`, `cat_talleres`, `cat_clientes` y `fotos_tipos`.
- `fotos_ops`: maestro de OPs, sacado de la tabla `ops`.
- `fotos_sugerencias`: lo ya registrado, para contar repeticiones. Lee `fotos_registros`.
- `fotos_guardar`: guarda el envío en `fotos_registros` y `fotos_archivos`. Revisa que las fotos estén subidas, calcula el número de repetición y no duplica si se reintenta. Usa también `fotos_ajustes` y `cat_personal`.
- `fotos_corregir`: corrige OP y color en `fotos_registros` y `fotos_archivos`, y deja la constancia en `fotos_correcciones`.
- Fotos: bucket privado `fotos-taller`, con la ruta `AAAA/MM/<id>/<orden>.jpg` y su miniatura `_t.jpg`. Primero se suben las fotos y después se llama a `fotos_guardar`.

## Quién la usa
Según las entradas por el portal de los últimos 30 días al 21/10/2026: 16 personas. Son sobre todo auditoras de calidad (13) y supervisoras de calidad (2).

## Quién puede guardar
- Lo decide la base con la función de permiso `fotos_puede_capturar()`, que usa la tabla `permisos`.
- La usuaria además tiene que figurar como auditora activa en el catálogo.
- Algunas usuarias pueden elegir otra auditora (`yo.elige`), para cargar papeles antiguos. Lo devuelve la base.
- Corregir: solo quien registró el envío (`NO_ES_TUYO`).
- A Sistemas: `fotos_puede_capturar` es la pieza que reemplazan con su seguridad.
