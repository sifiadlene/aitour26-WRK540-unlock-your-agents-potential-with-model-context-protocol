## Asistentes a Eventos de Microsoft

Las instrucciones en esta página asumen que estás asistiendo a un evento y tienes acceso a un entorno de laboratorio preconfigurado. Este entorno proporciona una suscripción de Azure con todas las herramientas y recursos necesarios para completar el taller.

## Introducción

Este taller está diseñado para enseñarte sobre el Servicio de Agentes de Azure AI y el [SDK](https://learn.microsoft.com/python/api/overview/azure/ai-projects-readme?context=%2Fazure%2Fai-services%2Fagents%2Fcontext%2Fcontext){:target="_blank"} asociado. Consiste en múltiples laboratorios, cada uno destacando una característica específica del Servicio de Agentes de Azure AI. Los laboratorios están destinados a completarse en orden, ya que cada uno se basa en el conocimiento y trabajo del laboratorio anterior.

## Recursos de Nube del Taller

Los siguientes recursos están pre-provisionados en tu suscripción de Azure del laboratorio:

- Un grupo de recursos llamado **rg-zava-agent-wks-nnnnnnnn**
- Un **hub de Azure AI Foundry** llamado **fdy-zava-agent-wks-nnnnnnnn**
- Un **proyecto de Azure AI Foundry** llamado **prj-zava-agent-wks-nnnnnnnn**
- Dos modelos están desplegados: **gpt-4o-mini** y **text-embedding-3-small**. [Ver precios.](https://azure.microsoft.com/pricing/details/cognitive-services/openai-service/){:target="\_blank"}
- Base de datos de Azure Database for PostgreSQL Flexible Server (B1ms Burstable 32GB) llamada **pg-zava-agent-wks-nnnnnnnn**. [Ver precios](https://azure.microsoft.com/pricing/details/postgresql/flexible-server){:target="\_blank"}
- Recurso Application Insights llamado **appi-zava-agent-wks-nnnnnnnn**. [Ver precios](https://azure.microsoft.com/pricing/calculator/?service=monitor){:target="\_blank"}

## Seleccionar Lenguaje de Programación del Taller

El taller está disponible tanto en Python como en C#. Por favor asegúrate de seleccionar el lenguaje que se ajuste a la sala del laboratorio o preferencia usando las pestañas de selector de lenguaje. Nota, no cambies de lenguaje a mitad del taller.

**Selecciona la pestaña de lenguaje que coincida con tu sala de laboratorio:**

=== "Python"
    El lenguaje por defecto para el taller está establecido a **Python**.
=== "C#"
    El lenguaje por defecto para el taller está establecido a **C#**.

    !!! warning "La versión C#/.NET de este taller está en beta y tiene problemas de estabilidad conocidos."

    Asegúrate de leer la sección de [guía de solución de problemas](../../es/dotnet-troubleshooting.md) **ANTES** de comenzar el taller. De lo contrario, selecciona la versión de **Python** del taller.

## Autenticarse con Azure

Necesitas autenticarte con Azure para que la aplicación del agente pueda acceder al Servicio de Agentes de Azure AI y a los modelos. Sigue estos pasos:

1. Abre una ventana de terminal. La aplicación de terminal está **fijada** a la barra de tareas de Windows 11.

    ![Abrir la ventana de terminal](../../media/windows-taskbar.png){ width="300" }

2. Ejecuta el siguiente comando para autenticarte con Azure:

    ```powershell
    az login
    ```

    !!! note
        Se te pedirá que abras un enlace del navegador e inicies sesión en tu cuenta de Azure.

        1. Una ventana del navegador se abrirá automáticamente, selecciona **Work or school account** y luego selecciona **Continue**.
        1. Usa el **Username** y **TAP (Temporary Access Pass)** que se encuentran en la **sección superior** de la pestaña **Resources** en el entorno del laboratorio.
        1. Selecciona **Yes, all apps**
        1. Selecciona **Done**

3. Luego selecciona la suscripción **Default** desde la línea de comandos, seleccionando **Enter**.

4. Deja la ventana de terminal abierta para los siguientes pasos.

## Autenticarse con el Servicio DevTunnel

DevTunnel permite al Servicio de Agentes de Azure AI acceder a tu Servidor MCP local durante el taller.

```powershell
devtunnel login
```

!!! note
    Se te pedirá usar la cuenta que usaste para `az login`. Selecciona la cuenta y continúa.

Deja la ventana de terminal abierta para los siguientes pasos.

## Abrir el Taller

Sigue estos pasos para abrir el taller en Visual Studio Code:

=== "Python"

    El siguiente bloque de comandos actualiza el repositorio del taller, activa el entorno virtual de Python y abre el proyecto en VS Code.

    Copia y pega el siguiente bloque de comandos al terminal y presiona **Enter**.

    ```powershell
    ; cd $HOME\aitour26-WRK540-unlock-your-agents-potential-with-model-context-protocol `
    ; git pull `
    ; .\src\python\workshop\.venv\Scripts\activate `
    ; code .vscode\python-workspace.code-workspace
    ```

    !!! warning "Cuando el proyecto se abre en VS Code, aparecen dos notificaciones en la esquina inferior derecha. Haz clic en ✖ para cerrar ambas notificaciones."

=== "C#"

    === "VS Code"

        1. Abre el taller en Visual Studio Code. Desde la ventana de terminal, ejecuta el siguiente comando:

            ```powershell
            ; cd $HOME\aitour26-WRK540-unlock-your-agents-potential-with-model-context-protocol `
            ; git pull `
            ;code .vscode\csharp-workspace.code-workspace
            ```

        !!! note "Cuando el proyecto se abre en VS Code, aparecerá una notificación en la esquina inferior derecha para instalar la extensión de C#. Haz clic en **Install** para instalar la extensión de C#, ya que esto proporcionará las características necesarias para el desarrollo en C#."

    === "Visual Studio 2022"

        2. Abre el taller en Visual Studio 2022. Desde la ventana de terminal, ejecuta el siguiente comando:

            ```powershell
            ; git pull `
            ;cd $HOME; start .\aitour26-WRK540-unlock-your-agents-potential-with-model-context-protocol\src\csharp\McpAgentWorkshop.slnx
            ```

            !!! note "Es posible que se te pregunte con qué programa abrir la solución. Selecciona **Visual Studio 2022**."

## Estructura del Proyecto

=== "Python"

    Familiarízate con las **carpetas** y **archivos** clave en el espacio de trabajo con los que trabajarás durante todo el taller.

    ### La carpeta "workshop"

    - El archivo **app.py**: El punto de entrada para la aplicación, conteniendo su lógica principal.

        Nota la variable **INSTRUCTIONS_FILE**—establece qué archivo de instrucciones usa el agente. Actualizarás esta variable en un laboratorio posterior.

    - El archivo **resources.txt**: Contiene los recursos utilizados por la aplicación del agente.
    - El archivo **.env**: Contiene las variables de entorno utilizadas por la aplicación del agente.

    ### La carpeta "mcp_server"

    - El archivo **sales_analysis.py**: El Servidor MCP con herramientas para análisis de ventas.

    ### La carpeta "shared/instructions"

    - La carpeta **instructions**: Contiene las instrucciones pasadas al LLM.


    ![Estructura de carpetas del laboratorio](../../media/project-structure-self-guided-python.png)

=== "C#"

    ## Estructura del Proyecto

    El proyecto usa [Aspire](http://aka.ms/dotnet-aspire) para simplificar la construcción de la aplicación del agente, gestionar el servidor MCP y orquestar todas las dependencias externas. La solución está compuesta por cuatro proyectos, todos con el prefijo `McpAgentWorkshop`:

    * `AppHost`: El orquestador de Aspire, y proyecto de lanzamiento para el taller.
    * `McpServer`: El proyecto del servidor MCP.
    * `ServiceDefaults`: Configuración predeterminada para servicios, como registro y telemetría.
    * `WorkshopApi`: La API del Agente para el taller. La lógica principal de la aplicación está en la clase `AgentService`.

    Además de los proyectos .NET en la solución, hay una carpeta `shared` (visible como una Carpeta de Solución, y a través del explorador de archivos), que contiene:

    * `instructions`: Las instrucciones pasadas al LLM.
    * `scripts`: Scripts shell auxiliares para varias tareas, se hará referencia a estos cuando sea necesario.
    * `webapp`: La aplicación cliente front-end. Nota: Esta es una aplicación Python, cuyo ciclo de vida será gestionado por Aspire.

    ![Estructura de carpetas del laboratorio](../../media/project-structure-self-guided-csharp.png)

*Traducido usando GitHub Copilot.*