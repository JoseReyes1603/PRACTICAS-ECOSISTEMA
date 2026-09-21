# PRACTICAS-ECOSISTEMA# Escenario "Ecosistema": Identificación de Organismos con IA y Telegram

Identificación automática de organismos del jardín del Tecnológico mediante fotografía, Telegram y un Agente de Inteligencia Artificial en Make.com.

## Descripción

El objetivo de este proyecto es automatizar el reconocimiento de campo de organismos (productores, consumidores y descomponedores) dentro de la materia de Desarrollo Sustentable con Automatización. El estudiante envía una fotografía desde el jardín del campus a un bot de Telegram; un Agente de IA configurado en Make.com analiza la imagen y responde en tiempo real con el nombre del organismo, su nivel trófico y su función en el ecosistema.

## Objetivos de aprendizaje

* Diseñar y configurar una automatización en Make.com que procese imágenes enviadas a un bot de Telegram.
* Aplicar visión por computadora mediante el módulo **Make AI Agent** para clasificar organismos vivos y su rol trófico.
* Implementar control de flujo con un **Router** y filtros condicionales para discriminar mensajes con y sin imagen.
* Documentar técnicamente la arquitectura y variables clave del escenario para su trazabilidad y evaluación.

## Imagenes

<img width="500" height="300" alt="Captura make ecosistema" src="https://github.com/user-attachments/assets/360ebfd9-fa47-43c9-91f7-da1d2e313062" />


## Código
{
  "name": "Escenario Ecosistema - Guía de Automatización",
  "flow": [
    {
      "id": 3,
      "module": "telegram:WatchUpdates",
      "version": 1,
      "parameters": {
        "connection": "Ecosistema (webhook de Telegram Bot API)"
      },
      "mapper": {},
      "metadata": {
        "designer": {
          "x": 0,
          "y": 0
        },
        "restore": {},
        "expect": [
          {
            "name": "message",
            "type": "collection",
            "spec": [
              {
                "name": "chat",
                "type": "collection",
                "spec": [
                  {
                    "name": "id",
                    "type": "number"
                  }
                ]
              },
              {
                "name": "photo",
                "type": "array"
              }
            ]
          }
        ]
      }
    },
    {
      "id": 4,
      "module": "builtin:BasicRouter",
      "version": 1,
      "metadata": {
        "designer": {
          "x": 300,
          "y": 0
        }
      },
      "routes": [
        {
          "flow": [
            {
              "id": 5,
              "module": "telegram:DownloadFile",
              "version": 1,
              "parameters": {
                "connection": "Ecosistema Telegram Bot connection"
              },
              "mapper": {
                "file_id": "{{3.message.photo[].file_id}}"
              },
              "metadata": {
                "designer": {
                  "x": 600,
                  "y": 150
                }
              }
            },
            {
              "id": 8,
              "module": "make-ai-agent:RunAgent",
              "version": 1,
              "parameters": {
                "connection": "Ecosistema IA",
                "model": "gpt-5-nano",
                "response_format": "text",
                "max_output_length": "50%",
                "recursion_limit": 300,
                "history_count": 10
              },
              "mapper": {
                "input": "Analiza la imagen adjunta siguiendo tus instrucciones.",
                "input_files": [
                  {
                    "data": "{{5.fileOutput}}",
                    "file_name": "{{5.fileName}}"
                  }
                ],
                "system_prompt": "Eres un asistente educativo que identifica organismos en fotos tomadas por estudiantes en el jardín del Tecnológico, para la materia de Desarrollo Sustentable, tema 'El Ecosistema'.\n\nCuando recibas una imagen, responde SIEMPRE en este formato, sin texto adicional antes o después:\n\n[nombre probable del organismo]\n[Productor / Consumidor / Descomponedor]\n[rol en el ecosistema en máximo 15 palabras]\n\nSi la imagen no muestra un organismo vivo, responde únicamente:\n\"❌ No identifico un organismo. Intenta con una planta, insecto u otro ser vivo.\"\n\nReglas estrictas:\n- Máximo 35 palabras en total.\n- Sin introducciones, sin despedidas, sin explicaciones extra.\n- Responde siempre en español."
              },
              "metadata": {
                "designer": {
                  "x": 900,
                  "y": 150
                }
              }
            },
            {
              "id": 9,
              "module": "telegram:SendTextMessage",
              "version": 1,
              "parameters": {
                "connection": "Ecosistema Telegram Bot connection"
              },
              "mapper": {
                "chat_id": "{{3.message.chat.id}}",
                "text": "{{8.response}}"
              },
              "metadata": {
                "designer": {
                  "x": 1200,
                  "y": 150
                }
              }
            }
          ],
          "filter": {
            "name": "Tiene Foto",
            "conditions": [
              [
                {
                  "a": "{{3.message.photo}}",
                  "o": "exist"
                }
              ]
            ]
          }
        },
        {
          "flow": [
            {
              "id": 6,
              "module": "telegram:SendTextMessage",
              "version": 1,
              "parameters": {
                "connection": "Ecosistema Telegram Bot connection"
              },
              "mapper": {
                "chat_id": "{{3.message.chat.id}}",
                "text": "Envíame una foto del organismo (planta, insecto, hongo, etc.) para poder identificarlo"
              },
              "metadata": {
                "designer": {
                  "x": 600,
                  "y": -150
                }
              }
            }
          ],
          "filter": {
            "name": "No tiene Foto",
            "conditions": [
              [
                {
                  "a": "{{3.message.photo}}",
                  "o": "text:equal",
                  "b": ""
                }
              ]
            ]
          }
        }
      ]
    }
  ]
}



## Video del funcionamiento
[Ver video en YouTube](https://youtube.com/shorts/CDEY1mLPeto?feature=share)

## Resultados
[Reporte de Resultados - Escenario Ecosistema.pdf](https://github.com/user-attachments/files/32480095/Reporte.de.Resultados.-.Escenario.Ecosistema.pdf)


