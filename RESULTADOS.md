# Resultados – Práctica 3.1-P4 Buzón ciudadano con folio

**Asignatura:** Desarrollo Sustentable (ACD-0908) · **Carrera:** Ing. en Sistemas Computacionales · TecNM Mazatlán
**Bot:** `@Buzon_amBIental_bot` · **Escenario Make:** `Buzon_Ciudadano_Equipo#` · **Hoja:** `Buzon_Ciudadano_Equipo#` (pestaña `Reportes`)

## 1. Resumen

El buzón quedó activo y funcionando: recibe mensajes por Telegram, responde con instrucciones a `/start`, registra cada reporte en Google Sheets con usuario, nombre, fecha local y estatus, y devuelve al ciudadano un folio con formato `AMB-AAMMDD-HHmmss`.

## 2. Pruebas realizadas (27–28 de octubre de 2026)

| # | Mensaje enviado | Resultado esperado | Resultado obtenido |
|---|---|---|---|
| 1 | `/start` | Instrucciones de uso, sin registrar | Correcto: llegó el mensaje de bienvenida y no se creó fila |
| 2 | Reporte de la colonia Hacienda del Seminario (basura en esquina de calle) | Folio + registro | **Falló parcialmente:** se registró, pero el folio salió vacío (ver sección 3) |
| 3 | `Hola` / `Gracias` | Ignorar o validar | Se registraron como reportes con folio (limitación, ver sección 4) |
| 4 | `Chris - se inundo el kraken` (otro usuario) | Folio + registro con su usuario | Correcto: se guardó con su usuario y nombre, con folio propio |

Después de corregir el error del folio, los reportes se guardaron con los seis campos completos: `Folio`, `Fecha` (hora de Mazatlán), `Usuario`, `Nombre`, `Reporte`, `Estatus = Recibido`.

## 3. Error encontrado y solución

- **Síntoma:** el bot respondía "Tu folio es:" sin número y la celda `Folio` quedaba vacía, aunque la ejecución en Make aparecía como exitosa.
- **Causa:** el módulo *Set variable* no devuelve el valor con el nombre de la variable (`{{4.folio}}`), sino en el campo `value`.
- **Solución:** cambiar la referencia a `{{4.value}}` en el módulo *Add a Row* y en la respuesta de Telegram.
- **Resultado:** a partir de ese cambio todos los reportes salen con folio.
- **Observación:** en la interfaz de Make, *Set variable* aparece marcado como *Deprecated*; funciona, pero en un proyecto real convendría sustituirlo.

## 4. Limitaciones detectadas

- Cualquier texto que no empiece con `/` se registra como reporte (por ejemplo `Hola`), porque no hay validación del formato `Colonia - Problema`.
- Cada folio se basa en la hora (`AAMMDD-HHmmss`): dos mensajes en el mismo segundo podrían repetirlo.
- El estatus siempre es `Recibido`; no hay seguimiento ni respuesta de una autoridad.
- Si la persona no tiene nombre de usuario de Telegram, la columna `Usuario` queda vacía (se conserva el nombre).
- El bot solo funciona en español y requiere celular, Telegram e internet.

## 5. Mejoras propuestas

1. Validar el formato del mensaje con un filtro (por ejemplo, que contenga ` - `) y pedir al usuario que lo corrija.
2. Agregar un contador o una parte aleatoria al folio para evitar duplicados.
3. Agregar estatus posteriores (`En atención`, `Resuelto`) y un comando para consultar el estatus por folio.
4. Canalizar el reporte por correo a la dependencia competente (municipal, estatal o PROFEPA).
5. Ofrecer alternativas no digitales (teléfono, ventanilla) para no excluir a quienes no usan Telegram.

## 6. Conclusiones

El objetivo se cumplió: un buzón ciudadano que registra reportes ambientales con fecha, usuario y folio sin escribir código. Lo más valioso fue ver que el folio es lo que convierte un mensaje en un reporte que se puede rastrear, y que la herramienta técnica solo sirve si hay una autoridad que reciba, responda y dé seguimiento. También aprendí a revisar los datos que realmente llegan a cada módulo, porque un escenario puede salir en verde y aun así guardar información incompleta.
