---
lab:
  title: Administración de la seguridad de Servicios de Azure AI
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Administración de la seguridad de Servicios de Azure AI

La seguridad es una consideración crítica para cualquier aplicación y, como desarrollador, debes asegurarte de que el acceso a recursos como los servicios de Azure AI está restringido solo a aquellos usuarios que lo requieren.

El acceso a los servicios de Azure AI se suele controlar mediante claves de autenticación, que se generan al crear inicialmente un recurso de servicios de Azure AI.

## Clonación del repositorio en Visual Studio Code

Desarrollará el código mediante Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: si ya ha clonado el repositorio **mslearn-ai-services** recientemente, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-ai-services` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.
4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio, si es necesario

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

5. Expanda la carpeta `Labfiles/02-ai-services-security`.

Se ha proporcionado código para C# y Python. Expanda la carpeta del lenguaje que prefiera.

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
4. Espere a que se complete la implementación y, a continuación, vea los detalles de la implementación.

## Administrar las claves de autenticación

Al crear el recurso de servicios de Azure AI, se han generado dos claves de autenticación. Puede administrarlas en Azure Portal o mediante la interfaz de la línea de comandos (CLI) de Azure. 

1. Elige un método para obtener las claves de autenticación y el punto de conexión: 

    **Mediante Azure Portal**: en Azure Portal, ve al recurso de Servicios de Azure AI y consulta su página **Claves y punto de conexión**. Esta página contiene la información que necesitará para conectarse al recurso y usarlo desde las aplicaciones que desarrolle. Concretamente:
    - Un *punto de conexión* HTTP al que las aplicaciones cliente puedan enviar solicitudes.
    - Dos *claves* que se puedan usar para la autenticación (las aplicaciones cliente pueden usar cualquiera de las claves. Una práctica común es usar una para el desarrollo y la otra para producción. Puede volver a generar fácilmente la clave de desarrollo después de que los desarrolladores terminen su trabajo para evitar el acceso continuado).
    - La *ubicación* en la que se hospeda el recurso. Esta información es necesaria para las solicitudes a algunas API (pero no a todas).

    **Mediante la línea de comandos**: también puedes usar el siguiente comando para obtener la lista de claves de Servicios de Azure AI. En Visual Studio Code, abre un nuevo terminal. Después, pega el comando siguiente; reemplazando *&lt;resourceName&gt;* por el nombre del recurso de servicios de Azure AI y *&lt;resourceGroup&gt;* por el nombre del grupo de recursos en el que lo creaste.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    El comando devuelve una lista de las claves del recurso de servicios de Azure AI: hay dos claves, denominadas **key1** y **key2**.

    > **Sugerencia**: si aún no has autenticado la CLI de Azure, primero ejecuta `az login` e inicia sesión en tu cuenta.

2. Para probar el servicio de Azure AI, puedes usar **curl**: una herramienta de línea de comandos para las solicitudes HTTP. En la carpeta **02-ai-services-security**, abra **rest-test.cmd** y edite el comando **curl** que contiene (que se muestra a continuación); reemplace *&lt;yourEndpoint&gt;* y *&lt;yourKey&gt;* por el URI del punto de conexión y la clave **Key1** para usar la API de Analyze Text en el recurso de servicios de Azure AI.

    ```bash
    curl -X POST "<yourEndpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <your-key>" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

3. Guarde los cambios. En el terminal, navega a la carpeta "02-ai-services-security". (**Nota**: para ello, haz clic con el botón derecho en la carpeta 02-ai-services-security" del explorador y selecciona *Abrir en terminal integrado*). Luego, ejecute el siguiente comando:

    ```
    ./rest-test.cmd
    ```

El comando devuelve un documento JSON que contiene información sobre el idioma detectado en los datos de entrada (que debe ser inglés).

4. Si una clave se ve comprometida o los desarrolladores que la tienen ya no requieren acceso, puede volver a generarla en el portal o mediante la CLI de Azure. Ejecute el siguiente comando para volver a generar la clave **key1** (reemplace los valores de *&lt;resourceName&gt;* y *&lt;resourceGroup&gt;* de su recurso).

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

Se devuelve la lista de claves del recurso de servicios de Azure AI: observa que **key1** ha cambiado desde la última vez que recuperaste las claves.

5. Vuelva a ejecutar el comando **rest-test** con la clave antigua (puede usar la flecha **^** del teclado para recorrer los comandos anteriores) y verifique que ahora se produce un error.
6. Edite el comando *curl* en **rest-test.cmd**; reemplace la clave por el nuevo valor de **key1** y guarde los cambios. A continuación, vuelva a ejecutar el comando **rest-test** y compruebe que se ejecuta correctamente.

> **Sugerencia**: En este ejercicio, ha usado los nombres completos de los parámetros de la CLI de Azure, como **--resource-group**.  También puede usar alternativas más cortas, como **-g**, para que los comandos sean menos detallados (pero un poco más difíciles de entender).  En la [referencia de comandos de la CLI de servicios de Azure AI](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest) se enumeran las opciones de parámetros para cada comando de la CLI de servicios de Azure AI.

## Proteger el acceso a las claves con Azure Key Vault

Puedes desarrollar aplicaciones que consuman servicios de Azure AI mediante una clave para la autenticación. Sin embargo, esto significa que el código de la aplicación debe poder obtener la clave. Una opción es almacenar la clave en una variable de entorno o en un archivo de configuración donde se implemente la aplicación, pero este enfoque hace que la clave sea vulnerable al acceso no autorizado. Un enfoque mejor al desarrollar aplicaciones en Azure es almacenar la clave de forma segura en Azure Key Vault y proporcionar acceso a la clave a través de una *identidad administrada* (es decir, una cuenta de usuario usada por la propia aplicación).

### Creación de un almacén de claves y adición de un secreto

En primer lugar, debes crear un almacén de claves y agregar un *secreto* para la clave de servicios de Azure AI.

1. Anota el valor de **key1** del recurso de servicios de Azure AI (o cópialo en el Portapapeles).
2. En la Azure Portal, en la página **Inicio**, seleccione el botón **&#65291;Crear un recurso**, busque *Key Vault* y cree un recurso de **Key Vault** con la siguiente configuración:

    - Pestaña **Aspectos básicos**
        - **Suscripción**: *suscripción de Azure*
        - **Grupo de recursos**: *el mismo grupo de recursos que el recurso de Servicios de Azure AI*
        - **Nombre del almacén de claves**: *escriba un nombre único*.
        - **Región**: *la misma región que el recurso de servicios de Azure AI*
        - **Plan de tarifa**: estándar
    
    - Pestaña **Configuración de acceso**
        -  **Modelo de permisos**: directiva de acceso del almacén
        - Desplázate hacia abajo hasta la sección **Directivas de acceso** y selecciona el usuario con la casilla de la izquierda. A continuación, seleccione **Revisar y crear** y seleccione **Crear** para crear el recurso.
     
3. Espere a que se complete la implementación y, a continuación, vaya al recurso del almacén de claves.
4. En el panel de navegación izquierdo, seleccione **Secretos** (en la sección "Objetos").
5. Seleccione **+ Generate/Import** (+ Generar/Importar) y agregue un nuevo secreto con la siguiente configuración:
    - **Opciones de carga**: manual
    - **Nombre**: AI-Services-Key *(es importante que coincida exactamente, porque más adelante ejecutará código que recuperará el secreto basado en este nombre)*
    - **Valor secreto**: *tu clave de servicios de Azure AI **key1***
6. Seleccione **Crear**.

### Creación de una entidad de servicio

Para acceder al secreto del almacén de claves, la aplicación debe usar una entidad de servicio que tenga acceso al secreto. Usará la interfaz de la línea de comandos (CLI) de Azure para crear la entidad de servicio, encontrar su id. de objeto y conceder acceso al secreto en Azure Key Vault.

1. Ejecute el siguiente comando de la CLI de Azure, reemplazando *&lt;spName&gt;* por un nombre único adecuado para una identidad de aplicación (por ejemplo, *ai-app* con las iniciales anexadas al final; el nombre debe ser único dentro del inquilino). Reemplaza también *&lt;subscriptionId&gt;* y *&lt;resourceGroup&gt;* por los valores correctos para el id. de suscripción y el grupo de recursos que contiene los recursos de servicios de Azure AI y del almacén de claves:

    > **Sugerencia**: Si no está seguro del id. de suscripción, use el comando **az account show** para recuperar la información de la suscripción (el id. de suscripción es el atributo **id** de la salida). Si ves un error sobre el objeto que ya existe, elige otro nombre único.

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

La salida de este comando incluye información sobre la nueva entidad de servicio. Debe tener un aspecto similar a este:

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "api://ai-app-",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

Tome nota de los valores de **appId**,**password** y **tenant**: los necesitará más adelante (si cierra este terminal, no podrá recuperar la contraseña, por lo que es importante anotar los valores ahora; puede pegar la salida en un nuevo archivo de texto en el equipo local para asegurarse de poder encontrar los valores que necesita más adelante)

2. Para obtener el **id. de objeto** de la entidad de servicio, ejecute el siguiente comando de la CLI de Azure; reemplace *&lt;appId&gt;* por el valor del id. de aplicación de la entidad de servicio.

    ```
    az ad sp show --id <appId>
    ```

3. Copie el valor `id` en el json devuelto en respuesta. 
3. Para asignar permiso a fin de que la nueva entidad de servicio acceda a los secretos en Key Vault, ejecute el siguiente comando de la CLI de Azure; reemplace *&lt;keyVaultName&gt;* por el nombre del recurso de Azure Key Vault y *&lt;objectId&gt;* por el valor del valor de identificador de la entidad de servicio que acaba de copiar.

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### Uso de la entidad de servicio en una aplicación

Ahora ya puedes usar la identidad de la entidad de servicio en una aplicación, de modo que esta pueda acceder a la clave secreta de servicios de Azure AI en el almacén de claves y usarla para conectarte al recurso de servicios de Azure AI.

> **Nota**: En este ejercicio, almacenaremos las credenciales de la entidad de servicio en la configuración de la aplicación y las usaremos para autenticar una identidad **ClientSecretCredential** en el código de la aplicación. Esto está bien para desarrollo y pruebas, pero, en una aplicación de producción real, un administrador asignaría una *identidad administrada* a la aplicación para que use la identidad de la entidad de servicio para acceder a los recursos, sin almacenar en caché ni guardar la contraseña.

1. En el terminal, cambie a la carpeta **C-Sharp** o **Python** en función de sus preferencias de lenguaje ejecutando `cd C-Sharp` o `cd Python`. A continuación, ejecute `cd keyvault_client` para ir a la carpeta de la aplicación.
2. Instale los paquetes que necesitará usar para Azure Key Vault y la API de Text Analytics en el recurso de servicios de Azure AI mediante la ejecución del comando adecuado para sus preferencias de lenguaje:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    dotnet add package Azure.Identity --version 1.12.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.6.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    pip install azure-identity==1.17.1
    pip install azure-keyvault-secrets==4.8.0
    ```

3. Consulte el contenido de la carpeta **keyvault-client** y fíjese en que contiene un archivo para los valores de configuración:
    - **C#** : appsettings.json
    - **Python**: .env

    Abra el archivo de configuración y actualice los valores de configuración que contiene para reflejar los valores siguientes:
    
    - El **punto de conexión** del recurso de Servicios de Azure AI.
    - El nombre del recurso de **Azure Key Vault**
    - El **inquilino** de la entidad de servicio
    - El **appId** de la entidad de servicio
    - La **contraseña** de la entidad de servicio

     Guarde los cambios presionando **CTRL+S**.
4. Observe que la carpeta **keyvault-client** contiene un archivo de código para la aplicación cliente:

    - **C#** : Program.cs
    - **Python**: keyvault-client.py

    Abra el archivo de código y revise el código que contiene, fijándose en los siguientes detalles:
    - Se importa el espacio de nombres del SDK que instaló.
    - El código de la función **Principal** recupera los valores de configuración de la aplicación y luego usa las credenciales de la entidad de servicio para obtener la clave de servicios de Azure AI del almacén de claves.
    - La función **GetLanguage** usa el SDK para crear un cliente para el servicio y, a continuación, usa el cliente para detectar el idioma del texto introducido.
5. Escriba el siguiente comando para ejecutar el programa:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. Cuando se le solicite, escriba algún texto y revise el idioma que detecta el servicio. Por ejemplo, pruebe a escribir "Hello", "Bonjour" y "Gracias".
7. Cuando haya terminado de probar la aplicación, escriba "quit" (salir) para detener el programa.

## Limpieza de recursos

Si no usas los recursos de Azure creados en este laboratorio para otros módulos de entrenamiento, puedes eliminarlos para evitar incurrir en cargos adicionales.

1. Abre Azure Portal en `https://portal.azure.com` y, en la barra de búsqueda superior, busca los recursos que creaste en este laboratorio.

2. En la página del recurso, selecciona **Eliminar** y sigue las instrucciones para eliminar el recurso. Una alternativa es eliminar todo el grupo de recursos para limpiar todos los recursos al mismo tiempo.

## Más información

Para obtener más información sobre cómo proteger servicios de Azure AI, consulta la [documentación de seguridad de Servicios de Azure AI](https://docs.microsoft.com/azure/ai-services/security-features).
