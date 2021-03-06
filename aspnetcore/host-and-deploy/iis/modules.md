---
title: Módulos de IIS con ASP.NET Core
author: guardrex
description: Descubra módulos activos e inactivos de IIS para aplicaciones ASP.NET Core y cómo administrar módulos de IIS.
ms.author: riande
ms.custom: mvc
ms.date: 04/04/2018
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 40af94f9cbb83f27f22d90b6b0f2854090687d34
ms.sourcegitcommit: ecf2cd4e0613569025b28e12de3baa21d86d4258
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2018
ms.locfileid: "43312351"
---
# <a name="iis-modules-with-aspnet-core"></a>Módulos de IIS con ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Las aplicaciones ASP.NET Core se hospedan en IIS en una configuración de proxy inverso. Algunos de los módulos nativos de IIS y todos los módulos administrados de IIS no están disponibles para procesar las solicitudes para las aplicaciones ASP.NET Core. En muchos casos, ASP.NET Core ofrece una alternativa a las características de los módulos nativos y administrados de IIS.

## <a name="native-modules"></a>Módulos nativos

En la tabla se indican los módulos nativos de IIS que son funcionales con solicitudes de proxy inverso en aplicaciones ASP.NET Core.

| Module | Funcional con aplicaciones ASP.NET Core | Opción de ASP.NET Core |
| ------ | :-------------------------------: | ------------------- |
| **Autenticación anónima**<br>`AnonymousAuthenticationModule` | Sí | |
| **Autenticación básica**<br>`BasicAuthenticationModule` | Sí | |
| **Autenticación de asignaciones de certificados de cliente**<br>`CertificateMappingAuthenticationModule` | Sí | |
| **CGI**<br>`CgiModule` | No | |
| **Validación de configuración**<br>`ConfigurationValidationModule` | Sí | |
| **Errores HTTP**<br>`CustomErrorModule` | No | [Middleware de páginas de códigos de estado](xref:fundamentals/error-handling#configure-status-code-pages) |
| **Registro personalizado**<br>`CustomLoggingModule` | Sí | |
| **Documento predeterminado**<br>`DefaultDocumentModule` | No | [Middleware de archivos predeterminados](xref:fundamentals/static-files#serve-a-default-document) |
| **Autenticación implícita**<br>`DigestAuthenticationModule` | Sí | |
| **Examen de directorios**<br>`DirectoryListingModule` | No | [Middleware de exploración de directorios](xref:fundamentals/static-files#enable-directory-browsing) |
| **Compresión dinámica**<br>`DynamicCompressionModule` | Sí | [Middleware de compresión de respuestas](xref:performance/response-compression) |
| **Traza**<br>`FailedRequestsTracingModule` | Sí | [Registro de ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Almacenamiento en caché de archivos**<br>`FileCacheModule` | No | [Middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware) |
| **Almacenamiento en caché HTTP**<br>`HttpCacheModule` | No | [Middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware) |
| **Registro HTTP**<br>`HttpLoggingModule` | Sí | [Registro de ASP.NET Core](xref:fundamentals/logging/index)<br>Implementaciones: [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging), [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging), [NLog](https://github.com/NLog/NLog.Extensions.Logging), [Serilog](https://github.com/serilog/serilog-extensions-logging)
| **Redireccionamiento HTTP**<br>`HttpRedirectionModule` | Sí | [Middleware de reescritura de dirección URL](xref:fundamentals/url-rewriting) |
| **Autenticación de asignaciones de certificados de cliente IIS**<br>`IISCertificateMappingAuthenticationModule` | Sí | |
| **Restricciones de IP y dominio**<br>`IpRestrictionModule` | Sí | |
| **Filtros ISAPI**<br>`IsapiFilterModule` | Sí | [Middleware](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule` | Sí | [Middleware](xref:fundamentals/middleware/index) |
| **Compatibilidad con el protocolo**<br>`ProtocolSupportModule` | Sí | |
| **Filtrado de solicitudes**<br>`RequestFilteringModule` | Sí | [Middleware de reescritura de dirección URL`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Supervisor de solicitudes**<br>`RequestMonitorModule` | Sí | |
| **Reescritura de direcciones URL**<br>`RewriteModule` | Sí&#8224; | [Middleware de reescritura de dirección URL](xref:fundamentals/url-rewriting) |
| **Inclusiones del lado servidor**<br>`ServerSideIncludeModule` | No | |
| **Compresión estática**<br>`StaticCompressionModule` | No | [Middleware de compresión de respuestas](xref:performance/response-compression) |
| **Contenido estático**<br>`StaticFileModule` | No | [Middleware de archivos estáticos](xref:fundamentals/static-files) |
| **Almacenamiento en caché de tokens**.<br>`TokenCacheModule` | Sí | |
| **Almacenamiento en caché de URI**<br>`UriCacheModule` | Sí | |
| **Autorización de URL**<br>`UrlAuthorizationModule` | Sí | [Identidad de ASP.NET Core](xref:security/authentication/identity) |
| **Autenticación de Windows**<br>`WindowsAuthenticationModule` | Sí | |

&#8224;Los tipos de coincidencia `isFile` y `isDirectory` del módulo de reescritura de direcciones URL no funcionan con las aplicaciones ASP.NET Core debido a los cambios en la [estructura de directorios](xref:host-and-deploy/directory-structure).

## <a name="managed-modules"></a>Módulos administrados

Los módulos administrados *no* son funcionales cuando la versión de CLR de .NET del grupo de aplicaciones se establece en **No Managed Code** (Sin código administrado). ASP.NET Core ofrece alternativas de middleware en varios casos.

| Module                  | Opción de ASP.NET Core |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| FormsAuthentication     | [Middleware de autenticación de cookies](xref:security/authentication/cookie) |
| OutputCache             | [Middleware de almacenamiento en caché de respuestas](xref:performance/caching/middleware) |
| Perfil                 | |
| RoleManager             | |
| ScriptModule 4.0        | |
| Sesión                 | [Middleware de sesión](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [Middleware de reescritura de dirección URL](xref:fundamentals/url-rewriting) |
| UrlRoutingModule 4.0    | [Identidad de ASP.NET Core](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>Cambios en la aplicación del Administrador de IIS

Al usar el Administrador de IIS para realizar la configuración, cambia el archivo *web.config* de la aplicación. Si implementa una aplicación e incluye *web.config*, los cambios realizados con el Administrador de IIS se sobrescriben con el archivo *web.config* implementado. Si se realizan cambios en el archivo *web.config* del servidor, copie el archivo *web.config* actualizado en el servidor en el proyecto local inmediatamente.

## <a name="disabling-iis-modules"></a>Deshabilitación de los módulos de IIS

Si un módulo de IIS configurado en el nivel de servidor debe deshabilitarse en una aplicación, la adición del archivo *web.config* de la aplicación puede deshabilitar el módulo. Puede dejar el módulo en su sitio y desactivarlo mediante un valor de configuración (si está disponible), o quitar el módulo de la aplicación.

### <a name="module-deactivation"></a>Desactivación de módulos

Muchos módulos ofrecen un valor de configuración que les permite deshabilitarse sin quitar el módulo de la aplicación. Esta es la manera más sencilla y rápida de desactivar un módulo. Por ejemplo, el módulo de redireccionamiento de HTTP se puede deshabilitar con el elemento **\<httpRedirect>** en *web.config*:

```xml
<configuration>
  <system.webServer>
     <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Para más información sobre la deshabilitación de los módulos con valores de configuración, siga los vínculos de la sección sobre *elementos secundarios* de [IIS \<system.webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Eliminación de módulos

Si opta por quitar un módulo con un valor de configuración en *web.config*, primero desbloquee el módulo y desbloquee la sección **\<modules>** de *web.config*:

1. Desbloquee el módulo en el nivel de servidor. Seleccione el servidor de IIS en la barra lateral **Conexiones** del Administrador de IIS. Abra **Módulos** en el área **IIS**. Seleccione el módulo de la lista. En la barra lateral **Acciones** e la derecha, seleccione **Desbloquear**. Desbloquee tantos módulos como quiera quitar de *web.config* más tarde.

2. Implemente la aplicación sin una sección **\<modules>** en *web.config*. Si una aplicación se implementa con un archivo *web.config* que contiene la sección **\<modules>** sin haber desbloqueado primero la sección en el Administrador de IIS, Configuration Manager produce una excepción al intentar desbloquear la sección. Por lo tanto, implementar la aplicación sin una sección **\<modules>**.

3. Desbloquee la sección **\<modules>** de *web.config*. En la barra lateral **Conexiones**, seleccione el sitio web en **Sitios**. En el área **Administración**, abra el **error de configuración**. Use los controles de navegación para seleccionar la sección `system.webServer/modules`. En la barra lateral **Acciones** de la derecha, seleccione **Desbloquear** la sección.

4. En este punto, se puede agregar una sección **\<modules>** al archivo *web.config* con un elemento **\<remove>** para quitar el módulo de la aplicación. Se pueden agregar varios elementos **\<remove>** para quitar varios módulos. Si se realizan cambios en *web.config* en el servidor, realice inmediatamente los mismos cambios en el archivo *web.config* el proyecto de forma local. Quitar un módulo de esta manera no afecta al uso del módulo con otras aplicaciones del servidor.

   ```xml
   <configuration> 
    <system.webServer> 
      <modules> 
        <remove name="MODULE_NAME" /> 
      </modules> 
    </system.webServer> 
   </configuration>
   ```

Un módulo de IIS también se puede quitar con *Appcmd.exe*. Proporcione `MODULE_NAME` y `APPLICATION_NAME` en el comando:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Por ejemplo, quite `DynamicCompressionModule` del sitio web predeterminado:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Configuración mínima del módulo

Los únicos módulos necesarios para ejecutar una aplicación ASP.NET Core son el Módulo de autenticación anónima y el Módulo ASP.NET Core.

![Administrador de IIS abierto en Módulos que muestra la configuración mínima del módulo](modules/_static/modules.png)

El Módulo de almacenamiento en caché de URI (`UriCacheModule`) permite a IIS almacenar en caché la configuración del sitio web en el nivel de dirección URL. Sin este módulo, IIS debe leer y analizar la configuración en cada solicitud, incluso cuando se solicita de forma repetida la misma dirección URL. Como consecuencia de ello, el rendimiento se reduce considerablemente. *Aunque el Módulo de almacenamiento en caché de URI no es estrictamente necesario para ejecutar una aplicación ASP.NET Core hospedada, es recomendable habilitarlo en todas las implementaciones de ASP.NET Core.*

El Módulo de almacenamiento en caché de HTTP (`HttpCacheModule`) implementa la caché de resultados de IIS y también la lógica de almacenamiento en caché de los elementos de la caché de HTTP.sys. Sin este módulo, el contenido ya no se almacena en caché en modo kernel y los perfiles de caché se pasan por alto. Quitar el Módulo de almacenamiento en caché de HTTP normalmente tiene efectos negativos sobre el rendimiento y el uso de los recursos. *Aunque el Módulo de almacenamiento en caché de HTTP no es estrictamente necesario para ejecutar una aplicación ASP.NET Core hospedada, es recomendable habilitarlo en todas las implementaciones de ASP.NET Core.*

## <a name="additional-resources"></a>Recursos adicionales

* [Hospedaje en Windows con IIS](xref:host-and-deploy/iis/index)
* [Introduction to IIS Architectures: Modules in IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis) (Introducción a las arquitecturas de IIS: módulos de IIS)
* [IIS Modules Overview](/iis/get-started/introduction-to-iis/iis-modules-overview) (Introducción a los módulos de IIS)
* [Customizing IIS 7.0 Roles and Modules](https://technet.microsoft.com/library/cc627313.aspx) (Personalización de los roles y módulos de IIS 7.0)
* [IIS `<system.webServer>`](/iis/configuration/system.webServer/)
