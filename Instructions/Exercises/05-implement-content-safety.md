---
lab:
  title: Implementación de la Seguridad del contenido de Azure AI
---

# Implementación de la Seguridad del contenido de Azure AI

En este ejercicio, aprovisionarás un recurso de seguridad del contenido, probarás el recurso en Azure AI Studio y probarás el recurso en el código.

## Aprovisionamiento de un recurso de *seguridad del contenido*

Si aún no tienes uno, deberás aprovisionar un recurso de **seguridad del contenido** en tu suscripción a Azure.

1. Inicie sesión en Azure Portal en `https://portal.azure.com` y regístrese con la cuenta de Microsoft asociada a su suscripción de Azure.
1. Seleccione **Crear un recurso**.
1. En el campo de búsqueda, busca **Seguridad del contenido**. Luego, en los resultados selecciona **Crear** en el recurso **Servicio de lenguaje**.
1. Aprovisione el recurso mediante la siguiente configuración:
    - **Suscripción**: *su suscripción a Azure*.
    - **Grupo de recursos**: *seleccione o cree un grupo de recursos*.
    - **Región**: selecciona **Este de EE. UU.**
    - **Nombre**: *escriba un nombre único*.
    - **Plan de tarifa**: selecciona **F0** (*gratis*), o **S** (*estándar*) si F0 no está disponible.
1. Seleccione **Revisar y crear** y **Crear** para aprovisionar el recurso.
1. Espere a que se complete la implementación y, a continuación, ve al recurso.
1. Selecciona **Control de acceso** en el panel de navegación izquierdo y, después, selecciona **+ Agregar** --**Agregar asignación de rol**.
1. Desplázate hacia abajo para elegir el rol **Usuario de Cognitive Services** y selecciona **Siguiente**.
1. Agrega tu cuenta a este rol y luego selecciona **Revisar + asignar**.
1. Selecciona **Administración de recursos** en la barra de navegación de la izquierda y selecciona **Claves y punto de conexión**. Deja abierta esta página para poder copiar las claves más adelante.

## Uso de los escudos de avisos de Seguridad del contenido de Azure AI

En este ejercicio, usarás Azure AI Studio para probar los escudos de avisos de seguridad del contenido con dos entradas de muestra. Uno simula un mensaje de usuario y el otro simula un documento con texto potencialmente no seguro insertado en él.

1. En otra pestaña del explorador, abre la página Seguridad del contenido de [Inteligencia artificial de Azure Studio](https://ai.azure.com/explore/contentsafety) e inicia sesión.
1. En **Moderar el contenido del texto** selecciona **Pruébalo**.
1. En la página **Moderar el contenido del texto**, en **Servicios de Azure AI** selecciona el recurso Seguridad del contenido que has creado anteriormente.
1. Selecciona **Varias categorías de riesgo en una oración**. Revisa el texto del documento para ver posibles problemas.
1. Selecciona **Ejecutar prueba** y revisa los resultados.
1. Opcionalmente, modifica los niveles de umbral y selecciona **Ejecutar prueba** de nuevo.
1. En la barra de navegación izquierda, selecciona **Detección de materiales protegidos para el texto**.
1. Selecciona **Letras protegidas** y ten en cuenta que estas son las letras de una canción publicada.
1. Selecciona **Ejecutar prueba** y revisa los resultados.
1. En la barra de navegación izquierda, selecciona **Moderar contenido de imagen**.
1. Selecciona **Contenido de autolesiones**.
1. Ten en cuenta que todas las imágenes se difuminan de forma predeterminada en AI Studio. También debes tener en cuenta que el contenido sexual de las muestras es muy suave.
1. Selecciona **Ejecutar prueba** y revisa los resultados.
1. En la barra de navegación de la izquierda, selecciona **Escudos de avisos**.
1. En la página **Escudos de avisos**, en **Azure AI Services** selecciona el recurso Seguridad del contenido que has creado anteriormente.
1. Selecciona **Ataques por inyección de mensajes y de documentos**. Revisa el mensaje de usuario y el texto del documento para ver posibles problemas.
1. Haga clic en **Ejecutar prueba**.
1. En **Ver resultados**, comprueba que se han detectado ataques de evasión de seguridad tanto en el mensaje del usuario como en el documento.

    > [!TIP]
    > El código está disponible para todos los ejemplos de AI Studio.

1. En **Pasos siguientes**, en **Ver el código**, selecciona **Ver código**. Se muestra la ventana **código de ejemplo**.
1. Usa la flecha hacia abajo para seleccionar Python o C# y luego selecciona **Copiar** para copiar el código de ejemplo en el portapapeles.
1. Cierra la pantalla **Código de ejemplo**.

### Configuración de la aplicación

Ahora crearás una aplicación en C# o Python.

#### C#

##### Requisitos previos

* [Visual Studio Code](https://code.visualstudio.com/) en una de las [plataformas admitidas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
* [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) es la plataforma de destino de este ejercicio.
* La [extensión de C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) para Visual Studio Code.

##### Instalación

Realiza los pasos siguientes para preparar Visual Studio Code para el ejercicio.

1. Inicia Visual Studio Code y en la vista Explorador, haz clic en **Crear Proyecto de .NET** y selecciona **Aplicación de consola**.
1. Selecciona una carpeta del equipo y asigna un nombre al proyecto. Selecciona **Crear proyecto** y confirma el mensaje de advertencia.
1. En el panel Explorador, expande Explorador de soluciones y selecciona **Program.cs**.
1. Compila y ejecuta el proyecto al seleccionar **Ejecutar** -> **Ejecutar sin depurar**. 
1. En el Explorador de soluciones, haz clic con el botón derecho en el proyecto C# y selecciona **Agregar paquete NuGet.**
1. Busca **Azure.AI.TextAnalytics** y selecciona la versión más reciente.
1. Busca un segundo paquete NuGet: **Microsoft.Extensions.Configuration.Json 8.0.0**. El archivo de proyecto ahora debe enumerar dos paquetes NuGet.

##### Incorporación de código

1. Pega el código de ejemplo que has copiado anteriormente en la sección **ItemGroup**.
1. Desplázate hacia abajo para buscar *Reemplazar por tu propia subscription _key y punto de conexión*.
1. En Azure Portal, en la página Claves y punto de conexión, copia una de las claves (1 o 2). Reemplaza **<your_subscription_key>** por este valor.
1. En Azure Portal, en la página Claves y punto de conexión, copia el punto de conexión. Pega este valor en el código para reemplazar **<your_endpoint_value>**.
1. En **Azure AI Studio**, copia el valor **Mensaje de usuario**. Pégalo en el código para reemplazar **<test_user_prompt>**.
1. Desplázate hacia abajo hasta **<this_is_a_document_source>** y elimina esta línea de código.
1. En **Azure AI Studio**, copia el valor **Documento**.
1. Desplázate hacia abajo hasta **<this_is_another_document_source>** y pega el valor de documento.
1. Selecciona **Ejecutar** -> **Ejecutar sin depurar** y comprueba que se ha detectado un ataque. 

#### Python

##### Requisitos previos

* [Visual Studio Code](https://code.visualstudio.com/) en una de las [plataformas admitidas](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* La extensión [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) está instalada para Visual Studio Code.

* El [módulo de solicitudes](https://pypi.org/project/requests/) está instalado.

1. Crea un nuevo archivo de Python con una extensión **.py** y asígnale un nombre adecuado.
1. Pega el código de ejemplo que has copiado anteriormente.
1. Desplázate hacia abajo para encontrar la sección titulada *Reemplazar por tu propia subscription _key y punto de conexión*.
1. En Azure Portal, en la página Claves y punto de conexión, copia una de las claves (1 o 2). Reemplaza **<your_subscription_key>** por este valor.
1. En Azure Portal, en la página Claves y punto de conexión, copia el punto de conexión. Pega este valor en el código para reemplazar **<your_endpoint_value>**.
1. En **Azure AI Studio**, copia el valor **Mensaje de usuario**. Pégalo en el código para reemplazar **<test_user_prompt>**.
1. Desplázate hacia abajo hasta **<this_is_a_document_source>** y elimina esta línea de código.
1. En **Azure AI Studio**, copia el valor **Documento**.
1. Desplázate hacia abajo hasta **<this_is_another_document_source>** y pega el valor de documento.
1. Desde el terminal integrado para tu archivo, ejecuta el programa, por ejemplo:

    - `.\prompt-shield.py`

1. Valida que se detecta un ataque.
1. Opcionalmente, puedes experimentar con diferentes valores de contenido y documento de prueba.
