
^indice
---

## Table of Contents

1. [Introducción](#introducción)  
2. [Formato URL](#^formato-url)
3. [Métodos HTTP (HTTP Methods)](#^metodos-http)  
   - [GET](#get)
   - [PUT](#put)  
   - [POST](#post)  
   - [PATCH](#patch)  
   - [DELETE](#delete)  
4. [Identificador Único (UID) (Unique Identifier (UID))](#^identificador-unico-uid)  
5. [Configuración Requerida para Rutas de Archivos (Required Configuration for File Paths)](#^configuracion-requerida-para-rutas-de-archivos)  
6. [Smart 3D Admin Web API Entity Data Model (EDM)](#^smart-3d-admin-web-api-entity-data-model-edm)  
   - [Entities](#^entities)
   - [Properties](#^properties)
   - [Navigation properties](#^navigation-properties)
   - [Actions](#^actions)
   - [Functions](#^functions)
   - [Operaciones de escritura (Write operations)](#^write-operations)
   - [Creación de entidades (Creating entities)](#^creating-entities)
   - [Cambio de relaciones (Changing relationships)](#^changing-relationships)
   - [Actualización de entidades individuales (Updating single entities)](#^updating-single-entities)
   - [Eliminación de entidades (Deleting entities)](#^deleting-entities)
   - [Eliminar relaciones (Delete relationships)](#^delete-relationships)
   - [Límites (Limits)](#^limits)
7. [Compatibilidad OData (OData Support)](#^odata-support)  
   - [Opciones de consulta (Query Options)](#^query-options)  
   - [Tratamiento de caracteres especiales en el parámetro filter (Handling special characters in filter parameter)](#^handling-special-characters-in-filter-parameter)
   - [Peticiones asíncronas (Asynchronous Requests)](#^asynchronous-requests)
   - [Funcionalidad OData no soportada (Unsupported OData Functionality)](#^unsupported-odata-functionality)  

---

## Introducción

Intergraph Smart® 3D Admin Web API es una API RESTful compatible con OData, que permite realizar actividades administrativas de Smart 3D. Este documento no intenta explicar el uso de funcionalidades genéricas como HTML, OData o RESTful, sino que se centra en las áreas únicas del producto Smart 3D. Existe una descripción completa sobre el uso de Hexagon Smart APIs disponible en la Smart API Developer Documentation.

- **Atención al cliente (Customer Support)**  
- **Declaración contra la piratería (Anti-Piracy Statement)**  
- © 2021 - 2024 Hexagon AB y/o sus subsidiarias y afiliadas  
- Versión 14 Update 1  
- Publicado el 10/31/2024 a las 8:34 PM  

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^formato-url
## Formato URL

El formato general de una solicitud URL a la web API es:

```
https://Host/WebApp/s3dadmin/v1/Sites(‘Site Alias’)/Plants(‘Plant Name’)/Remaining Resource Path/Query Options
```

Donde:
- **Host** es el nombre del servidor web de la API.  
- **WebApp** es el nombre de la aplicación web definida para el servidor.  
- **s3dadmin/v1** es el prefijo del servicio.  
- **Site Alias** es el alias para la combinación de servidor de base de datos Smart 3D y base de datos Smart 3D. Este valor se definió cuando se disparó "RestoreSite" o "EnableSite" de Smart 3D Admin Web API.  
- **Plant Name** es el nombre de la planta de Smart 3D (en el sitio especificado anteriormente) a la que se va a acceder.  
- **Remaining Resource Path** define el recurso específico al que se accederá.  
- **Query Options** son las opciones de consulta estándar de OData.

Por ejemplo, para devolver las propiedades de un grupo de permisos llamado "PG202" de la planta "MyS3dPlant", se utiliza la siguiente URL:

```
GET
https://MyServer.domain.com/WebApp/s3dadmin/v1/Sites('MyS3DSiteAlias')/Plants('MyS3dPlant')/PermissionGroups?$filter=Name eq 'PG202'
```

#### `<ServiceRoot>` Sintaxis de URL en este documento

Para mantener las URL legibles, se muestra `<ServiceRoot>` como la primera parte de la URL.

Por ejemplo, esto:

```
GET
https://MyServer.domain.com/WebApp/s3dadmin/v1/Sites(‘MyS3DSiteAlias’)/Plants(’MyS3dPlant’)/PermissionGroups?$filter=Name eq ‘PG202’
```

Se mostrará como:

```
GET
<ServiceRoot>/Sites(‘MyS3DSiteAlias’)/Plants(’MyS3dPlant’)/PermissionGroups?$filter=Name eq ‘PG202’
```

O si fueras a devolver propiedades de un Permission Group perteneciente a un Catalog, se mostraría así:

```
GET
<ServiceRoot>/Sites(‘MyS3DSiteAlias’)/Catalogs(’MyCatalog’)/PermissionGroups?$filter=Name eq ‘PG101’
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^metodos-http
## Metodos HTTP (HTTP Methods)

La web API admite los métodos HTTP **GET**, **PUT**, **POST**, **PATCH** y **DELETE**.

#### GET
El método GET se utiliza para recuperar datos de la base de datos Smart 3D y para ejecutar las funciones proporcionadas.

#### PUT
El método PUT se utiliza para crear o modificar relaciones entre dos entidades.

#### POST
El método POST se utiliza para ejecutar las acciones proporcionadas o crear entidades.

#### PATCH
El método PATCH se utiliza para actualizar propiedades de un objeto concreto de Smart 3D.

#### DELETE
El método DELETE se utiliza para eliminar una entidad o una relación entre dos entidades.


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^identificador-unico-uid
## Identificador Único (UID) (Unique Identifier (UID))

En general, el UID que se pasa en una URL para identificar un recurso específico es el OID de Smart 3D del objeto. El formato del OID debe ser `{00007533-0000-0000-0200-54C7B7541D04}`, incluso para una base de datos Oracle. Algunas Navigation properties no usan el OID como UID en una URL:

- **Sites** - El UID es el alias del sitio definido durante la configuración del servicio web.  
- **Plants** - El UID es el nombre de la Plant.  
- **Catalogs** - El UID es el nombre del Catalog.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^configuracion-requerida-para-rutas-de-archivos
## Configuración Requerida para Rutas de Archivos (Required Configuration for File Paths)

La mayoría de las acciones de Smart 3D Admin Web API leen y escriben en el sistema de archivos del servidor de Smart 3D. Para que estas acciones funcionen:

- La ruta debe ser escribible para todos los usuarios configurados en el servidor Smart 3D.  
- La ruta debe tener suficiente espacio de almacenamiento para acomodar archivos de respaldo u otro contenido que se escriba.

Específicamente, esto aplica a las rutas que especifiques para cualquier parámetro con `Path` en el nombre del parámetro, por ejemplo:

- `backupFilesPath`
- `backupConfigurationFilePath`
- `templateFilePath`
- `sharedContentPath`

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^smart-3d-admin-web-api-entity-data-model-edm
## Smart 3D Admin Web API Entity Data Model (EDM)

El Entity Data Model puede accederse usando la URL `<ServiceRoot>/$metadata`.

^entities
### Entities

Las entidades en el EDM de Smart 3D Admin Web API se basan en la información requerida para realizar todas las actividades de administración. Las siguientes son las entidades:

- Site  
- Catalog  
- Plant  
- Model  
- PermissionGroup  
- PermissionGroupFolder  
- AccessControlRule  
- Location  
- Class, ClassTool, ClassMap  
- Interface, InterfaceTool, InterfaceMap  
- Property, PropertyTool, PropertyMap  
- List, ListTool, ListMap  
- ListItem, ListItemTool, ListItemMap  
- UomList, UomListTool, UomListMap  
- UomListItem, UomListItemTool, UomListItemMap  

Todas las entidades tienen directa o indirectamente una clase base definida como `DynamicEdmEntityObject`. Esta clase base tiene dos propiedades: `Id` y `ConcurrencyToken`. `Id` contiene el UID, como se describe en [Unique Identifier (UID)](#identificador-único-uid). `ConcurrencyToken` es una propiedad generada que se usa para determinar si el objeto actual está actualizado al realizar operaciones PATCH.

```xml
<EntityType Name="DynamicEdmEntityObject" Abstract="true" OpenType="true">
  <Key>
    <PropertyRef Name="Id" />
  </Key>
  <Property Name="Id" Type="Edm.String" Nullable="false" />
  <Property Name="ConcurrencyToken" Type="Edm.String" />
```

Nota: las entidades `PermissionGroup` y `PermissionGroupFolder` derivan de la clase base `S3DItem`, definida como:

```xml
<EntityType Name="S3dItem" BaseType="Com.Ingr.Core.V1.DynamicEdmEntityObject" Abstract="true" OpenType="true">
  <Property Name="ClassName" Type="Edm.String" />
  <Property Name="DateCreated" Type="Edm.DateTimeOffset" />
  <Property Name="DateLastModified" Type="Edm.DateTimeOffset" />
  <Property Name="UserCreated" Type="Edm.String" />
  <Property Name="UserLastModified" Type="Edm.String" />
  <Property Name="ApprovalStatus" Type="Edm.Int64" />
  <Property Name="ApprovalReason" Type="Edm.Int64" />
</EntityType>
```

La clase base `S3dItem` es `DynamicEdmEntityObject`.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

^properties
### Properties

Las propiedades EDM corresponden a los atributos de Smart 3D.  

Sus propiedades se pueden descubrir usando el documento `$metadata` del EDM y pueden participar completamente en cualquier opción de consulta del sistema OData.  

Al devolver una entidad que representa un objeto de tipo de usuario de Smart 3D, las propiedades definidas solo en el tipo de usuario y no en la clase base se devuelven como propiedades dinámicas. Debido a que no son propiedades estructurales, no están representadas en el documento `$metadata` y las opciones de consulta OData relacionadas con estas propiedades se limitan solo a declaraciones `$select`. Por ejemplo:

```
URI - <ServiceRoot>/Sites?$select=Id,Status
```

> **Nota:** Se devuelve una propiedad abierta `Status` cuando `Site`, `Catalog` o `Plant` se muestran en la respuesta, únicamente si la entidad no está en estado "up to date" o "ready" o "usable". El valor de esta propiedad muestra la razón por la cual esta entidad no está en estado "Ready".

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

^navigation-properties
### Navigation properties

Las Navigation properties en el EDM corresponden a relaciones en Smart 3D. Solo se representa un subconjunto de las relaciones de Smart 3D. Puedes ver estas propiedades en los metadatos EDM.

#### Annotation

Por lo general, el nombre de la navigation property no coincide exactamente con el nombre de la relación en Smart 3D. Para determinar el nombre de la relación en Smart 3D, revisa la anotación `Description`.

Por ejemplo, para determinar el significado de la navigation property `PermissionGroups` en la entidad `PermissionGroupFolder`, observa la anotación `PermissionGroupFolder/PermissionGroups` (mostrada abajo) que indica que utiliza la relación de Smart 3D `BelongsToFolder`:

```xml
<Annotations Target="Com.Ingr.S3dAdmin.V1.PermissionGroupFolder/PermissionGroups">
  <Annotation Term="Org.OData.Core.V1.Description" String="Navigation property from the PermissionGroupFolder object to the collection of PermissionGroup using the BelongsToFolder relationship" />
  <Annotation Term="Com.Ingr.Core.V1.DisplayName" String="PermissionGroups" />
</Annotations>
```

> Las anotaciones pueden accederse en `<ServiceRoot>/annotations/$metadata`.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

^actions
### Actions

Las siguientes acciones están disponibles en la web API. Deben ejecutarse usando el método HTTP POST.

> **Todas las acciones son operaciones de larga duración**, por lo que se necesitan al menos dos instancias de Smart 3D Admin Services para ejecutarse.

#### CreateSite

Crea un `Site` a partir de los datos proporcionados. Por ejemplo, la siguiente URL crea y devuelve una entidad `Site` usando los detalles mencionados en el cuerpo:

```
POST
<ServiceRoot>/CreateSite

{
  "databaseServer": "ProductionServer1",
  "databaseProvider": "Com.Ingr.S3dAdmin.V1.DatabaseProvider.MSSQL",
  "siteDatabaseName": "SiteA_SDB",
  "siteDatabaseWorkShareLocation": "LocationX",
  "nameRuleIdentifier": "1",
  "nameGeneratorServer": "ServerName",
  "templateFilePath": "\\fileShare\\DB\\Backups\\AppRepos.dat",
  "overwriteExistingDatabase": true,
  "siteAlias": "MySiteAlias"
}
```

**Parámetros**:

| Parameter                     | Type                                  | Description                                                                  |
| ----------------------------- | ------------------------------------- | ---------------------------------------------------------------------------- |
| databaseServer                | string                                | Nombre del servidor donde se creará la base de datos del sitio               |
| databaseProvider              | Com.Ingr.S3dAdmin.V1.DatabaseProvider | Cualquier valor del tipo enumerado DatabaseProvider                          |
| siteDatabaseName              | string                                | Nombre de la base de datos para el sitio que se creará                       |
| siteDatabaseWorkShareLocation | string                                | Nombre de la Location                                                        |
| nameRuleIdentifier            | string                                | Identificador de la regla de nombres                                         |
| nameGeneratorServer           | string                                | Nombre del servidor NameGenerator a utilizar para este sitio                 |
| templateFilePath              | string                                | Ruta de archivo completa (UNC) de la plantilla de la base de datos del sitio |
| overwriteExistingDatabase     | boolean                               | Indica si se sobrescribirá la base de datos si ya existe                     |
| siteAlias                     | string                                | Alias que se usará para referirse a este sitio                               |

> Asegúrate de que las rutas UNC que especifiques tengan la configuración requerida. Para más información, consulta [Required Configuration for File Paths](#configuración-requerida-para-rutas-de-archivos).

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### RestoreSite

Restaura un sitio a partir de los datos proporcionados. Por ejemplo, la siguiente URL restaura un sitio a partir de respaldos y otros datos:

```
POST <ServiceRoot>/RestoreSite

{
  "backupConfigurationFilePath": "\\\\fileShare\\DBBackups\\backup.bcf",
  "databaseProvider": "Com.Ingr.S3dAdmin.V1.DatabaseProvider.MSSQL",
  "databaseServer": "ProductionServer1",
  "backupFilesPath": "\\\\fileShare\\DBBackups\\",
  "siteDatabaseName": "SiteA_SDB",
  "siteDatabaseWorkShareLocation": "Location",
  "nameGeneratorServer": "ServerName",
  "overwriteExistingDatabase": true,
  "siteAlias": "MySiteAlias"
}
```

**Parámetros**:

| Parameter                  | Type                                               | Description                                                                                  |
|---------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------|
| databaseProvider          | Com.Ingr.S3dAdmin.V1.DatabaseProvider              | Cualquier valor del tipo enumerado DatabaseProvider                                          |
| databaseServer            | string                                             | Nombre del servidor donde se restaurará la base de datos del sitio                           |
| backupFilesPath           | string                                             | Ruta UNC accesible que contiene los archivos de respaldo                                     |
| siteDatabaseName          | string                                             | Nombre de la base de datos para el sitio que se está restaurando                             |
| siteDatabaseWorkShareLocation | string                                         | Nombre de la Location                                                                        |
| nameGeneratorServer       | string                                             | Nombre del servidor NameGenerator a utilizar para este sitio                                |
| overwriteExistingDatabase | boolean                                            | Indica si se sobrescribirá la base de datos si ya existe                                     |
| siteAlias                 | string                                             | Alias que se usará para referirse a este sitio                                              |

Asegúrate de que las rutas UNC que especifiques tengan la configuración requerida. Para más información, consulta [Required Configuration for File Paths](#configuración-requerida-para-rutas-de-archivos).

La propiedad `UpToDate` en la respuesta ayuda a entender si se requiere una actualización. Si la propiedad `UpToDate` se establece en `false`, se rellena una propiedad abierta `Status` con la información detallada del estado actual de la entidad `Plant` devuelta.

#### UpgradeSite

Actualiza un sitio dado a la última versión disponible. Por ejemplo, la siguiente URL actualiza una entidad `Site`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/UpgradeSite
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### CreateCatalog

Crea un `Catalog` en un `Site` especificado, basándose en los datos proporcionados. Por ejemplo, la siguiente URL crea y devuelve una entidad `Catalog` usando los detalles mencionados en el cuerpo:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/CreateCatalog

{
  "databaseServer": "ProductionServer1",
  "catalogDatabaseName":"PlantA_CDB",
  "sharedContentPath":"\\\\fileshare\\SharedContent",
  "templateFilePath":"\\\\fileshare\\DBBackups\\CatalogDb.dat",
  "overwriteExistingDatabase":"true"
}
```

**Parámetros**:

| Parameter                  | Type    | Description                                                                           |
|---------------------------|---------|---------------------------------------------------------------------------------------|
| databaseServer            | String  | Nombre del servidor donde se creará la base de datos del catálogo                    |
| catalogDatabaseName       | String  | Nombre de la base de datos del catálogo                                              |
| sharedContentPath         | String  | Ruta UNC accesible que contiene el "symbol share"                                     |
| templateFilePath          | String  | Ruta de archivo completa (UNC) de la plantilla de la base de datos del catálogo       |
| overwriteExistingDatabase | Boolean | Indica si se sobrescribirá la base de datos si ya existe                              |

Asegúrate de que las rutas UNC que especifiques tengan la configuración requerida.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### CreatePlant

Crea una `Plant` en un `Site` especificado, a partir de los datos proporcionados. Por ejemplo, la siguiente URL crea y devuelve una entidad `Plant` usando los detalles mencionados en el cuerpo:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/CreatePlant

{
  "catalogDatabaseName":"CatalogA",
  "plantName":"PlantA",
  "modelDatabaseServer":"ServerA",
  "modelDatabaseName":"PlantA_MDB",
  "reportsDatabaseServer":"ServerA",
  "reportsDatabaseName":" PlantA_RDB",
  "nameGeneratorServer": "NameGeneratorServerName",
  "modelType": "Com.Ingr.S3dAdmin.V1.ModelType.Plant",
  "modelPurpose": "Com.Ingr.S3dAdmin.V1.ModelPurpose.Standard"
}
```

**Parámetros**:

| Parameter            | Type                                         | Description                                                                      |
|----------------------|----------------------------------------------|----------------------------------------------------------------------------------|
| catalogDatabaseName  | String                                       | Nombre de la base de datos de catálogo relacionada con la planta que se crea    |
| plantName            | String                                       | Nombre de la Plant que se va a crear                                            |
| modelDatabaseServer  | String                                       | Nombre del servidor donde se creará la base de datos de la planta               |
| modelDatabaseName    | String                                       | Nombre de la base de datos de la planta                                         |
| reportsDatabaseServer| String                                       | Nombre del servidor donde se creará la base de datos de reportes                |
| reportsDatabaseName  | String                                       | Nombre de la base de datos que se utilizará para los reportes                   |
| nameGeneratorServer  | String                                       | Nombre del servidor NameGenerator que se usará para esta planta                 |
| modelType            | Com.Ingr.S3dAdmin.V1.ModelType               | Valor del tipo enumerado ModelType                                              |
| modelPurpose         | Com.Ingr.S3dAdmin.V1.ModelPurpose            | Valor del tipo enumerado ModelPurpose                                           |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### RestorePlant

Restaura una `Plant` en un `Site` especificado, a partir de los datos proporcionados. Por ejemplo, la siguiente URL restaura y devuelve una entidad `Plant` usando los detalles mencionados en el cuerpo:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/RestorePlant

{
  "plantName":"PlantA",
  "backupConfigurationFilePath": "\\\\fileShare\\DBBackups\\backup.bcf",
  "databaseServer": "ProductionServer1",
  "backupFilesPath": "\\\\fileShare\\DBBackups\\",
  "sharedContentPath":"\\\\fileshare\\SharedContent",
  "nameGeneratorServer": "ServerName",
  "overwriteExistingDatabase": true,
  "linkIfCatalogExist": false
}
```

**Parámetros**:

| Parameter                  | Type    | Description                                                                                    |
|---------------------------|---------|------------------------------------------------------------------------------------------------|
| plantName                 | String  | Nombre de la Plant a restaurar                                                                 |
| backupConfigurationFilePath | String| Ruta de archivo completa (UNC) del archivo bcf                                                 |
| databaseServer            | String  | Nombre del servidor donde se restaurará la base de datos de la planta                          |
| backupFilesPath           | String  | Ruta UNC accesible que contiene los archivos de respaldo                                       |
| sharedContentPath         | String  | Ruta UNC accesible que contiene el "symbol share"                                              |
| nameGeneratorServer       | String  | Nombre del servidor NameGenerator que se usará para esta planta                                |
| overwriteExistingDatabase | Boolean | Indica si se sobrescribirá la base de datos si ya existe                                       |
| linkIfCatalogExist        | Boolean | Indica si se debe vincular con el catálogo existente si ya existe                              |

Asegúrate de que las rutas UNC que especifiques tengan la configuración requerida.  
La propiedad `UpToDate` en la respuesta ayuda a entender si se requiere una actualización. Si `UpToDate` es `false`, se rellena la propiedad abierta `Status` con la información detallada del estado actual de la entidad `Plant`.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### RestorePlantAsCopy

En un sitio dado, restaura una `Plant` existente como copia de planta a partir de los datos proporcionados. Por ejemplo, la siguiente URL crea y devuelve una entidad `Plant` usando los detalles en el cuerpo:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/RestorePlantAsCopy

{
  "plantToRestore":"PlantA",
  "newPlantName":"PlantACopy",
  "catalogDatabaseName":"PlantA_CDB",
  "modelDatabaseName": "PlantA_MDB",
  "backupConfigurationFilePath": "\\\\fileshare\\DBBackups\\backup.bcf",
  "backupFilesPath": "\\\\fileshare\\DBBackups\\",
  "databaseServer": "ProductionServer1",
  "sharedContentPath":"\\\\fileshare\\SharedContent",
  "nameGeneratorServer": "ServerName",
  "linkIfCatalogExist": true
}
```

**Parámetros**:

| Parameter                  | Type    | Description                                                                    |
|---------------------------|---------|--------------------------------------------------------------------------------|
| plantToRestore            | String  | Nombre de la planta existente a copiar                                         |
| newPlantName              | String  | Nombre de la nueva planta                                                      |
| catalogDatabaseName       | String  | Nombre de la base de datos de catálogo relacionada con la planta que se restaura |
| modelDatabaseName         | String  | Nombre de la base de datos del modelo que se usará para esta planta            |
| backupConfigurationFilePath | String| Ruta de archivo completa (UNC) del archivo bcf                                 |
| backupFilesPath           | String  | Ruta UNC accesible que contiene los archivos de respaldo                       |
| databaseServer            | String  | Nombre del servidor donde se restaurará la base de datos de la planta          |
| sharedContentPath         | String  | Ruta UNC accesible que contiene el "symbol share"                              |
| nameGeneratorServer       | String  | Nombre del servidor NameGenerator para esta planta                             |
| linkIfCatalogExist        | Boolean | Indica si se debe vincular al catálogo existente, si existe                    |

Asegúrate de que las rutas UNC especificadas tengan la configuración requerida.  
La propiedad `UpToDate` en la respuesta ayuda a entender si se requiere una actualización. Si `UpToDate` es `false`, se rellena `Status` con información detallada del estado actual de la `Plant`.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### RestorePlantForSelectiveRecovery

Restaura una `Plant` para la recuperación selectiva de objetos de modelo con los datos proporcionados. Por ejemplo, la siguiente URL crea y devuelve una entidad `Plant`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/RestorePlantForSelectiveRecovery

{
  "plantToRestore":"PlantA",
  "newPlantName ":"PlantSR",
  "catalogDatabaseName":"PlantSR_CDB",
  "modelDatabaseName": "PlantSR_MDB",
  "backupConfigurationFilePath": "\\\\fileshare\\DBBackups\\backup.bcf",
  "backupFilesPath": "\\\\fileshare\\DBBackups\\",
  "databaseServer": "ProductionServer1",
  "linkIfCatalogExist": true
}
```

**Parámetros**:

| Parameter                  | Type    | Description                                                                 |
|---------------------------|---------|-----------------------------------------------------------------------------|
| plantToRestore            | String  | Nombre de la planta de la cual restaurar la información                     |
| newPlantName              | String  | Nombre de la nueva planta a crear                                           |
| catalogDatabaseName       | String  | Nombre de la base de datos de catálogo relacionada con la planta que se restaura |
| modelDatabaseName         | String  | Nombre de la base de datos del modelo que se usará para esta planta         |
| backupConfigurationFilePath | String| Ruta de archivo completa (UNC) del archivo bcf                              |
| databaseServer            | String  | Nombre del servidor donde se restaurará la base de datos del sitio          |
| backupFilesPath           | String  | Ruta UNC accesible que contiene los archivos de respaldo                    |
| linkIfCatalogExist        | Boolean | Indica si se debe vincular al catálogo existente, si ya existe              |

Asegúrate de que las rutas UNC especificadas tengan la configuración requerida.  
La propiedad `UpToDate` en la respuesta ayuda a entender si se requiere una actualización. Si `UpToDate` es `false`, se rellena `Status` con la información detallada del estado actual de la entidad `Plant`.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### UpgradeCatalog

Actualiza un `Catalog` dado en un `Site` especificado. Por ejemplo, la siguiente URL actualiza y devuelve una entidad `Catalog`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA_CDB’)/UpgradeCatalog
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### UpgradePlant

Actualiza una `Plant` dada en un `Site` especificado. Por ejemplo, la siguiente URL actualiza y devuelve una entidad `Plant`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/UpgradePlant
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### RegenerateReports

Regenera la base de datos de reportes asociada a una planta dada. Por ejemplo, la siguiente URL regenera y devuelve un string de estado:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘MyPlant’)/RegenerateReports
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### BackupPlant

Realiza un respaldo (backup) de una `Plant` dada, basándose en los datos proporcionados. Por ejemplo, la siguiente URL respalda una `Plant` en un `Site` usando los detalles en el cuerpo:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/BackupPlant

{
  "backupConfigurationFilePath":"\\\\fileshare\\DBBackups\\BackupPlant.bcf",
  "backupFilesPath":"\\\\fileshare\\DBBackups\\",
  "overwriteExistingFile":true
}
```

**Parámetros**:

| Parameter                  | Type    | Description                                                                     |
|---------------------------|---------|---------------------------------------------------------------------------------|
| backupConfigurationFilePath | String| Ruta de archivo completa (UNC) del archivo bcf                                  |
| backupFilesPath           | String  | Ruta UNC accesible que contiene los archivos de respaldo                        |
| overwriteExistingFile     | Boolean | Indica si se sobrescribirá el archivo si ya existe                              |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### BackupPlants

Realiza un respaldo de una colección de plantas en un `Site` dado, basándose en los datos proporcionados. Por ejemplo, la siguiente URL respalda un conjunto de `Plant` en un `Site` usando los detalles en el cuerpo:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/BackupPlants

{
  "plantIds":[ "PlantA", "PlantB" ],
  "backupConfigurationFilePath":"\\\\fileshare\\DBBackups\\Multiple\\BackupPlants.bcf",
  "backupFilesPath":"\\\\fileshare\\DBBackups\\Multiple\\",
  "overwriteExistingFile":true
}
```

**Parámetros**:

| Parameter                  | Type             | Description                                             |
|---------------------------|------------------|---------------------------------------------------------|
| plantIds                  | Collection<string> | Conjunto de plantas a respaldar                         |
| backupConfigurationFilePath | String        | Ruta de archivo completa (UNC) del archivo bcf          |
| backupFilesPath           | String           | Ruta UNC accesible que contiene los archivos de respaldo|
| overwriteExistingFile     | Boolean          | Indica si se sobrescribirá el archivo si ya existe      |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### EnableSite

Habilita el acceso de la Web API para un sitio restaurado existente basándose en los datos proporcionados. Por ejemplo, la siguiente URL habilita el acceso a una entidad `Site`:

```
POST
<ServiceRoot>/EnableSite

{
  "siteAlias": "TestSiteAlias",
  "databaseProvider": "Com.Ingr.S3dAdmin.V1.DatabaseProvider.MSSQL",
  "databaseServer": "ProductionServer1",
  "siteDatabaseName": "SiteA_SDB"
}
```

**Parámetros**:

| Parameter         | Type                                    | Description                                                   |
|-------------------|-----------------------------------------|---------------------------------------------------------------|
| siteAlias         | String                                  | Alias que se usará para referirse a este sitio               |
| databaseProvider  | Com.Ingr.S3dAdmin.V1.DatabaseProvider    | Cualquier valor del tipo enumerado DatabaseProvider           |
| databaseServer    | String                                  | Nombre del servidor en el que existe el sitio                 |
| siteDatabaseName  | String                                  | Nombre de la base de datos del sitio que se está habilitando  |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### DisableSite

Deshabilita un sitio para el acceso de la Web API. Por ejemplo, la siguiente URL deshabilita el acceso de la Web API para un sitio dado. Esta acción también se encargará de eliminar todas las plantas habilitadas para ejecutarse en todas las máquinas:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/DisableSite
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### EnablePlant

Habilita una planta para el acceso tanto de la Plant API como de la Admin API. Este proceso asigna un servicio backend dedicado para procesar todas las solicitudes futuras en la planta:

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants('PlantA')/EnablePlant
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### EnablePlants

Habilita una o más plantas para el acceso tanto de la Admin API como de la Plant API. Este proceso asigna un servicio backend dedicado para cada uno de los nombres de planta suministrados como parámetro de entrada:

```
POST
<ServiceRoot>/Sites('MySiteAlias')/EnablePlants

{
  "plantIds": ["PlantA", "PlantB"]
}
```

**Parámetros**:

| Parameter | Type                | Description                                   |
|-----------|---------------------|-----------------------------------------------|
| plantIds  | Collection<string>  | Nombres de las plantas válidas a habilitar.   |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
#### DisablePlants

Deshabilita las plantas de la configuración de Web API. Esto elimina el servicio backend dedicado que se había asignado a cada uno de los nombres de planta proporcionados como parámetro de entrada:

```
POST
<ServiceRoot>/Sites('MySiteAlias')/DisablePlants

{
  "plantIds": ["PlantA", "PlantB"]
}
```

**Parámetros**:

| Parameter | Type                | Description                                    |
|-----------|---------------------|------------------------------------------------|
| plantIds  | Collection<string>  | Nombres de las plantas válidas a deshabilitar. |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### RegeneratePlantMetadata

Regenera los metadatos para las plantas dadas. Internamente, esto lleva las propiedades definidas por el usuario (bulkloaded o definidas en Smart3D) al metadata de la web API como propiedades estructurales. Esto permite al usuario filtrar plantas basándose en estas propiedades. La operación PATCH está soportada para estas propiedades.

```
POST
<ServiceRoot>/Sites('MySiteAlias')/RegeneratePlantMetadata

{
  "plantIds": ["PlantA", "PlantB"]
}
```

**Parámetros**:

| Parameter | Type                | Description                                                  |
|-----------|---------------------|--------------------------------------------------------------|
| plantIds  | Collection<string>  | Nombres de las plantas válidas para las cuales se regenerará el metadata. |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### UpsertSioInterface

Crea o actualiza una interfaz Smart Interop Object (SIO).

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/UpsertSioInterface

{
  "sioData": "{
    'Name':'SioInterfaceName',
    'Id':'SioInterfaceId',
    'DisplayName':'SioInterfaceDisplayName',
    'Version':'1.2',
    'DataFlows':['DataFlows.0','DataFlows.11']
  }"
}
```

**Parámetros**:

| Parameter | Type   | Description                                                   |
|-----------|--------|---------------------------------------------------------------|
| sioData   | String | Datos para crear o actualizar la interfaz SIO                |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### UpsertSioProperty

Crea o actualiza una propiedad en una interfaz Smart Interop Object (SIO).

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/UpsertSioProperty

{
  "sioData": "{
    'Name':'SioPropertyName',
    'Id':'SioPropertyId',
    'DisplayName':'SioPropertyDisplayName',
    'Version':'1.2',
    'Type':'Int',
    'ExposedBy':'SioInterfaceName'
  }"
}
```

Si el tipo de la propiedad es `EnumList`, debes incluir también la propiedad `ScopedByList`, que es el ID del `SioList`. Por ejemplo:

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/UpsertSioProperty

{
  "sioData": "{
    'Name':'SioPropertyName',
    'Id':'SioPropertyId',
    'DisplayName':'SioPropertyDisplayName',
    'Version':'1.2',
    'Type':'EnumList',
    'ExposedBy':'SioInterfaceName',
    'ScopedByList':'SioListId'
  }"
}
```

**Parámetros**:

| Parameter | Type   | Description                                                                |
|-----------|--------|----------------------------------------------------------------------------|
| sioData   | String | Datos para crear o actualizar la propiedad en la interfaz SIO              |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### UpsertSioList

Crea o actualiza una lista en un Smart Interop Object (SIO).

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/UpsertSioList

{
  "sioData": "{
    'Name':'SioListName',
    'DisplayName':'SioListDisplayName',
    'Id':'SioListId',
    'Version':'1.2'
  }"
}
```

**Parámetros**:

| Parameter | Type   | Description                                            |
|-----------|--------|--------------------------------------------------------|
| sioData   | String | Datos para crear o actualizar la lista                 |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### UpsertSioListItem

Crea o actualiza un ítem en una lista Smart Interop Object (SIO).

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/UpsertSioListItem

{
  "sioData": "{
    'Name':'SioListItemName',
    'Id':'SioListItemId',
    'Value':123,
    'Version':'1.2',
    'ParentList':'SioListId'
  }"
}
```

**Parámetros**:

| Parameter | Type   | Description                                                                 |
|-----------|--------|-----------------------------------------------------------------------------|
| sioData   | String | Datos para crear o actualizar el ítem en la lista SIO                        |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### DeleteSioInterface

Elimina una interfaz Smart Interop Object (SIO).

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/DeleteSioInterface

{
  "id":"interfaceId"
}
```

**Parámetros**:

| Parameter | Type   | Description                                      |
|-----------|--------|--------------------------------------------------|
| id        | String | ID de la interfaz a eliminar                     |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### DeleteSioProperty

Elimina una propiedad de una interfaz Smart Interop Object (SIO).

> Elimina primero la lista asociada con la propiedad antes de eliminar la propiedad.

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/DeleteSioProperty

{
  "id":"propertyId",
  "exposedby":"interfaceId"
}
```

**Parámetros**:

| Parameter | Type   | Description                                               |
|-----------|--------|-----------------------------------------------------------|
| id        | String | ID de la propiedad a eliminar                             |
| exposedBy | String | ID de la interfaz de la que se eliminará la propiedad     |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### DeleteSioListItem

Elimina un ítem de una lista Smart Interop Object (SIO).

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/DeleteSioListItem

{
  "id":"listItemId",
  "parentId":"parentListId"
}
```

**Parámetros**:

| Parameter | Type   | Description                                                      |
|-----------|--------|------------------------------------------------------------------|
| id        | String | ID del ítem a eliminar de la lista                              |
| parentId  | String | ID de la lista de la que se eliminará el ítem                   |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

#### DeleteSioList

Elimina una lista de un Smart Interop Object (SIO).

```
POST
<ServiceRoot>/Sites('MySiteAlias')/Plants(‘PlantA’)/DeleteSioList

{
  "id":"listId"
}
```

**Parámetros**:

| Parameter | Type   | Description               |
| --------- | ------ | ------------------------- |
| id        | String | ID de la lista a eliminar |

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^functions
### Functions

Todas las funciones definidas en Smart3D Admin Web API se utilizan para obtener la respuesta de las acciones asíncronas. Las funciones se definen en el formato:

```
Get<ActionName>ResultAsync
```

Por ejemplo, la acción `RestorePlant` tendrá definida la función `GetRestorePlantResultAsync`.

Estas funciones toman `resultId` como argumento y devuelven la respuesta de la solicitud asíncrona si se completó el procesamiento. Si no, devuelve `202 Accepted`.

Por ejemplo, cuando se ejecuta una solicitud de acción `RestorePlant` de manera asíncrona, la respuesta será `202 Accepted` con un header `Location`. Este header contendrá una URI que es la solicitud de la función de resultado correspondiente:

```
GET
<ServiceRoot>/Sites('13')/GetRestorePlantResultAsync(resultId='0c8954c7-d914-4ff7-ab85-dee6234bc564')
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^write-operations
### Write operations

El acceso a las operaciones de escritura también está más restringido que las operaciones de lectura.  

Es posible que no tengas acceso a las operaciones de escritura dependiendo de la licencia adquirida por tu organización y de la configuración de OAuth realizada por tus administradores. Por favor, consulta con tu administrador para determinar si deberías tener privilegios, o si recibes un `403 (Forbidden)` al intentar una solicitud de escritura que crees que deberías poder realizar.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^creating-entities
## Creating entities

El usuario puede crear entidades `PermissionGroup`, `PermissionGroupFolder` y `AccessControlRule` en la `Plant/Catalog`. La entidad `Location` se puede crear solo en la base de datos del `Site`.  

El usuario también puede crear las siguientes entidades de meta-esquema: `Interface`, `Property`, `List` y `ListItem`.

### PermissionGroupFolder

Para crear un `PermissionGroupFolder` en `Plant/Catalog` con el padre como `Plant/Catalog/PermissionGroupFolder`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/PermissionGroupFolderChildren

{
  "Name": "name of PG Folder to be created"
}
```

Del mismo modo, para crear un `PermissionGroupFolder` bajo una carpeta en un `Catalog`:

```
<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA’)/PermissionGroupFolderChildren(‘CatalogPGRootFolder’)/ChildFolders("Level1 Folder")/ChildFolders

{
  "Name": "name of PG Folder to be created"
}
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### PermissionGroup

Para crear un `PermissionGroup` en `Plant/Catalog` bajo un `PermissionGroupFolder`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/PermissionGroupFolderChildren(‘PGRootFolder’)/PermissionGroups

{
  "Name": "name of PG to be created",
  "Location@odata.bind": "<ServiceRoot>/Sites(‘MySiteAlias’)/Locations(‘id’)"
}
```

Si conocemos los `AccessControlRules` que se asociarán al `PermissionGroup`, se pueden proporcionar así:

```
{
  "Name": "name of PG to be created",
  "Location@odata.bind": "Sites(‘MySiteAlias’)/Locations(‘id’)",
  "AccessControlRules": [
    {"Role":"domain\\user1","AccessRight": "Com.Ingr.S3dadmin.V1.Write" },
    {"Role": "domain\\user2", "AccessRight": "Com.Ingr.S3dadmin.V1.Read" }
  ]
}
```

Al menos uno de los `AccessControlRule` especificados debe tener `Write` o `FullAccess` para crear con éxito el `PermissionGroup`.  

Si no se proporcionan `AccessControlRules`, se creará automáticamente una regla de acceso con el usuario del token y se otorgará `FullAccess`. Cuando se especifican, no se crea la regla para el usuario del token.

Del mismo modo, para crear un `PermissionGroup` bajo una carpeta en un `Catalog`:

```
<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA’)/PermissionGroupFolderChildren(‘CatalogPGRootFolder’)/ChildFolders("Level1 Folder")/PermissionGroups

{
  "Name": "name of PG to be created",
  "Location@odata.bind": Sites(‘MySiteAlias’)/Locations(‘id’)"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### AccessControlRule

Para crear una entidad `AccessControlRule` en el `PermissionGroup` de una `Plant/Catalog`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/PermissionGroupFolderChildren(‘PGRootFolder’)/PermissionGroups(‘PG1’)/AccessControlRules
```

O

```
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/PermissionGroups(‘PG1’)/AccessControlRules

{
  "Role": "domain\\username",
  "AccessRight": "Com.Ingr.S3dAdmin.V1.AccessRight.FullAccess"
}
```

Del mismo modo, para crear un `AccessControlRule` en un `PermissionGroup` de un `Catalog`:

```
<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA’)/PermissionGroupFolderChildren(‘CatalogPGRootFolder’)/ChildFolders("Level1 Folder")/PermissionGroups(‘PG1’)/AccessControlRules

{
  "Role": "domain\\username",
  "AccessRight": "Com.Ingr.S3dAdmin.V1.AccessRight.Write"
}
```

Para crear un `AccessControlRule` al nivel de `Plant/Catalog`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/AccessControlGroups

Or

<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA’)/AccessControlGroups

{
  "Role": "domain\\username",
  "AccessRight": "Com.Ingr.S3dAdmin.V1.AccessRight.FullAccess"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Interface

Para crear una entidad `Interface` en la base de datos de esquema de `Smart3D Catalog`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces

{
  "Name": "ICustomInterface",
  "Description": "Custom Interface"
}
```

Opcionalmente, el usuario puede proporcionar valores para las Navigation properties relacionadas para crearlas o vincularlas:

```
{
  "Name": "ICustomInterface",
  "InterfaceMap": {
    "Mappings": ["IObject"]
  },
  "Properties": [
    {
      "Name": "CustomPropertyString",
      "Type": "String"
    },
    {
      "Name": "CustomPropertyInt",
      "Type": "Int"
    }
  ],
  "RealizedByClasses@odata.bind": [
    "Sites('{{AdminSiteAlias}}')/Plants('{{AdminPlantName}}')/Classes('EQUIP:CPEquipment')",
    "Sites('{{AdminSiteAlias}}')/Plants('{{AdminPlantName}}')/Classes('EQUIP:CPSmartEquipment')"
  ]
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Property

Para crear una entidad `Property` en la `Interface` de la base de datos de esquema de `Smart3D Catalog`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces(‘ICustomInterface’)/Properties

{
  "Name": " CustomPropertyEnum",
  "Type": "EnumList"
}
```

Opcionalmente, se pueden proporcionar valores para las Navigation properties relacionadas para crearlas o vincularlas:

```
{
  "Name": "CustomPropertyEnum",
  "Type": "EnumList",
  "PropertyMap":{
    "Mappings":["MappedProperty"]
  },
  "ScopedByList@odata.bind": "Sites('{{AdminSiteAlias}}')/Plants('{{AdminPlantName}}')/Lists(‘UDP:T:CustomCodeList’)"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### List

Para crear una entidad `List` en la base de datos de esquema de `Smart3D Catalog`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists

{
  "Name": " CustomCodeList",
  "DisplayName": "Custom list",
  "Description": "Custom list for the new custom property"
}
```

Opcionalmente, se pueden proporcionar valores para las Navigation properties relacionadas para crearlas o vincularlas:

```
{
  "Name": " CustomCodeList",
  "DisplayName": "Custom list",
  "Description": "Custom list for the new custom property",
  "Items": [
    {
      "Name":"Item1",
      "DisplayName":"Code list Item1",
      "AConversion": 0.0,
      "BConversion": 0.0,
      "Value": 1
    },
    {
      "Name":"Item2",
      "DisplayName":"Item2",
      "AConversion": 0.0,
      "BConversion": 0.0,
      "Value": 2
    }
  ]
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### ListItem

Para crear una entidad `ListItem` en la base de datos de esquema de `Smart3D Catalog`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists(‘CustomCodeList’)/Items

{
  "Name":"Item1",
  "DisplayName":"Code list Item1",
  "AConversion": 0.0,
  "BConversion": 0.0,
  "Value": 1
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Location

Para crear una entidad `Location` en el `Site`:

```
POST
<ServiceRoot>/Sites(‘MySiteAlias’)/Locations

{
  "Name":"Location Name",
  "SiteServer":"Location Server",
  "NameRuleIdentifier":"Location Name Rule Id"
}
```

---
^changing-relationships
## Changing relationships

Se admiten las siguientes combinaciones para cambiar relaciones:

- PermissionGroup > Location  
- Class > Interface & Interface > Class  
- Property > List/UomList  
- List > List o UomList - UomList (Child/Parent)

### PermissionGroup > Location

El usuario puede cambiar la `Location` de la entidad `PermissionGroup`, como se muestra a continuación:

```
POST/PUT
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/PermissionGroupFolderChildren(‘PGRootFolder’)/PermissionGroups(‘PG1’)/Location/$ref

{
  "@odata.id": "<ServiceRoot>/Sites(‘MySiteAlias’)/Locations(‘id of new Location to be associated with’)"
}
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Class > Interface

El usuario puede asociar una `Interface` a una `Class` de dos formas (Class > Interface y Interface > Class):

```
POST/PUT [Class to Interface]
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Classes(‘ClassId’)/Realizes/$ref

{
  "@odata.id": "<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces(‘InterfaceId’)"
}
```

```
POST/PUT [Interface to Class]
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interface(‘InterfaceId’)/RealizedByClasses/$ref

{
  "@odata.id":"<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Classes(‘ClassId’)"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Property > List/UomList

El usuario puede asociar una `Property` de una `Interface` a una entidad `List` (o `UomList`) solo durante la creación.

Después de la creación, el sistema no permite al usuario agregar o modificar la asociación entre `Property` y `List` (o `Property` y `UomList`). Para cambiar la asociación, se debe eliminar la `Property` y volver a crearla con la `List/UomList` correcta.

```
POST/PUT
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces(‘InterfaceId)/Properties(‘PropertyId’)/ScopedByList/$ref

{
  "@odata.id": "<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists(‘ListId’)"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### List > List o UomList > UomList (Child/Parent)

El usuario puede asociar una `List` a otra `List` (o `UomList` a otro `UomList`) como `Child` o `Parent`.

Después de la creación, el sistema no permite cambiar el `parent/child` de una `List` o `UomList`. Para cambiar la asociación `parent/child`, se debe eliminar la `List` o `UomList` y volver a crearla con el `parent/child` correcto.

```
POST/PUT [List to Parent List]
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists(‘ListId’)/Parent/$ref

{
  "@odata.id": "<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists(‘ParentListId’)"
}
```

```
POST/PUT [List to Child List]
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists(‘ListId’)/Descendant/$ref

{
  "@odata.id": "<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists(‘ChildListId’)"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^updating-single-entities
## Updating single entities

La actualización de una o más propiedades de un solo elemento se realiza a través de una solicitud PATCH. Esta solicitud sigue los estándares HTTP y OData. Las propiedades que se pueden actualizar se describen a continuación.

> El método PATCH se puede usar para modificar propiedades definidas por el usuario en Smart3D que no sean de solo lectura en estas entidades. Para ello, debes regenerar el metadata para las plantas usando la acción `RegeneratePlantMetadata`.

### Site

**Propiedad soportada**:
- `NameGeneratorServer`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)

{
  "NameGeneratorServer": "name of the NameGenerator server to be used"
}
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Catalog

**Propiedad soportada**:
- `SharedContentPath`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA’)

{
  "SharedContentPath": "C:\\SharedContentPath"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### Plant

**Propiedades soportadas**:
- `Description`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)

{
  "Description": "description of the plant"
}
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Model

**Propiedades soportadas**:
- `Purpose`
- `Type`
- `NameGeneratorServer`

**Ejemplo 1**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Model

{
  "Type": "Com.Ingr.S3dAdmin.V1.ModelType.Plant",
  "Purpose": "Com.Ingr.S3dAdmin.V1.ModelPurpose.ChangeManagement"
}
```

**Ejemplo 2**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Model

{
  "NameGeneratorServer": "name of the NameGenerator server to be used"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### Location

**Propiedad soportada**:
- `Name`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Locations(‘LocId’)

{
  "Name": "name of Location"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### PermissionGroupFolder

**Propiedad soportada**:
- `Name`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA’)/
PermissionGroupFolderChildren(‘CatalogPGRootFolder’)/
ChildFolders("Level1 Folder")

{
  "Name": "name of PG Folder"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### PermissionGroup

**Propiedad soportada**:
- `Name`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/
PermissionGroupFolderChildren(‘PlantPGRootFolder’)/
ChildFolders("Level1 Folder")/PermissionGroups(‘PG’)

{
  "Name": "name of PG"
}
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### AccessControlRule

**Propiedad soportada**:
- `AccessRight`

**Ejemplo 1**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs
(‘CatalogA’)/AccessControlGroups(‘AccessRuleId’)

{
  "AccessRight": "Com.Ingr.S3dAdmin.V1.AccessRight.Read"
}
```

**Ejemplo 2**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/
PermissionGroupFolderChildren(‘PlantGRootFolder’)/
ChildFolders("Level1 Folder")/PermissionGroups(‘PG1’)/
AccessControlRules

{
  "AccessRight": "Com.Ingr.S3dAdmin.V1.AccessRight.FullAccess"
}
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Interface

**Propiedades soportadas**:
- `Description`
- `DisplayName`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants
(‘PlantA’)/Interfaces(‘ICustomInterface’)

{
  "Description": "Modified description of the interface",
  "DisplayName": "Modified interface name"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### InterfaceMap

**Propiedades soportadas**:
- `SelectCriterion`
- `ProcessCriterion`
- `Mappings`

**Ejemplo** (similar al de Interface, añadiendo campos de `InterfaceMap`):

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants
(‘PlantA’)/Interfaces(‘ICustomInterface’)

{
  "Description": "Modified description of the interface",
  "DisplayName": "Modified interface name"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Property

**Propiedades soportadas**:
- `Description`
- `DisplayName`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants
(‘PlantA’)/Interfaces(‘ICustomInterface’)/Properties(‘CustomProperty’)

{
  "Description": "Modified description of the property",
  "DisplayName": "Modified property name"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### PropertyMap

**Propiedades soportadas**:
- `Ignored`
- `SelectCriterion`
- `ProcessCriterion`
- `Mappings`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants
(‘PlantA’)/Interfaces(‘ICustomInterface’)/Properties(‘CustomProperty’)/PropertyMap

{
  "Ignored": true
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### List

**Propiedades soportadas**:
- `Description`
- `DisplayName`
- `IsOpenList`
- `IsImmutableList`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants
(‘PlantA’)/Lists(‘CustomList’)

{
  "Description": "Modified description of the list",
  "DisplayName": "Modified list name"
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### ListMap

**Propiedades soportadas**:
- `Ignored`
- `SelectCriterion`
- `ProcessCriterion`
- `Mappings`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants
(‘PlantA’)/Lists(‘CustomList’)/ListMap

{
  "Ignored": true
}
```


<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### ListItem

**Propiedades soportadas**:
- `Description`
- `DisplayName`
- `AConversion`
- `BConversion`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants
(‘PlantA’)/Lists(‘CustomList’)/ListItem(‘ItemId’)

{
  "AConversion": 1.0
}
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### ListItemMap

**Propiedades soportadas**:
- `Ignored`
- `SelectCriterion`
- `ProcessCriterion`
- `Mappings`

**Ejemplo**:

```
PATCH
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants
(‘PlantA’)/Lists(‘CustomList’)/ListItem(‘ItemId’)/ListItemMap

{
  "Ignored": true
}
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^deleting-entities
## Deleting entities

El usuario puede eliminar las siguientes entidades:

- PermissionGroupFolder  
- PermissionGroup  
- AccessControlRule  
- Location  
- Interface  
- Property  
- List  
- ListItem  

### PermissionGroupFolder

Para eliminar un `PermissionGroupFolder`, primero se debe asegurar que no haya entidades `PermissionGroup` asociadas en los hijos.

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/PermissionGroupFolderChildren(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA’)/PermissionGroupFolderChildren(‘parentFolderId’)/ChildFolders(‘idToDelete’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### PermissionGroup

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/PermissionGroups(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA)/PermissionGroupFolderChildren(‘parentFolderId’)/ChildFolders(‘folderId’)/PermissionGroups(‘idToDelete’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### AccessControlRule

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/AccessControlGroups(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/PermissionGroups(‘id’)/AccessControlRules(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘catalogA’)/AccessControlGroups(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Catalogs(‘CatalogA’)/PermissionGroups(‘id’)/AccessControlRules(‘idToDelete’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### Location

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Locations(‘idToDelete’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### Interface

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Classes(‘classId’)/Realizes(‘idToDelete’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### Property

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces(‘interfaceId’)/Properties(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Classes(‘classId’)/Realizes(‘interfaceId’)/Properties(‘idToDelete’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### List

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces(‘interfaceId’)/Properties(‘PropertyId’)/ScopedByList
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### ListItem

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Lists(‘listId’)/Items(‘idToDelete’)

<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces(‘interfaceId’)/Properties(‘PropertyId’)/ScopedByList/Items(‘idToDelete’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^delete-relationships
## Delete relationships

El usuario puede eliminar relaciones entre dos entidades. Se admiten las siguientes combinaciones:

- Class to Interface  
- Interface to Class  
- Property to List  

### Class to interface relationship

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Classes(‘classId’)/Relizes/$ref?$id=<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Interfaces(‘interfaceId’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

### Interface to class relationship

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/ Interfaces(‘interfaceId’)/RelizedByClasses/$ref?$id=<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/Classes(‘classId’)
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>
### Property to list relationship

**Ejemplo**:

```
DELETE
<ServiceRoot>/Sites(‘MySiteAlias’)/Plants(‘PlantA’)/ Interfaces(‘interfaceId’)/Properties(‘propertyId’)/ScopedByList/$ref
```

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^limits
## Limits

La versión de las bases de datos depende de la versión de Smart 3D instalada en la máquina de servicios de Smart 3D.

- Si no se incluye `$top` en la URL, el máximo de entidades que se devuelven es 100.  
- Si se incluye `$top`, el valor máximo por defecto es 10000.  

Cuando se envía una URL al servidor web de la web API para su ejecución, el servidor web envía una solicitud a un proceso de Smart 3D para ejecutar la URL.

Estos límites son configurables. Consulta *Configure Admin API application settings* en la *Intergraph Smart 3D Web API Installation and Configuration Guide*.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^odata-support
## OData Support
^query-options
### Query Options

**Opciones soportadas**  
Las siguientes opciones de consulta del sistema OData son compatibles:

- `$filter`
  - String
    - `contains`
    - `endswith`
    - `startswith`
  - El `$filter` aplicado a las propiedades de tipo string no distingue mayúsculas/minúsculas. "Permission Group 1" se considera igual que "permission grouP 1". Esto también se aplica a todos los operadores de comparación (`eq, ne, gt, ge, lt, le`) y funciones (`contains, endswith, startswith`).
  - Comparación
    - `eq`
    - `ne`
    - `gt`
    - `ge`
    - `lt`
    - `le`
    - `and`
    - `or`
    - `not`
  - Operadores Lambda
    - `any`
    - `all`
  - Ejemplos de filtrado en colecciones de navegación:
    - `/Plants('PlantKey')/Interfaces?$filter=Properties/any(x: x/ScopedbyList/Name ne 'ApprovalReason')`
    - `/Plants('PlantKey')/Interfaces?$filter=Properties/all(x: x/ScopedbyList/Name ne 'ApprovalReason')`

- `$expand`
- `$select`
- `$orderby`
- `$top`
- `$skip`
- `$count`
- `$value`

**Opciones no soportadas**  
Las siguientes opciones de consulta OData no están soportadas:

- `$filter` en propiedades dinámicas
- `$orderby` en propiedades dinámicas

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^handling-special-characters-in-filter-parameter
### Handling special characters in filter parameter

Algunos caracteres especiales no están permitidos directamente en los parámetros de filtro. Deben reemplazarse con su valor hexadecimal correspondiente, con la excepción de `'` (comilla simple). La comilla simple `'` debe reemplazarse por dos comillas simples `''`.

La siguiente tabla enumera algunos caracteres especiales que no están permitidos y su valor hexadecimal correspondiente:

| Special Character | Replace With |
|-------------------|--------------|
| +                 | %2B          |
| &                 | %26          |
| #                 | %23          |
| %                 | %25          |
| ' (apostrophe)    | '' (dos apóstrofes) |

Ejemplos:

- **Incorrecto** (con `#` cerca del final)  
  `/Plants('PlantKey')/PermissionGroups?$filter=Name eq ‘PG#4'`  
- **Correcto** (con `%23` cerca del final)  
  `/Plants('PlantKey')/PermissionGroups?$filter=Name eq ‘PG%234'`

- **Incorrecto** (con `'` simple cerca del final)  
  `/Plants('PlantKey')/PermissionGroups?$filter=Name eq ‘PG'7'`  
- **Correcto** (con dos `''` cerca del final)  
  `/Plants('PlantKey')/PermissionGroups?$filter=Name eq ‘PG''7'`

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^asynchronous-requests
## Asynchronous Requests

El header `Prefer: Respond-Async` debe definirse para solicitar que una función o acción se ejecute de forma asíncrona. Si la ejecución es exitosa, la respuesta devuelve `202 Accepted` e incluye una URI en el header `Location`. Esta URI debe devolver la información de la operación asíncrona completada. Los siguientes casos determinan lo que se devuelve:

1. En el caso normal, devuelve los datos junto con `200 Ok`.  
2. Si la solicitud falla durante el procesamiento, devuelve la excepción.  
3. Si los datos se han eliminado del caché, devuelve `410 Gone`.  
4. Si el procesamiento de la solicitud aún no está completo, devuelve `202 Accepted` y el header nuevamente. La solicitud puede reenviarse.

Existen anotaciones `AsynchronousRequestsSupported` para la función/acción, que pueden determinarse con los metadatos de capacidades. Por ejemplo:

```xml
<Annotations Target="Com.Ingr.S3dAdmin.V1.BackupPlant(Edm.String, Edm.String, Edm.Boolean)">
  <Annotation Term="Com.Ingr.Capabilities.V1.AsynchronousRequestsSupported" Bool="true" />
</Annotations>
```

Esto muestra que la acción `BackupPlant` admite solicitudes asíncronas.

Entonces, para la siguiente solicitud:

```
GET
<ServiceRoot>/Sites('DefWSBindATP_SDB')/Plants('DefWSBindATP')/BackupPlant

Headers
Prefer: Respond-Async
```

El header `Prefer: Respond-Async` se establece, indicando a la API que la solicitud debe procesarse de manera asíncrona. El estado de retorno para esta llamada debe ser `202 Accepted`. Este estado indica que se ha recibido el mensaje y actualmente se está procesando la solicitud. Dentro del header `Location` de la respuesta debería estar la URI necesaria para recuperar los datos de la transacción asíncrona completada. Se verá así:

```
<ServiceRoot>/Sites('DefWSBindATP_SDB')/Plants('DefWSBindATP')/GetBackupPlantResultsAsync(resultId='%7Bf76bce25-d37f-4a04-a9de-7beccb0b7eb5%7D')/
```

Ejecutar esta URI debería devolver los datos completamente procesados, tal como aparecerían si la solicitud original se hubiera enviado de forma síncrona.

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
^unsupported-odata-functionality
## Unsupported OData Functionality

La siguiente funcionalidad de OData no está soportada:

- Batch requests  
- Object deletion  

<span style="display:block;text-align: right;">[[#^indice|Volver al índice]]</span>

---
