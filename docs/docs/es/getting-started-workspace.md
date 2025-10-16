Hay dos espacios de trabajo de VS Code en el taller, uno para Python y otro para C#. El espacio de trabajo contiene el código fuente y todos los archivos necesarios para completar los laboratorios para cada lenguaje. Elige el espacio de trabajo que coincida con el lenguaje con el que quieres trabajar.

=== "Python"

    1. **Copia** la siguiente ruta al portapapeles:

        ```text
        /workspace/.vscode/python-workspace.code-workspace
        ```
    1. Desde el menú de VS Code, selecciona **File** y luego **Open Workspace from File**.
    3. Reemplaza y **pega** el nombre de ruta copiado y selecciona **OK**.


    ## Estructura del Proyecto

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

    ![Estructura de carpetas del laboratorio](media/project-structure-self-guided-python.png)

=== "C#"

    1. En Visual Studio Code, ve a **File** > **Open Workspace from File**.
    2. Reemplaza la ruta predeterminada con la siguiente:

        ```text
        /workspace/.vscode/csharp-workspace.code-workspace
        ```

    3. Selecciona **OK** para abrir el espacio de trabajo.

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

    ![Estructura de carpetas del laboratorio](media/project-structure-self-guided-csharp.png)

*Traducido usando GitHub Copilot.*