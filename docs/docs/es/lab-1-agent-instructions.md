## Lo Que Aprenderás

En este laboratorio, revisarás y actualizarás las instrucciones del agente para incluir una regla sobre el año fiscal que comienza el 1 de julio. Esto es importante para que el agente interprete y analice correctamente los datos de ventas.

## Introducción

El propósito de las instrucciones del agente es definir el comportamiento del agente, incluyendo cómo interactúa con los usuarios, qué herramientas puede usar y cómo debe responder a diferentes tipos de consultas. En este laboratorio, revisarás las instrucciones existentes del agente y harás una pequeña actualización para asegurar que el agente interprete correctamente el año fiscal.

## Ejercicio del Laboratorio

### Abrir las Instrucciones del Agente

1. Desde el Explorador de VS Code, navega a la carpeta `shared/instructions`.
2. **Abre** el archivo `mcp_server_tools_with_code_interpreter.txt`.

![Abrir Instrucciones del Agente](../media/vs-code-instructions-folder.png)

### Revisar las Instrucciones del Agente

Revisa cómo las instrucciones definen el comportamiento de la aplicación del agente:

!!! tip "En VS Code, presiona Alt + Z (Windows/Linux) u Option + Z (Mac) para habilitar el modo de ajuste de línea, haciendo las instrucciones más fáciles de leer."

- **Rol Principal:** Agente de ventas para Zava (minorista de bricolaje WA) con tono profesional y amigable usando emojis y sin suposiciones o contenido no verificado.
- **Reglas de Base de Datos:** Siempre obtener esquemas primero (get_multiple_table_schemas()) con LIMIT 20 obligatorio en todas las consultas SELECT usando nombres exactos de tabla/columna.
- **Visualizaciones:** Crear gráficos SOLO cuando se solicite explícitamente usando activadores como "gráfico", "graficar", "visualizar", o "mostrar como [tipo]" en formato PNG descargado de sandbox sin rutas de imagen markdown.
- **Respuestas:** Por defecto a tablas Markdown con soporte multi-idioma y CSV disponible bajo solicitud.
- **Seguridad:** Mantener en el alcance solo de datos de ventas de Zava con respuestas exactas para consultas fuera de alcance/poco claras y redirigir usuarios hostiles a TI.
- **Restricciones Clave:** Sin datos inventados usando solo herramientas con límite de 20 filas e imágenes PNG siempre.

### Actualizar las Instrucciones del Agente

Copia el texto de abajo y pega directamente después de la regla sobre no generar contenido no verificado o hacer suposiciones:

!!! tip "Haz clic en el ícono de copiar a la derecha para copiar el texto al portapapeles."

```markdown
- **El año fiscal (AF) comienza el 1 jul** (Q1=Jul–Sep, Q2=Oct–Dic, Q3=Ene–Mar, Q4=Abr–Jun).
```

Las instrucciones actualizadas deberían verse así:

```markdown
- Usa **solo** salidas de herramientas verificadas; **nunca** inventes datos o suposiciones.
- **El año fiscal (AF) comienza el 1 jul** (Q1=Jul–Sep, Q2=Oct–Dic, Q3=Ene–Mar, Q4=Abr–Jun).
```

*Traducido usando GitHub Copilot.*