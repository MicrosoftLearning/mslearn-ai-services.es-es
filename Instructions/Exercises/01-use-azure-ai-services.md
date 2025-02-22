---
lab:
  title: Introducción a Servicios de Azure AI
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Introducción a Servicios de Azure AI

En este ejercicio, podrás empezar a trabajar con Servicios de Azure AI creando un recurso de **Servicios de Azure AI** en la suscripción de Azure y usándolo desde una aplicación cliente. El objetivo del ejercicio no es adquirir experiencia en ningún servicio determinado, sino familiarizarte con un patrón general de aprovisionamiento y trabajo con servicios de Azure AI como desarrollador.

## Clonación del repositorio en Visual Studio Code

Desarrollará el código mediante Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: Si ya ha clonado el repositorio **mslearn-ai-services**, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-ai-services` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.
4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio, si es necesario

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

5. Expanda la carpeta `Labfiles/01-use-azure-ai-services`.

Se ha proporcionado código para C# y Python. Expanda la carpeta del lenguaje que prefiera.

## Aprovisionamiento de un recurso de Servicios de Azure AI

Los servicios de Azure AI son servicios basados en la nube con funcionalidades de inteligencia artificial que se pueden incorporar a las aplicaciones. Puedes aprovisionar recursos individuales de servicios de Azure AI para API específicas (por ejemplo, **Lenguaje** o **Visión**). También puedes aprovisionar un recurso único de **Servicios de Azure AI** que proporcione acceso a varias API de servicios de Azure AI mediante una clave y punto de conexión únicos. En este caso, usarás un único recurso de **Servicios de Azure AI**.

1. Inicie sesión en Azure Portal en `https://portal.azure.com` y regístrese con la cuenta de Microsoft asociada a su suscripción de Azure.
2. En la barra de búsqueda superior, busca *Servicios de Azure AI*, selecciona **Cuenta de varios servicios de Azure AI** y crea un recurso con la siguiente configuración:
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *elija o cree un grupo de recursos (si usa una suscripción restringida, es posible que no tenga permiso para crear un nuevo grupo de recursos; use el proporcionado)*
    - **Región**: *elija cualquier región disponible*
    - **Nombre**: *escriba un nombre único*
    - **Plan de tarifa**: estándar S0
3. Active las casillas necesarias y cree el recurso.
4. Espere a que se complete la implementación y, a continuación, consulte los detalles.
5. Vaya al recurso y consulte su página **Claves y punto de conexión**. Esta página contiene la información que necesitará para conectarse al recurso y usarlo desde las aplicaciones que desarrolle. Concretamente:
    - Un *punto de conexión* HTTP al que las aplicaciones cliente pueden enviar solicitudes.
    - Dos *claves* que se pueden usar para la autenticación (las aplicaciones cliente pueden usar cualquiera de las claves para autenticarse).
    - La *ubicación* en la que se hospeda el recurso. Esta información es necesaria para las solicitudes a algunas API (pero no a todas).

## Uso de la interfaz de REST

Las API de servicios de Azure AI se basan en REST, por lo que se pueden consumir mediante el envío de solicitudes JSON a través de HTTP. En este ejemplo, explorarás una aplicación de consola que usa la API de REST **Lenguaje** para realizar la detección de lenguaje, pero el principio básico es el mismo para todas las API que admite el recurso de Servicios de Azure AI.

> **Nota**: En este ejercicio, puede elegir usar la API de REST de **C#** o **Python**. En los pasos siguientes, realice las acciones adecuadas para su lenguaje preferido.

1. En Visual Studio Code, expanda la carpeta **C-Sharp** o **Python**, según sus preferencias de lenguaje.
2. Consulte el contenido de la carpeta **rest-client** y fíjese en que contiene un archivo para los valores de configuración:

    - **C#** : appsettings.json
    - **Python**: .env

    Abra el archivo de configuración y actualice los valores de configuración que contiene para reflejar el **punto de conexión** y una **clave** de autenticación para el recurso de Servicios de Azure AI. Guarde los cambios.

3. Tenga en cuenta que la carpeta **rest-client** contiene un archivo de código para la aplicación cliente:

    - **C#** : Program.cs
    - **Python**: rest-client.py

    Abra el archivo de código y revise el código que contiene, fijándose en los siguientes detalles:
    - Se importan varios espacios de nombres para habilitar la comunicación HTTP.
    - El código de la función **Principal** recupera el punto de conexión y la clave del recurso de servicios de Azure AI, que se usarán para enviar solicitudes de REST al servicio Text Analytics.
    - El programa acepta la entrada del usuario y usa la función **GetLanguage** para llamar a la API de REST de detección de idioma de Text Analytics para que el punto de conexión de los servicios de Azure AI detecte el idioma del texto introducido.
    - La solicitud enviada a la API consta de un objeto JSON que contiene los datos de entrada; en este caso, una colección de objetos de **documentos**, cada uno de los cuales tiene un **id.** y **texto**.
    - La clave del servicio se incluye en el encabezado de solicitud para autenticar la aplicación cliente.
    - La respuesta del servicio es un objeto JSON que la aplicación cliente puede analizar.

4. Haga clic con el botón derecho en la carpeta **rest-client**, seleccione *Abrir en terminal integrado* y ejecute el siguiente comando:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    pip install python-dotenv
    python rest-client.py
    ```

5. Cuando se le solicite, escriba algún texto y revise el idioma que detecta el servicio, que se devuelve en la respuesta JSON. Por ejemplo, pruebe a escribir "Hello", "Bonjour" y "Gracias".
6. Cuando haya terminado de probar la aplicación, escriba "quit" (salir) para detener el programa.

## Uso de un SDK

Puede escribir código que consuma las API de REST de servicios de Azure AI directamente, pero hay kits de desarrollo de software (SDK) para muchos lenguajes de programación populares, incluidos Microsoft C#, Python, Java y Node.js. El uso de un SDK puede simplificar considerablemente el desarrollo de aplicaciones que consuman servicios de Azure AI.

1. En Visual Studio Code, expanda la carpeta **sdk-client** bajo la carpeta **C-Sharp** o **Python**, según sus preferencias de lenguaje. A continuación, ejecute `cd ../sdk-client` para cambiar a la carpeta **sdk-client** pertinente.

2. Instala el paquete del SDK de Text Analytics ejecutando el comando adecuado para tus preferencias de lenguaje:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    ```

3. Consulte el contenido de la carpeta **sdk-client** y fíjese en que contiene un archivo para los valores de configuración:

    - **C#** : appsettings.json
    - **Python**: .env

    Abra el archivo de configuración y actualice los valores de configuración que contiene para reflejar el **punto de conexión** y una **clave** de autenticación para el recurso de Servicios de Azure AI. Guarde los cambios.
    
4. Tenga en cuenta que la carpeta **sdk-client** contiene un archivo de código para la aplicación cliente:

    - **C#** : Program.cs
    - **Python**: sdk-client.py

    Abra el archivo de código y revise el código que contiene, fijándose en los siguientes detalles:
    - Se importa el espacio de nombres del SDK que instaló.
    - El código de la función **Principal** recupera el punto de conexión y la clave del recurso de servicios de Azure AI que se usarán con el SDK a fin de crear un cliente para el servicio Text Analytics.
    - La función **GetLanguage** usa el SDK para crear un cliente para el servicio y, a continuación, usa el cliente para detectar el idioma del texto introducido.

5. Vuelva al terminal, asegúrese de que está en la carpeta **sdk-client** y escriba el siguiente comando para ejecutar el programa:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python sdk-client.py
    ```

6. Cuando se le solicite, escriba algún texto y revise el idioma que detecta el servicio. Por ejemplo, pruebe a escribir "Adiós", "Au revoir" y "Hasta la vista".
7. Cuando haya terminado de probar la aplicación, escriba "quit" (salir) para detener el programa.

> **Nota**: Es posible que algunos idiomas que requieren juegos de caracteres Unicode no se reconozcan en esta aplicación de consola sencilla.

## Limpieza de recursos

Si no usas los recursos de Azure creados en este laboratorio para otros módulos de entrenamiento, puedes eliminarlos para evitar incurrir en cargos adicionales.

1. Abre Azure Portal en `https://portal.azure.com` y, en la barra de búsqueda superior, busca los recursos que creaste en este laboratorio.

2. En la página del recurso, selecciona **Eliminar** y sigue las instrucciones para eliminar el recurso. Una alternativa es eliminar todo el grupo de recursos para limpiar todos los recursos al mismo tiempo.

## Más información

Para obtener más información sobre los Servicios de Azure AI, consulte la [documentación de Servicios de Azure AI](https://docs.microsoft.com/azure/ai-services/what-are-ai-services).
