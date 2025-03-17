---
lab:
  title: Supervisión de los Servicios de inteligencia artificial de Azure
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Supervisión de los Servicios de inteligencia artificial de Azure

Los servicios de Azure AI pueden ser una parte fundamental de una infraestructura general de aplicaciones. Es importante poder supervisar la actividad y recibir alertas sobre problemas que pueden necesitar atención.

## Clonación del repositorio en Visual Studio Code

Desarrollará el código mediante Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: Si ya ha clonado el repositorio **mslearn-ai-services**, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-ai-services` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.
4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio, si es necesario

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

5. Expanda la carpeta `Labfiles/03-monitor-ai-services`.

## Aprovisionamiento de un recurso de Servicios de Azure AI

Si aún no tiene uno en su suscripción, deberá aprovisionar un recurso de **Servicios de Azure AI**.

1. Inicie sesión en Azure Portal en `https://portal.azure.com` y regístrese con la cuenta de Microsoft asociada a su suscripción de Azure.
2. En la barra de búsqueda superior, busque *Servicios de Azure AI*, seleccione **Servicios de Azure AI** y cree un recurso de cuenta de varios servicios de Azure AI con la siguiente configuración:
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *elija o cree un grupo de recursos (si usa una suscripción restringida, es posible que no tenga permiso para crear un nuevo grupo de recursos; use el proporcionado)*
    - **Región**: *elija cualquier región disponible*
    - **Nombre**: *escriba un nombre único*
    - **Plan de tarifa**: estándar S0
3. Active las casillas necesarias y cree el recurso.
4. Espere a que se complete la implementación y, a continuación, consulte los detalles.
5. Cuando se haya implementado el recurso, vaya a él y vea su página **Keys and Endpoint** (Claves y punto de conexión). Anote el URI del punto de conexión; lo necesitará más adelante.

## Configuración de una alerta

Vamos a empezar a supervisar definiendo una regla de alertas para que puedas detectar actividad en el recurso de servicios de Azure AI.

1. En Azure Portal, ve al recurso de servicios de Azure AI y consulta la página **Alertas** (en la sección **Supervisión**).
2. Seleccione la lista desplegable **+Crear** y elija **Regla de alertas**.
3. En la página **Crear una regla de alertas**, en **Ámbito**, comprueba que aparece el recurso de servicios de Azure AI. (Cierre el panel **Selección de una señal** si está abierto).
4. En la pestaña **Condición**, seleccione el vínculo **Ver todas las señales** para mostrar el panel **Selección de una señal** que aparece a la derecha, donde puede seleccionar un tipo de señal para supervisar.
5. En la lista **Tipo de señal**, desplácese hacia abajo hasta la sección **Registro de actividad** y seleccione **Enumerar claves (cuenta de la API de Cognitive Services)**. Luego, seleccione **Aplicar**.
6. Revise la actividad de las últimas 6 horas.
7. Seleccione la pestaña **Acciones**. Tenga en cuenta que puede especificar un *grupo de acciones*. Esto le permite configurar acciones automatizadas cuando se desencadena una alerta (por ejemplo, enviar una notificación por correo electrónico). No lo haremos en este ejercicio; pero puede ser útil en un entorno de producción.
8. En la pestaña **Detalles**, establezca el **nombre de la regla de alerta** en**Alerta de lista de claves**.
9. Seleccione **Revisar + crear**. 
10. Revise la configuración de la alerta. Seleccione **Crear** y espere a que se cree la regla de alerta.
11. Ahora puedes usar el comando siguiente para obtener la lista de claves de servicios de Azure AI, reemplazando *&lt;resourceName&gt;* por el nombre del recurso de servicios de Azure AI y *&lt; resourceGroup&gt;* por el nombre del grupo de recursos en el que lo creaste.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    El comando devuelve una lista de las claves del recurso de servicios de Azure AI.

    > **Nota:** Si no ha iniciado sesión en la CLI de Azure, es posible que tenga que ejecutar `az login` para que funcione el comando Enumerar claves.

12. Vuelva al explorador con Azure Portal y actualice la **página Alertas**. Debería ver una alerta de **Gravedad 4** en la tabla (si no es así, espere hasta cinco minutos y vuelva a actualizar).
13. Seleccione una alerta para ver sus detalles.

## Visualización de una métrica

Además de definir alertas, puedes ver las métricas del recurso de servicios de Azure AI para supervisar su uso.

1. En Azure Portal, en la página del recurso de servicios de Azure AI, selecciona **Métricas** (en la sección **Supervisión**).
2. Si no hay ningún gráfico existente, seleccione **+ Nuevo gráfico**. A continuación, en la lista **Métrica**, revise las métricas posibles que puede visualizar y seleccione **Llamadas totales**.
3. En la lista **Agregación**, seleccione **Recuento**.  Esto te permitirá supervisar el número total de llamadas al recurso de servicio de Azure AI, lo que resulta útil para determinar cuánto se utiliza el servicio durante un período de tiempo.
4. Para generar algunas solicitudes para tu servicio de Azure AI, usarás **curl**, una herramienta de línea de comandos para las solicitudes HTTP. En su editor, abra **rest-test.cmd** y edite el comando **curl** que contiene (el cual se muestra a continuación); reemplace *&lt;yourEndpoint&gt;* y *&lt;yourKey&gt;* por el URI del punto de conexión y la clave **Key1** para usar la API de Text Analytics en el recurso de servicios de Azure AI.

    ```
    curl -X POST "<your-endpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <your-key>" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

5. Guarde los cambios y ejecute el siguiente comando:

    ```
    ./rest-test.cmd
    ```

    El comando devuelve un documento JSON que contiene información sobre el idioma detectado en los datos de entrada (que debe ser inglés).

6. Vuelva a ejecutar el comando **rest-test** varias veces para generar alguna actividad de llamada (puede usar la clave **^** para recorrer en ciclos los comandos anteriores).
7. Vuelva a la página **Métricas** en Azure Portal y actualice el gráfico de recuento **Llamadas totales**. Las llamadas realizadas con *curl* pueden tardar unos minutos en reflejarse en el gráfico; siga actualizando el gráfico hasta que se actualice para incluirlas.

## Limpieza de recursos

Si no usas los recursos de Azure creados en este laboratorio para otros módulos de entrenamiento, puedes eliminarlos para evitar incurrir en cargos adicionales.

1. Abre Azure Portal en `https://portal.azure.com` y, en la barra de búsqueda superior, busca los recursos que creaste en este laboratorio.

2. En la página del recurso, selecciona **Eliminar** y sigue las instrucciones para eliminar el recurso. Una alternativa es eliminar todo el grupo de recursos para limpiar todos los recursos al mismo tiempo.

## Más información

Una de las opciones para supervisar servicios de Azure AI es usar el *registro de diagnóstico*. Una vez habilitado, el registro de diagnóstico captura abundante información sobre el uso de recursos de servicios de Azure AI y puede ser una herramienta útil de supervisión y depuración. Puedes tardar más de una hora en generar información tras configurar el registro de diagnóstico, por lo que no se ha explorado en este ejercicio; pero puedes obtener más información al respecto en la [documentación de Servicios de Azure AI](https://docs.microsoft.com/azure/ai-services/diagnostic-logging).
