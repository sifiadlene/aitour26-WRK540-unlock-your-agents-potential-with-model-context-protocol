## Tecnologías principales de un vistazo

- **Servicio de Agentes de Azure AI Foundry**
  Aloja el agente impulsado por LLM; orquesta herramientas (incluyendo Servidores MCP); gestiona contexto, Intérprete de Código y transmisión de tokens; y proporciona autenticación, registro y escalado.
- **Servidores MCP**
  MCP (Protocolo de Contexto de Modelo) es un estándar abierto que proporciona a los LLMs una interfaz unificada para herramientas externas, APIs y datos. Estandariza el descubrimiento de herramientas (como OpenAPI para REST) y mejora la composabilidad al hacer que las herramientas sean fáciles de actualizar o intercambiar según evolucionen las necesidades.
- **PostgreSQL + pgvector**
  Almacena datos relacionales y embeddings; soporta tanto consultas relacionales (SQL) como semánticas (vectoriales) (a través de pgvector), gobernadas por SQL y RLS.

**En conjunto:** el Servicio de Agentes enruta las intenciones del usuario; el servidor MCP las traduce en llamadas de herramientas/SQL; PostgreSQL+pgvector responde preguntas semánticas y analíticas.

## Arquitectura de la Solución

La arquitectura de la solución de Análisis de Ventas de Zava incluye:

- Una instancia del **Servicio de Agentes de Azure AI Foundry** que aloja el agente de Análisis de Ventas de Zava.
- Una base de datos **PostgreSQL** con la extensión **pgvector**, almacenando datos de ventas de Zava y embeddings.
- Un **Servidor MCP** que expone la base de datos PostgreSQL al agente a través de MCP.
- Una aplicación **Administrador de Agentes** que gestiona la interacción entre el usuario y el agente.
- Una interfaz de **Chat Web** para interacción de chat en tiempo real con el agente.

![La imagen muestra la arquitectura para la solución de Análisis de Ventas de Zava](../media/solution-overview.png)

## Beneficios clave de los Servidores MCP

- **Interoperabilidad** – Conecta agentes de IA a herramientas habilitadas para MCP de cualquier proveedor con código personalizado mínimo.
- **Ganchos de seguridad** – Integra inicio de sesión, permisos y registro de actividades.
- **Reutilización** – Construye una vez, reutiliza en proyectos, nubes y tiempos de ejecución.
- **Simplicidad operacional** – Un solo contrato reduce el código repetitivo y el mantenimiento.

## Mejores prácticas demostradas

- **APIs asíncronas:** El servicio de agentes y PostgreSQL usan APIs asíncronas; ideales con FastAPI/ASP.NET/Streamlit.
- **Transmisión de tokens:** Mejora la latencia percibida en la UI.
- **Observabilidad:** Soporte integrado de trazado y métricas para monitoreo y optimización.
- **Seguridad de base de datos:** PostgreSQL está asegurado con privilegios de agente restringidos y Seguridad a Nivel de Fila (RLS), limitando a los agentes solo a sus datos autorizados.
- **Intérprete de Código:** El [Intérprete de Código del Servicio de Agentes de Azure AI](https://learn.microsoft.com/azure/ai-services/agents/how-to/tools/code-interpreter?view=azure-python-preview&tabs=python&pivots=overview){:target="_blank"} ejecuta código generado por LLM bajo demanda en un entorno **aislado**, previniendo acciones más allá del alcance del agente.

### Extensibilidad

El patrón del taller se puede adaptar (ej., soporte al cliente) actualizando la base de datos + instrucciones del agente en Foundry.

## Arquitectura DevTunnel

En el entorno del taller, el Servicio de Agentes se ejecuta en Azure pero necesita conectarse a tu Servidor MCP ejecutándose localmente. DevTunnel crea un túnel seguro que expone tu Servidor MCP local al Servicio de Agentes basado en la nube.

```plaintext
          Nube Azure                          Desarrollo Local
    ┌─────────────────────┐                  ┌─────────────────────┐
    │   App Agente Zava   │                  │                     │
    │  (Alojado en Azure) │                  │  ┌─────────────────┐│
    │                     │                  │  │ Servidor MCP    ││
    │ ┌─────────────────┐ │                  │  │(sales_analysis) ││
    │ │ Servicio de     │ │                  │  │ localhost:8000  ││
    │ │ Agentes Azure AI│ │                  │  └─────────────────┘│
    │ └─────────────────┘ │                  │           │         │
    └─────────────────────┘                  │           ▼         │
              │                              │  ┌─────────────────┐│
              │ Solicitudes HTTPS            │  │   PostgreSQL    ││
              ▼                              │  │   + pgvector    ││
    ┌─────────────────────┐                  │  └─────────────────┘│
    │   DevTunnel         │                  │                     │
    │ Endpoint Público    │◄─────────────────┼──── Túnel Seguro    │
    │ (*.devtunnels.ms)   │ Reenvío de Puerto│                     │
    └─────────────────────┘                  └─────────────────────┘
```

**Cómo Funciona DevTunnel en el Taller:**

1. **Desarrollo Local**: Ejecutas el Servidor MCP localmente en `localhost:8000`
2. **Creación de DevTunnel**: DevTunnel crea un endpoint HTTPS público (ej., `https://abc123.devtunnels.ms`) conectado a `localhost:8000`.
3. **Integración Azure**: El Servicio de Agentes alojado en Azure se conecta al Servidor MCP a través del endpoint DevTunnel.
4. **Operación Transparente**: El servicio de agentes opera normalmente, sin saber que está accediendo al Servidor MCP ejecutándose localmente a través de un túnel.

Esta configuración te permite:

- Desarrollar y depurar localmente mientras usas servicios de IA alojados en la nube
- Probar escenarios realistas sin desplegar el Servidor MCP a Azure

*Traducido usando GitHub Copilot.*