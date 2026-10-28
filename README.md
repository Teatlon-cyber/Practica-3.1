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
