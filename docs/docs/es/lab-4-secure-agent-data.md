## Lo que Aprenderás

En este laboratorio, asegurarás los datos del agente usando el Protocolo de Contexto de Modelo (MCP) y la Seguridad a Nivel de Fila (RLS) de PostgreSQL. El agente tiene acceso de solo lectura a la base de datos y los datos están protegidos por roles de usuario (oficina central y gerente de tienda) para asegurar que solo usuarios autorizados puedan acceder a información específica.

## Introducción

La base de datos PostgreSQL usa Seguridad a Nivel de Fila (RLS) para controlar el acceso a datos por rol de usuario. El cliente de chat web por defecto usa el rol `Head office` (acceso completo a datos), pero cambiar al rol `Store Manager` restringe el acceso solo a datos específicos del rol.

El Servidor MCP proporciona al agente acceso a la base de datos Zava. Cuando el servicio del agente procesa solicitudes de usuario, el rol del usuario (UUID) se pasa al servidor MCP a través de los Encabezados de Recursos de Herramientas MCP para asegurar que se aplique la seguridad basada en roles.

En operación normal, un gerente de tienda se autenticaría con el agente y su rol de usuario se establecería en consecuencia. Pero esto es un taller, y vamos a seleccionar manualmente un rol.

??? note "🚀 Para Desarrolladores: ¿Cómo funciona la Seguridad a Nivel de Fila de PostgreSQL?"

    ### Vista General de Seguridad RLS de PostgreSQL

    La Seguridad a Nivel de Fila (RLS) filtra automáticamente las filas de la base de datos basándose en permisos del usuario. Esto permite que múltiples usuarios compartan las mismas tablas de base de datos mientras solo ven datos que están autorizados a acceder. 
    
    En este sistema, los usuarios de oficina central ven todos los datos de todas las tiendas, mientras que los gerentes de tienda están restringidos a ver solo la información de su propia tienda. El ejemplo de abajo muestra cómo se implementan las políticas RLS para la tabla `retail.orders`, con políticas idénticas aplicadas a las tablas `retail.order_items`, `retail.inventory`, y `retail.customers`.

    ```sql
    CREATE POLICY store_manager_orders ON retail.orders
    FOR ALL TO PUBLIC
    USING (
        -- Head office sees all data
        current_setting('app.current_rls_user_id', true) = '00000000-0000-0000-0000-000000000000'
        OR
        -- Store managers only see their store's data
        EXISTS (SELECT 1 FROM retail.stores s WHERE s.store_id = retail.orders.store_id 
                AND s.rls_user_id::text = current_setting('app.current_rls_user_id', true))
    );
    ```

    **Resultado:** Los gerentes de tienda solo ven los datos de su tienda, mientras que la oficina central ve todo - todo usando la misma base de datos y tablas.



    === "Python"

        Encontrarás el código responsable de establecer el rol del usuario en el archivo `workshop/chat_manager.py`.

        ```python
        if request.rls_user_id:
            # Create dynamic tool resources with RLS user ID header
            mcp_tool_resource = MCPToolResource(
                server_label="ZavaSalesAnalysisMcpServer",
                headers={"x-rls-user-id": request.rls_user_id},
                require_approval="never",
            )
            tool_resources.mcp = [mcp_tool_resource]
        ```

        El código para recuperar el ID de Usuario RLS está en `mcp_server/sales_analysis/sales_analysis.py`. Si el servidor no detecta el encabezado RLS, por defecto usa el rol de Oficina Central. Este respaldo está destinado solo para uso del taller y no debe aplicarse en producción.

        ```python
        def get_rls_user_id(ctx: Context) -> str:
            """Get the Row Level Security User ID from the request context."""

            rls_user_id = get_header(ctx, "x-rls-user-id")
            if rls_user_id is None:
                # Default to a placeholder if not provided
                rls_user_id = "00000000-0000-0000-0000-000000000000"
            return rls_user_id
        ```

    === "C#"

        Encontrarás el código responsable de establecer el rol del usuario en las solicitudes al Servidor MCP en la clase `AgentService`.

        ```csharp
        var mcpToolResource = new MCPToolResource(ZavaMcpToolLabel, new Dictionary<string, string>
        {
            { "x-rls-user-id", request.RlsUserId }
        });
        var toolResources = new ToolResources();
        toolResources.Mcp.Add(mcpToolResource);
        ```

        El `MCPToolResource` se agrega luego a la colección `ToolResources`, que se proporciona a la ejecución de streaming usando la propiedad `CreateRunStreamingOptions.ToolResources`. Esto es porque el ID de usuario RLS es un valor dinámico del cliente (diferentes usuarios "conectados" pueden tener diferentes IDs), necesitamos asegurar que se establezca en la _ejecución_ del hilo en lugar de cuando se crea el agente.

        Como el ID de usuario RLS se establece como un encabezado para que el agente lo reenvíe al Servidor MCP, esto se accede desde el `HttpContext` en la solicitud, que se puede acceder desde un `IHttpContextAccessor`, que se inyecta en los métodos de herramientas MCP. Se ha creado un método de extensión, `HttpContextAccessorExtensions.GetRequestUserId`, que se puede usar dentro de una herramienta:

        ```csharp
        public async Task<string> ExecuteSalesQueryAsync(
            NpgsqlConnection connection,
            ILogger<SalesTools> logger,
            IHttpContextAccessor httpContextAccessor,
            [Description("A well-formed PostgreSQL query.")] string query
        )
        {
            ...

            var rlsUserId = httpContextAccessor.GetRequestUserId();

            ...
        }
        ```

    ### Estableciendo el ID de Usuario RLS de Postgres

    Ahora que el Servidor MCP tiene el ID de Usuario RLS, necesita establecerse en la conexión PostgreSQL.

    === "Python"

        La solución Python establece el ID de Usuario RLS en cada conexión PostgreSQL llamando `set_config()` dentro del método `execute_query` en `mcp_server/sales_analysis/sales_analysis_postgres.py`.

        ```python
        ...
        conn = await self.get_connection()
        await conn.execute("SELECT set_config('app.current_rls_user_id', $1, false)", rls_user_id)

        rows = await conn.fetch(sql_query)
        ...
        ```

    === "C#"

        La solución C# establece el ID de Usuario RLS en la conexión PostgreSQL ejecutando un comando SQL para establecer la variable de contexto RLS inmediatamente después de abrir la conexión en el método `ExecuteSalesQueryAsync` en `SalesTools.cs`.

        ```csharp
        ...
        await using var cmd = new NpgsqlCommand("SELECT set_config('app.current_rls_user_id', @rlsUserId, false)", connection);
        cmd.Parameters.AddWithValue("rlsUserId", rlsUserId ?? string.Empty);
        await cmd.ExecuteNonQueryAsync();

        await using var queryCmd = new NpgsqlCommand(query, connection);
        await using var reader = await queryCmd.ExecuteReaderAsync();
        ...
        ```



## Ejercicio del Laboratorio

### Rol de Oficina Central

Por defecto, el cliente web opera con el rol `Head Office`, que tiene acceso completo a todos los datos.

1. Ingresa la siguiente consulta en el chat:

   ```text
   Show sales by store
   ```

   Verás que se devuelven los datos de todas las tiendas. Perfecto.

### Seleccionar un Rol de Gerente de Tienda

1. Regresa a la pestaña Web Chat de Agentes en tu navegador.
2. Selecciona el ícono de `settings` en la esquina superior derecha de la página.
3. Selecciona una `Store location` del menú desplegable.
4. Selecciona `Save` y ahora el agente operará con los permisos de acceso a datos de la ubicación de tienda seleccionada.

   ![](../media/select_store_manager_role.png)

Ahora el agente solo tendrá acceso a los datos para la ubicación de tienda seleccionada.

!!! info "Nota"
    Cambiar el usuario reiniciará la sesión de chat, ya que el contexto está vinculado al usuario.

Prueba la siguiente consulta:

```text
Show sales by store
```

Notarás que el agente solo devuelve datos para la ubicación de tienda seleccionada. Esto demuestra cómo el acceso a datos del agente está restringido basándose en el rol de gerente de tienda seleccionado.

![](../media/select_seattle_store_role.png)

*Traducido usando GitHub Copilot.*