Práctica 3.1 – Buzón ciudadano con folio
Campo	Detalle
Asignatura	Desarrollo Sustentable (ACD-0908)
Carrera	Ingeniería en Sistemas Computacionales
Institución	TecNM – Mazatlán, Sinaloa
Unidad / Tema	3. Escenario socio-cultural / 3.1 Sociedad, organización social
Práctica	3.1-P4 Buzón ciudadano con folio
Herramientas	Telegram (BotFather), Make, Google Sheets

Flujo: Telegram (Watch Updates) → Router → /start: instrucciones | reporte: Set variable (folio) → Google Sheets (Add a Row) → Telegram (respuesta con folio).

Blueprint del Escenario Make

Zona horaria del perfil de Make: America/Mazatlan (se configura en el perfil, no va dentro del blueprint). Los campos __IMTHOOK__, __IMTCONN__ y el ID de la hoja están en null / marcador por seguridad; al importar se asignan las conexiones propias. El token del bot no se incluye ni se sube a GitHub.

json
{
  "name": "Buzon_Ciudadano_Equipo#",
  "flow": [
    {
      "id": 1,
      "module": "telegram:WatchUpdates",
      "version": 1,
      "parameters": {
        "__IMTHOOK__": null
      },
      "mapper": {},
      "metadata": {
        "designer": {
          "x": 0,
          "y": 300,
          "name": "Watch Updates"
        }
      }
    },
    {
      "id": 2,
      "module": "builtin:BasicRouter",
      "version": 1,
      "mapper": null,
      "metadata": {
        "designer": {
          "x": 300,
          "y": 300,
          "name": "Router"
        }
      },
      "routes": [
        {
          "flow": [
            {
              "id": 3,
              "module": "telegram:SendReplyMessage",
              "version": 1,
              "parameters": {
                "__IMTCONN__": null
              },
              "filter": {
                "name": "Comando /start",
                "conditions": [
                  [
                    {
                      "a": "{{1.message.text}}",
                      "b": "/start",
                      "o": "text:equal"
                    }
                  ]
                ]
              },
              "mapper": {
                "chatId": "{{1.message.chat.id}}",
                "messageThreadId": "",
                "parseMode": "",
                "replyMarkup": "",
                "replyToMessageId": "",
                "replyMarkupAssembleType": "reply_markup_enter",
                "text": "¡Bienvenido al Buzón Ambiental!\n\nPara reportar un problema ambiental de tu colonia, envía UN mensaje con este formato:\nColonia - Problema\n\nEjemplo: Juan Carrasco - Basura acumulada en el canal de la calle principal.\n\nDescribe el lugar con calle o referencia. No incluyas datos personales de terceros (nombres, teléfonos ni domicilios exactos).\n\nRecibirás un folio para dar seguimiento a tu reporte."
              },
              "metadata": {
                "designer": {
                  "x": 600,
                  "y": 0,
                  "name": "Bienvenida /start"
                }
              }
            }
          ]
        },
        {
          "flow": [
            {
              "id": 4,
              "module": "util:SetVariable",
              "version": 1,
              "parameters": {},
              "filter": {
                "name": "Reporte (no es comando)",
                "conditions": [
                  [
                    {
                      "a": "{{1.message.text}}",
                      "b": "/",
                      "o": "text:notstartwith"
                    }
                  ]
                ]
              },
              "mapper": {
                "name": "folio",
                "scope": "roundtrip",
                "value": "AMB-{{formatDate(now; \"YYMMDD-HHmmss\")}}"
              },
              "metadata": {
                "designer": {
                  "x": 600,
                  "y": 300,
                  "name": "Set variable folio"
                },
                "interface": [
                  {
                    "name": "value",
                    "label": "folio",
                    "type": "any"
                  }
                ]
              }
            },
            {
              "id": 5,
              "module": "google-sheets:addRow",
              "version": 2,
              "parameters": {
                "__IMTCONN__": null
              },
              "mapper": {
                "mode": "select",
                "from": "drive",
                "spreadsheetId": "/<ID_HOJA_Buzon_Ciudadano_Equipo#>",
                "sheetId": "Reportes",
                "includesHeaders": true,
                "insertDataOption": "INSERT_ROWS",
                "insertUnformatted": false,
                "valueInputOption": "USER_ENTERED",
                "values": {
                  "0": "{{4.value}}",
                  "1": "{{formatDate(now; \"DD/MM/YYYY HH:mm\")}}",
                  "2": "{{1.message.from.username}}",
                  "3": "{{1.message.from.first_name}}",
                  "4": "{{1.message.text}}",
                  "5": "Recibido"
                }
              },
              "metadata": {
                "designer": {
                  "x": 900,
                  "y": 300,
                  "name": "Add a Row"
                }
              }
            },
            {
              "id": 6,
              "module": "telegram:SendReplyMessage",
              "version": 1,
              "parameters": {
                "__IMTCONN__": null
              },
              "mapper": {
                "chatId": "{{1.message.chat.id}}",
                "messageThreadId": "",
                "parseMode": "",
                "replyMarkup": "",
                "replyToMessageId": "",
                "replyMarkupAssembleType": "reply_markup_enter",
                "text": "Reporte recibido.\nTu folio es: {{4.value}}\nEstatus: Recibido\nGuarda este número para dar seguimiento a tu reporte."
              },
              "metadata": {
                "designer": {
                  "x": 1200,
                  "y": 300,
                  "name": "Respuesta con folio"
                }
              }
            }
          ]
        }
      ]
    }
  ],
  "metadata": {
    "instant": true,
    "version": 1,
    "scenario": {
      "roundtrips": 1,
      "maxErrors": 3,
      "autoCommit": true,
      "autoCommitTriggerLast": true,
      "sequential": true,
      "slots": null,
      "confidential": false,
      "dataloss": false,
      "dlq": false,
      "freshVariables": false
    },
    "designer": {
      "orphans": []
    }
  }
}
Evidencias de Funcionamiento
Creación del Bot en BotFather Creación del bot @Buzon_amBIental_bot con /newbot; el token se guardó solo como conexión en Make.
Estructura de la Hoja de Google Sheets Hoja Buzon_Ciudadano_Equipo#, pestaña Reportes, fila 1 con Folio, Fecha, Usuario, Nombre, Reporte, Estatus.
Escenario Completo en Make Diagrama con Watch Updates, Router (filtros /start y "no empieza con /"), Set variable folio, Add a Row y respuesta; escenario en estado Activo.
Pruebas de Chat en Telegram Respuesta con instrucciones a /start y reportes con formato Colonia - Problema que reciben su folio.
Registro Exitoso en Google Sheets Filas registradas con folio autogenerado AMB-AAMMDD-HHmmss, fecha local de Mazatlán, usuario, nombre, reporte y estatus Recibido.
Preguntas de Reflexión

1. ¿Por qué es importante que un reporte ciudadano reciba un folio? ¿Qué pasa con los reportes de palabra o en redes sociales? El folio convierte un mensaje en un trámite: deja constancia de qué se reportó, cuándo y quién lo hizo, y da al ciudadano un número para dar seguimiento y exigir respuesta. Sin folio no hay registro verificable ni plazo que se pueda reclamar; un reporte de palabra o una publicación en redes se pierde en el flujo de mensajes, depende de que alguien lo vea y la autoridad puede alegar que nunca lo recibió.

2. ¿Qué organización resultó más y menos accesible? Por lo que revisamos en la Actividad 1, las instancias federales como PROFEPA son las más accesibles en cuanto a medios: tienen denuncia en línea, teléfono y entregan folio de la denuncia popular. Las menos accesibles suelen ser las municipales y estatales, donde el trámite depende de ir a oficina o llamar en horario de oficina, y no siempre hay formulario en línea ni folio claro. Las organizaciones civiles son cercanas y fáciles de contactar, pero no tienen facultad para sancionar, solo para canalizar o dar visibilidad. (Ajustar con los datos exactos de la tabla de la Actividad 1.)

3. ¿Qué problemas ambientales se repitieron y quién debe atenderlos? En los reportes de prueba se repitieron la basura acumulada en calles y canales y las inundaciones por drenaje o arroyos. La basura y el drenaje corresponden primero al ayuntamiento (servicios públicos y organismo operador de agua); las afectaciones a zonas federales, costa, humedales o fauna corresponden a PROFEPA y SEMARNAT; y la autoridad ambiental estatal atiende lo que sea de competencia local. Las organizaciones civiles ayudan a documentar y presionar.

4. ¿Quién puede quedar fuera de un buzón digital como el nuestro? Adultos mayores con poca familiaridad con Telegram, personas sin celular, sin datos o sin internet estable (colonias periféricas, zonas rurales), personas con discapacidad visual si el bot no es accesible, y quienes hablan lengua indígena o no leen con fluidez. Además, el bot usa un formato fijo en español. Esto afecta la equidad: reportan más quienes tienen acceso y habilidades digitales, y los problemas de las zonas más vulnerables, que suelen ser los más graves, quedan sin registrar. Un canal digital debe complementarse con ventanilla, teléfono y mensajes de voz.

5. ¿Qué tendría que pasar para que fuera un canal formal?

Receptor: una dependencia con facultades (municipal, estatal o federal) que reciba los reportes, no solo una hoja de cálculo del equipo.
Responsable: persona o área asignada por folio, con canalización a la autoridad competente.
Plazo: tiempos de respuesta definidos (acuse inmediato, respuesta en un plazo legal).
Seguimiento: que el ciudadano pueda consultar el estatus (Recibido → En atención → Resuelto) con su folio.
Garantías: aviso de privacidad, protección de datos y validación de identidad cuando se requiera.
