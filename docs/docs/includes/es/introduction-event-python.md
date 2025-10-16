## Asistentes a Eventos de Microsoft

Las instrucciones en esta página asumen que estás asistiendo a un evento y tienes acceso a un entorno de laboratorio preconfigurado. Este entorno proporciona una suscripción de Azure con todas las herramientas y recursos necesarios para completar el taller.

## Introducción

Este taller está diseñado para enseñarte sobre el Servicio de Agentes de Azure AI y el [SDK](https://learn.microsoft.com/python/api/overview/azure/ai-projects-readme?context=%2Fazure%2Fai-services%2Fagents%2Fcontext%2Fcontext){:target="_blank"} asociado. Consiste en múltiples laboratorios, cada uno destacando una característica específica del Servicio de Agentes de Azure AI. Los laboratorios están destinados a completarse en orden, ya que cada uno se basa en el conocimiento y trabajo del laboratorio anterior.

## Seleccionar Lenguaje de Programación del Taller

El taller está disponible tanto en Python como en C#. Por favor asegúrate de seleccionar el lenguaje que se ajuste a la sala del laboratorio o preferencia usando las pestañas de selector de lenguaje. Nota, no cambies de lenguaje a mitad del taller.

**Selecciona la pestaña de lenguaje que coincida con tu sala de laboratorio:**

=== "Python"
    El lenguaje por defecto para el taller está establecido a **Python**.

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

## Estructura del Proyecto

=== "Python"

    Asegúrate de familiarizarte con las **subcarpetas** y **archivos** clave con los que trabajarás durante todo el taller.

    1. El archivo **app.py**: El punto de entrada para la aplicación, conteniendo su lógica principal.
    2. El archivo **resources.txt**: Los recursos de Azure AI Foundry creados para ti en este laboratorio.
    3. El archivo **sales_analysis.py**: La lógica de función implementada en el servidor MCP para ejecutar consultas SQL dinámicas contra la base de datos SQLite.
    4. El archivo **stream_event_handler.py**: Contiene la lógica del manejador de eventos para transmisión de tokens.
    5. La carpeta **shared/instructions**: Contiene las instrucciones del agente.

    ![Estructura de carpetas del laboratorio](../../media/project-structure-self-guided-python.png)

*Traducido usando GitHub Copilot.*