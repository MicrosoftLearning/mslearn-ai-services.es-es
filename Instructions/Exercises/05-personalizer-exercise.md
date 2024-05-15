---
lab:
  title: Uso del Personalizador de Azure AI con los cuadernos de Visual Studio Code para simular un bucle
---
> **Importante** A partir de septiembre de 2023, ya no se pueden crear nuevos recursos de Personalizador. Use solo este repositorio como referencia si ya tiene un recurso de Personalizer.

En este ejercicio, usará Personalizador de Azure AI con cuadernos en Visual Studio Code para simular una ruta de aprendizaje.

## Creación de un recurso de Personalizador de Azure AI mediante Azure Portal

1. En Azure Portal, busque **servicios de Azure AI**. Después, seleccione **Crear** en **Personalizador** en la lista de resultados.

   ![Captura de pantalla de Azure Portal en la que se muestra cómo crear un recurso de Personalizador de Azure AI](../media/ai-personalizer/create-personalizer-portal.png)

1. Seleccione la suscripción, escriba un nombre de grupo de recursos y el nombre del recurso. En plan de tarifa, elija **Gratis F0**.
1. Seleccione **Revisar y crear** para revisar las opciones y, después, seleccione **Crear** para crear el recurso.
1. Vaya al recurso de Personalizador de Azure AI recién creado y, después, en el panel Claves y punto de conexión, copie y pegue la **clave** y el **punto de conexión** en algún lugar seguro para usarlos más adelante:

   ![Captura de pantalla que muestra el panel Clave y punto de conexión.](../media/ai-personalizer/copy-key-endpoint.png)

1. Seleccione Configurar en el panel de navegación y establezca el **Tiempo de espera de recompensa** en **10 minutos** (si aún no está establecido) y establezca la**Frecuencia de actualización del modelo** en **15 segundos **.
1. Seleccione **Guardar**.

## Configuración del cuaderno

1. En el editor de Visual Studio Code, presione **Ctrl+Mayús+P** y seleccione **Crear: Nuevo Jupyter Notebook**.
1. Guarde el archivo y asígnele el nombre **my-notebook** en el dispositivo.
1. Ahora debe instalar las extensiones necesarias. Seleccione **Seleccionar kernel** en la parte superior derecha del cuaderno. Después, seleccione **Instalar/Habilitar extensiones sugeridas**.

   ![Captura de pantalla que muestra cómo instalar las extensiones.](../media/ai-personalizer/8-install-enable-extensions-code.png)

   > [!NOTE]
   > Si ya lo ha hecho antes, no verá esta opción y puede omitir este paso.

1. Espere a que se instalen las extensiones y, después, seleccione **Entornos de Python...** en la lista desplegable que aparece.
1. A continuación, seleccione el entorno recomendado superior.

## Preparación de los datos

Para este ejercicio, crearemos una lista de usuarios y una lista de productos de café que podemos usar como datos base para Personalizador de Azure AI.

1. Copie el siguiente código JSON en un archivo vacío y guarde ese archivo como `users.json` en la misma carpeta que el archivo del cuaderno.

   ```json
   {
     "Alice": {
       "Sunny": {
         "Morning": "Cold brew",
         "Afternoon": "Iced mocha",
         "Evening": "Cold brew"
       },
       "Rainy": {
         "Morning": "Latte",
         "Afternoon": "Cappucino",
         "Evening": "Latte"
       },
       "Snowy": {
         "Morning": "Cappucino",
         "Afternoon": "Cappucino",
         "Evening": "Cappucino"
       }
     },
     "Bob": {
       "Sunny": {
         "Morning": "Cappucino",
         "Afternoon": "Iced mocha",
         "Evening": "Cold brew"
       },
       "Rainy": {
         "Morning": "Latte",
         "Afternoon": "Latte",
         "Evening": "Latte"
       },
       "Snowy": {
         "Morning": "Iced mocha",
         "Afternoon": "Iced mocha",
         "Evening": "Iced mocha"
       }
     },
     "Cathy": {
       "Sunny": {
         "Morning": "Latte",
         "Afternoon": "Cold brew",
         "Evening": "Cappucino"
       },
       "Rainy": {
         "Morning": "Cappucino",
         "Afternoon": "Latte",
         "Evening": "Iced mocha"
       },
       "Snowy": {
         "Morning": "Cold brew",
         "Afternoon": "Iced mocha",
         "Evening": "Cappucino"
       }
     },
     "Dave": {
       "Sunny": {
         "Morning": "Iced mocha",
         "Afternoon": "Iced mocha",
         "Evening": "Iced mocha"
       },
       "Rainy": {
         "Morning": "Latte",
         "Afternoon": "Latte",
         "Evening": "Latte"
       },
       "Snowy": {
         "Morning": "Cappucino",
         "Afternoon": "Cappucino",
         "Evening": "Cappucino"
       }
     }
   }
   ```

1. Luego, copie este código y guárdelo en un archivo denominado `coffee.json`:

   ```json
   [
     {
       "id": "Cappucino",
       "features": [
         {
           "type": "hot",
           "origin": "kenya",
           "organic": "yes",
           "roast": "dark"
         }
       ]
     },
     {
       "id": "Cold brew",
       "features": [
         {
           "type": "cold",
           "origin": "brazil",
           "organic": "yes",
           "roast": "light"
         }
       ]
     },
     {
       "id": "Iced mocha",
       "features": [
         {
           "type": "cold",
           "origin": "ethiopia",
           "organic": "no",
           "roast": "light"
         }
       ]
     },
     {
       "id": "Latte",
       "features": [
         {
           "type": "hot",
           "origin": "brazil",
           "organic": "no",
           "roast": "dark"
         }
       ]
     }
   ]
   ```

1. Copie y pegue este código en un archivo y guárdelo como `example-rankrequest.json`:

   ```json
   {
     "contextFeatures": [],
     "actions": [],
     "excludedActions": [],
     "eventId": "",
     "deferActivation": false
   }
   ```

## Establecimiento del punto de conexión y la clave

1. En la parte superior del cuaderno, agregue el código siguiente para incluir los módulos necesarios:

   ```python
   import json
   import matplotlib.pyplot as plt
   import random
   import requests
   import time
   import uuid
   import datetime
   ```

1. Seleccione la celda y, después, seleccione el botón Ejecutar situado a la izquierda de la celda:

   ![Captura de pantalla que muestra el botón Ejecutar.](../media/ai-personalizer/8-press-run.png)

   > [!NOTE]
   > Seleccione el botón Ejecutar cada vez que rellene una nueva celda. Si se le pide que instale el paquete ipykernel, seleccione **Instalar**.

1. Seleccione **+ Código** en la parte superior del cuaderno para crear una nueva celda de código. Después agregue el siguiente código:

   ```python
   # Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
   personalization_base_url = "<your-endpoint>"
   resource_key = "<your-resource-key>"
   ```

1. Reemplace el valor **personalization_base_url** por el punto de conexión copiado y el valor **resource_key** por la clave.

## Seguimiento de iteraciones

1. A continuación, cree el código. Como una ayuda, tome nota de las horas de inicio y finalización de las funciones iterativas que usará más adelante. Agregue este código en una nueva celda:

   ```python
   # Print out current datetime
   def currentDateTime():
       currentDT = datetime.datetime.now()
       print (str(currentDT))

   # ititialize variable for model's last modified date
   modelLastModified = ""

   def get_last_updated(currentModifiedDate):

       print('-----checking model')

       # get model properties
       response = requests.get(personalization_model_properties_url, headers = headers, params = None)

       print(response)
       print(response.json())

       # get lastModifiedTime
       lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

       if (currentModifiedDate != lastModifiedTime):
           currentModifiedDate = lastModifiedTime
           print(f'-----model updated: {lastModifiedTime}')
   ```

1. No olvide ejecutar la nueva celda una vez que haya agregado el nuevo código.

## Obtención de la configuración de la directiva y el servicio

1. Después, deberá validar el estado del servicio. Para hacerlo, obtenga la directiva y la configuración del servicio. Para ello, agregue el siguiente código a una nueva celda:

   ```python
   def get_service_settings():

       print('-----checking service settings')

       # get learning policy
       response = requests.get(personalization_model_policy_url, headers = headers, params = None)

       print(response)
       print(response.json())

       # get service settings
       response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

       print(response)
       print(response.json())
   ```

1. Asegúrese de ejecutar la nueva celda de código.

El código consta de una función que realiza dos llamadas a la API de servicio. Cuando se llama a la función, devuelve los valores de servicio mediante la respuesta.

## Configuración de direcciones URL para llamadas y lectura de archivos JSON

Ahora agregará código para:

- compilar las direcciones URL usadas en las llamadas a REST
- establecer el encabezado de seguridad mediante la clave del recurso Personalizador
- establecer la inicialización aleatoria para el identificador del evento Rank
- leer en los archivos de datos JSON
- llamar al método get_last_updated: se ha quitado la directiva de aprendizaje en la salida de ejemplo
- llamar al método get_service_settings

1. Para ello, agregue el código siguiente a una nueva celda y ejecútelo:

   ```python
   # build URLs
   personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
   personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
   personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
   personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
   personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

   headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

   # context
   users = "users.json"

   # action features
   coffee = "coffee.json"

   # empty JSON for Rank request
   requestpath = "example-rankrequest.json"

   # initialize random
   random.seed(time.time())

   userpref = None
   rankactionsjsonobj = None
   actionfeaturesobj = None

   with open(users) as handle:
       userpref = json.loads(handle.read())

   with open(coffee) as handle:
       actionfeaturesobj = json.loads(handle.read())

   with open(requestpath) as handle:
       rankactionsjsonobj = json.loads(handle.read())

   get_last_updated(modelLastModified)
   get_service_settings()

   print(f'User count {len(userpref)}')
   print(f'Coffee count {len(actionfeaturesobj)}')
   ```

1. Debería devolver una respuesta similar a la siguiente:

   ```bash
   -----checking model
   <Response [200]>
   {'creationTime': '2023-09-22T14:58:45+00:00', 'lastModifiedTime': '2023-09-22T14:58:45+00:00'}
   -----model updated: "2023-09-22T14:58:45+00:00"
   -----checking service settings
   <Response [200]>
   {'name': '917554355a3347a1af3d2935d521426a', 'arguments': '--cb_explore_adf --epsilon 0.20000000298023224 --power_t 0 -l 0.001 --cb_type mtr -q ::'}
   <Response [200]>
   {'rewardWaitTime': 'PT10M', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': 'PT15S', 'logRetentionDays': 90, 'lastConfigurationEditDate': '2021-01-01T00:00:00', 'learningMode': 'Online'}
   User count 4
   Coffee count 4
   ```

1. El código de respuesta debe ser `<Response [200]>` para indicar una llamada correcta. **rewardWaitTime** debe mostrarse como 10 minutos y **modelExportFrequency** debe ser de 15 segundos.

## Configuración de un gráfico en Azure Portal

El código realiza solicitudes a la API. Para obtener una buena métrica de las solicitudes, puede crear un gráfico de métricas en Azure Portal:

1. En Azure Portal, vaya al recurso de Personalizador de Azure AI.

1. Seleccione **Métricas** en Supervisión en el panel de navegación.

   ![Captura de pantalla del gráfico de métricas.](../media/ai-personalizer/8-create-metric-chart.png)

1. Los espacios de nombres **Ámbito** y **Métricas** ya están establecidos automáticamente. Solo tiene que seleccionar la **Métrica** de **Llamadas correctas** y la **Agregación** de **Suma**.

1. Cambie el filtro de tiempo a las últimas cuatro horas.

## Creación de identificadores de evento únicos

Después, agregará código para generar un identificador único para cada llamada API de Rank. Use este id. para identificar la información de la llamada Rank y Reward para las solicitudes.

1. Para ello, cree una nueva celda de código en el cuaderno y agregue lo siguiente:

   ```python
   def add_event_id(rankjsonobj):
       eventid = uuid.uuid4().hex
       rankjsonobj["eventId"] = eventid
       return eventid
   ```

1. No olvide ejecutar la nueva celda de código.

> [!NOTE]
> En un escenario real, establecería esto en algo parecido al identificador de transacción de una compra.

## Obtención del usuario, la hora del día y el tiempo

Ahora puede agregar una función para:

- Seleccionar una combinación de un usuario, hora del día y tiempo únicos.
- Agregar esos elementos seleccionados a un objeto JSON que se enviará a la API de Rank a través de una llamada.

Para ello, agregue el código siguiente a una nueva celda y ejecútelo:

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```

## Agregar datos de café

Después, creará una función para obtener toda la lista de productos de café en un objeto JSON que, después, enviará a la API de Rank.

Para ello, agregue el código siguiente a una nueva celda y ejecútelo:

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

## Comparación de las predicciones con la preferencia de usuario conocida

Después, puede crear una función para comparar la preferencia de un usuario por un café determinado, teniendo en cuenta los detalles, como el tiempo y la hora del día, con lo que sugiere Personalizador de Azure AI.

1. Para ello, cree una nueva celda, agregue el código siguiente y ejecútelo:

   ```python
   def get_reward_from_simulated_data(name, weather, timeofday, prediction):
       if(userpref[name][weather][timeofday] == str(prediction)):
           return 1
       return 0
   ```

1. Esta función está pensada para ejecutarse después de cada vez que se llama a la API de Rank. Si la sugerencia coincide, se devuelve una puntuación de `1` en la respuesta. Si no coincide, se devolverá `0`.

## Creación de un bucle con llamadas a las API de Rank y Reward

Las celdas anteriores se usan para configurar el cuaderno para el bucle. Ahora configurará el bucle. El bucle cubre el cuerpo principal del trabajo en el cuaderno. Obtiene un usuario aleatorio, obtiene la lista de café y los envía a la API de Rank. Compara la predicción de Personalizador de Azure AI con las preferencias conocidas de ese usuario y, después, devuelve la recompensa a Personalizador de Azure AI.

Para crear el bucle, agregue el código siguiente a una nueva celda y ejecútelo:

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)*10

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
        total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
            total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

Este es un ejemplo de la estructura JSON que la función envía a la API de Rank:

```python
{
    'contextFeatures':[
      {
          'timeofday':'Evening',
          'weather':'Snowy',
          'name':'Alice'
      }
    ],
    'actions':[
      {
          'id':'Cappucino',
          'features':[
            {
                'type':'hot',
                'origin':'kenya',
                'organic':'yes',
                'roast':'dark'
            }
          ]
      }
        ...rest of the coffee list
    ],
    'excludedActions':[

    ],
    'eventId':'b5c4ef3e8c434f358382b04be8963f62',
    'deferActivation':False
}
```

La API de Rank responderá con una respuesta estructurada de la siguiente manera:

```python
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Cada iteración de bucle mostrará el usuario, el tiempo y la hora del día seleccionados aleatoriamente, y la recompensa determinada correctamente:

```bash
1 Alice Rainy Morning Latte 1
```

Una recompensa de `1` significa que el recurso de Personalizador de Azure AI ha seleccionado el tipo de café correcto para esta combinación concreta de usuario, tiempo y hora del día.

## Ejecución del bucle y visualización de los resultados del gráfico

Personalizador de Azure AI necesita varias miles de llamadas a la API Rank y la API Reward para crear un modelo. Ejecutará el bucle para un número establecido de iteraciones.

1. Para ello, cree una nueva celda de código, agregue el código siguiente y ejecútelo:

   ```python
   # max iterations
   num_requests = 150

   # check last mod date N% of time - currently 10%
   lastModCheck = int(num_requests * .10)

   jsonTemplate = rankactionsjsonobj

   # main iterations
   [count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
   ```

1. Actualice el gráfico de métricas en Azure Portal cada vez para ver el total de llamadas al servicio.
1. Este evento se puede ejecutar durante un tiempo. No cierre el bloc de notas hasta que haya terminado. Cuando el bucle realiza alrededor de 20 000 llamadas (una llamada de Rank y Reward por cada iteración del bucle), el bucle finalizará.

1. Después, cree un gráfico en el cuaderno para trazar los lotes de eventos de Rank y cuántas recomendaciones correctas se realizaron por cada lote. Para ello, agregue el código siguiente en una nueva celda y ejecútelo:

   ```python
   def createChart(x, y):
       plt.plot(x, y)
       plt.xlabel("Batch of rank events")
       plt.ylabel("Correct recommendations per batch")
       plt.show()

   createChart(count,rewards)
   ```

1. El cuaderno creará un gráfico:

   ![Captura de pantalla del gráfico.](../media/ai-personalizer/7-chart2.png)

> **Sugerencia:** Idealmente, una vez finalizada la prueba, el bucle debe realizar recomendaciones correctas en promedio a una velocidad del 100 % menos el valor de exploración (que es el 20 % de forma predeterminada), por lo que el 80 % es la tasa objetivo aquí. Una manera de llegar a esto es aumentar las iteraciones a, al menos, 10 000.

En el gráfico, se muestra el éxito del modelo en función de la directiva de aprendizaje predeterminada. En este gráfico, se muestra que se puede mejorar la directiva de aprendizaje. Para ello, cambie la directiva después de ejecutar evaluaciones.

## Ejecución de una evaluación sin conexión

> [!NOTE]
> Esta sección de este ejercicio es opcional porque solo se puede realizar después de haber realizado al menos 50 000 llamadas al recurso Personalizador de Azure AI. Sin embargo, puede volver a esta sección si en algún momento ha alcanzado 50 000 llamadas en el recurso.

Puede ejecutar una evaluación sin conexión para encontrar una directiva de aprendizaje mejor para un recurso de Personalizador de Azure AI con al menos 50 000 llamadas.

1. En Azure Portal, vaya al panel Optimización del recurso de Personalizador de Azure AI y seleccione **Crear evaluación**.
1. Proporcione un nombre de evaluación y seleccione un intervalo de fechas de inicio y finalización para la evaluación del bucle. El intervalo de fechas debe incluir solo los días en los que se centra para la evaluación:

   ![Captura de pantalla que muestra el formulario de evaluación.](../media/ai-personalizer/7-evaluation-form.png)

1. Seleccione **Iniciar evaluación** para comenzar la evaluación.

1. Una vez completada la evaluación, selecciónela en la lista de evaluaciones en el panel Optimizar. Después, revise el rendimiento de las directivas de aprendizaje en función de los detalles, como su recompensa media, los intervalos de confianza, etc:

   ![Captura de pantalla que muestra los resultados de la evaluación.](../media/ai-personalizer/7-offline-eval-result.png)

1. Verá algunas directivas, entre las que se incluyen:

   - **En línea**: la directiva actual del Personalizador de Azure AI.
   - **Baseline1**: directiva de línea base de la aplicación
   - **BaselineRand**: una directiva de toma de medidas aleatoriamente.
   - **Inter-len#** o **Hyper#**: directivas creadas por la detección de optimización.

1. Seleccione **Aplicar** en la directiva que mejore el mejor modelo.

## Limpieza de recursos

Si no usas los recursos de Azure creados en este laboratorio para otros módulos de entrenamiento, puedes eliminarlos para evitar incurrir en cargos adicionales.

1. Abre Azure Portal en `https://portal.azure.com` y, en la barra de búsqueda superior, busca los recursos que creaste en este laboratorio.

2. En la página del recurso, selecciona **Eliminar** y sigue las instrucciones para eliminar el recurso. Una alternativa es eliminar todo el grupo de recursos para limpiar todos los recursos al mismo tiempo.
