# Documentación Final – Workflow n8n: Cálculo y carga de día de la semana (por localidad)

## Objetivo
Automatizar la carga del **día de la semana** en un Google Sheet, tomando la **localidad** del cliente y consultando la API pública **WorldTimeAPI** para obtener el `day_of_week`.

---

## Descripción general del flujo
El workflow se ejecuta cada 5 minutos.  
En cada ejecución toma un registro del Google Sheet, consulta la API con la localidad, convierte el `day_of_week` numérico a texto (Lunes, Martes, etc.), actualiza la fila y continúa con el siguiente registro.  
Al finalizar todos los registros, envía un mail notificando que el proceso terminó.

---

# Explicación nodo por nodo

## 1) Schedule Trigger (cada 5 minutos)
Este nodo inicia el workflow automáticamente cada 5 minutos.  
Su función es ejecutar el proceso de forma periódica sin intervención manual.

---

## 2) Google Sheets – Get Row (leer primer registro)
Este nodo consulta el Google Sheet y obtiene el primer registro disponible.  
Desde ese registro se toma el dato clave **Localidad**, que será utilizado para la consulta a la API.

---

## 3) HTTP Request – WorldTimeAPI
Este nodo realiza una llamada GET a la API con la localidad del registro:  
`https://worldtimeapi.org/api/timezone/America/Argentina/{{ $json.Localidad }}`  
La respuesta incluye el campo **day_of_week**, que representa el día en formato numérico.

---

## 4) Switch (según day_of_week)
Este nodo evalúa el valor recibido en `day_of_week`.  
Dependiendo del número (1, 2, 3, etc.), deriva el flujo por la ruta correspondiente para asignar el día correcto.

---

## 5) Set – Asignación de variable Dia (texto)
Este nodo crea una variable llamada **Dia** con el valor del día en texto.  
Ejemplo: si `day_of_week = 1`, se asigna `Dia = "Lunes"`, y así sucesivamente.

---

## 6) Google Sheets – Update Row (guardar el día)
Este nodo actualiza el registro del Google Sheet agregando el valor de **Dia**.  
De esta forma se completa la columna **Día de la semana** con el texto correspondiente.

---

## 7) Re-ejecución / Loop para siguiente registro
Una vez actualizado el registro, el workflow continúa con el siguiente.  
Este proceso se repite hasta completar todos los registros del sheet.

---

## 8) Email – Notificación de fin de proceso
Cuando ya no quedan registros por procesar, se ejecuta este nodo.  
Su objetivo es enviar un correo confirmando que el workflow finalizó correctamente.

---

# Resultado esperado
- Cada fila del Google Sheet queda actualizada con el día de la semana en formato texto.
- El proceso se ejecuta automáticamente cada 5 minutos.
- Al finalizar el total de registros, se envía un mail informando el cierre del proceso.

---
