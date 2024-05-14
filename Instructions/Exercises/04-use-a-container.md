---
lab:
  title: "Uso de un contenedor de Servicios de Azure\_AI"
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Uso de un contenedor de Servicios de Azure AI

El uso de servicios de Azure AI hospedados en Azure permite a los desarrolladores de aplicaciones centrarse en la infraestructura de su propio código, a la vez que se benefician de los servicios escalables administrados por Microsoft. Sin embargo, en muchos escenarios, las organizaciones requieren más control sobre su infraestructura de servicio y los datos que se pasan entre los servicios.

Muchas de las API de servicios de Azure AI se pueden empaquetar e implementar en un *contenedor*, lo que permite que las organizaciones hospeden servicios de Azure AI en su propia infraestructura; por ejemplo, en servidores Docker locales, Azure Container Instances o clústeres de Azure Kubernetes Services. Los servicios de Azure AI en contenedores deben comunicarse con una cuenta de servicios de Azure AI basados en Azure para admitir la facturación; pero los datos de la aplicación no se pasan al servicio back-end y las organizaciones tienen un mayor control sobre la configuración de implementación de sus contenedores, lo que permite soluciones personalizadas para la autenticación, la escalabilidad y otras consideraciones.

> **Nota**: Actualmente se está investigando un problema con el que se encuentran algunos usuarios por el que los contenedores no se implementan correctamente y las llamadas a esos contenedores producen errores. En cuanto se haya resuelto el problema, se realizarán actualizaciones en este laboratorio.

## Clonación del repositorio en Visual Studio Code

Desarrollará el código mediante Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: Si ya ha clonado el repositorio **mslearn-ai-services**, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-ai-services` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.
4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio, si es necesario

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

5. Expanda la carpeta `Labfiles/04-use-a-container`.

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
5. Cuando se haya implementado el recurso, vaya a él y vea su página **Claves y punto de conexión**. Necesitará el punto de conexión y una de las claves de esta página en el procedimiento siguiente.

## Implementación y ejecución de un contenedor de Text Analytics

Muchas API de servicios de Azure AI de uso frecuente están disponibles en imágenes de contenedor. Para obtener una lista completa, consulta la [documentación de servicios de Azure AI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#container-availability-in-azure-cognitive-services). En este ejercicio, usará la imagen de contenedor para la API de *detección de idioma* de Text Analytics, pero los principios son los mismos para todas las imágenes disponibles.

1. En Azure Portal, en la página **Inicio**, seleccione el botón **+Crear un recurso**, busque *Container Instances* y cree un recurso de **Container Instances** con la siguiente configuración:

    - **Aspectos básicos**:
        - **Suscripción**: *suscripción de Azure*
        - **Grupo de recursos**: *elige el grupo de recursos que contiene el recurso de servicios de Azure AI*
        - **Nombre de contenedor**: *escriba un nombre único*.
        - **Región**: *elija cualquier región disponible*
        - **Origen de imagen:** Otro registro
        - **Tipo de imagen**: pública
        - **Imagen**: `mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest`
        - **Tipo de SO**: Linux
        - **Tamaño**: 1 vcpu, 12 GB de memoria
    - **Redes**:
        - **Tipo de red**: pública
        - **Etiqueta de nombre DNS**: *escriba un nombre único para el punto de conexión del contenedor*
        - **Puertos**: *cambie el puerto TCP 80 a **5000***
    - **Avanzado**:
        - **Directiva de reinicio**: en caso de error
        - **Variables de entorno**:

            | Marcar como seguro | Clave | Value |
            | -------------- | --- | ----- |
            | Sí | `ApiKey` | *Cualquier clave del recurso de servicios de Azure AI* |
            | Sí | `Billing` | *Obtención del URI de punto de conexión para el recurso de servicios de Azure AI* |
            | No | `Eula` | `accept` |

        - **Invalidación de comando**: [ ]
    - **Etiquetas**:
        - *No agregue ninguna etiqueta*

2. Seleccione **Revisar y crear** y, luego, **Crear**. Espere a que se complete la implementación y, a continuación, vaya al recurso implementado.
    > **Nota**: ten en cuenta que la implementación de un contenedor de Azure AI en Azure Container Instances suele tardar entre 5 y 10 minutos (aprovisionamiento) antes de que estén listos para usarse.
3. Observe las siguientes propiedades del recurso de instancia de contenedor en su página **Información general**:
    - **Estado**: debe ser *En ejecución*.
    - **Dirección IP**: se trata de la dirección IP pública que puede usar para acceder a las instancias de contenedor.
    - **FQDN**: se trata del *nombre de dominio completo* del recurso de instancias de contenedor; puede usarlo para acceder a las instancias de contenedor en lugar de la dirección IP.

    > **Nota**: en este ejercicio, has implementado la imagen de contenedor de servicios de Azure AI para la traducción de texto en un recurso de Azure Container Instances (ACI). Puedes usar un enfoque similar para implementarlo en un host de *[Docker](https://www.docker.com/products/docker-desktop)* en tu propio equipo o red mediante la ejecución del siguiente comando (en una sola línea) para implementar el contenedor de detección de idioma en la instancia local de Docker, reemplazando *&lt;yourEndpoint&gt;* y *&lt;yourKey&gt;* por el URI del punto de conexión y cualquiera de las claves del recurso de servicios de Azure AI.
    > El comando buscará la imagen en el equipo local y, si no la encuentra, la extraerá del registro de imágenes *mcr.microsoft.com* y la implementará en la instancia de Docker. Una vez completada la implementación, el contenedor se iniciará y escuchará las solicitudes entrantes en el puerto 5000.

    ```
    docker run --rm -it -p 5000:5000 --memory 12g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    ```

## Uso del contenedor

1. En el editor, abra **rest-test.cmd** y edite el comando **curl** que contiene (que se muestra a continuación), y reemplace *&lt;your_ACI_IP_address_or_FQDN&gt;* por la dirección IP o el FQDN del contenedor.

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.0/languages" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'Hello world.'},{'id':2,'text':'Salut tout le monde.'}]}"
    ```

2. Presione **CTRL+S** para guardar los cambios en el script. Ten en cuenta que no es necesario especificar el punto de conexión o la clave de servicios de Azure AI, pues el servicio de contenedor procesa la solicitud. A su vez, el contenedor se comunica periódicamente con el servicio en Azure para notificar el uso para la facturación, pero no envía datos de solicitud.
3. Escriba el siguiente comando para ejecutar el script:

    ```
    ./rest-test.cmd
    ```

4. Compruebe que el comando devuelve un documento JSON que contiene información sobre el idioma detectado en los dos documentos de entrada (que debe ser inglés y francés).

## Limpieza

Si ha terminado de experimentar con la instancia de contenedor, debe eliminarla.

1. En Azure Portal, abra el grupo de recursos donde creó los recursos para este ejercicio.
2. Seleccione el recurso de instancia de contenedor y elimínelo.

## Limpieza de recursos

Si no usas los recursos de Azure creados en este laboratorio para otros módulos de entrenamiento, puedes eliminarlos para evitar incurrir en cargos adicionales.

1. Abre Azure Portal en `https://portal.azure.com` y, en la barra de búsqueda superior, busca los recursos que creaste en este laboratorio.

2. En la página del recurso, selecciona **Eliminar** y sigue las instrucciones para eliminar el recurso. Una alternativa es eliminar todo el grupo de recursos para limpiar todos los recursos al mismo tiempo.

## Más información

Para obtener más información sobre cómo incluir en contenedores servicios de Azure AI, consulta la [documentación de contendores Servicios de Azure AI](https://learn.microsoft.com/azure/ai-services/cognitive-services-container-support).
