# Capacitación para desarrolladores de StackHawk: introducción a HawkScan (guía de capacitación para Windows)

[![en](https://img.shields.io/badge/lang-en-red.svg)](WindowsOSTrainingGuide.md)[![es](https://img.shields.io/badge/lang-es-yellow.svg)](WindowsOSTrainingGuide.es.md)

Esta guía se utilizará junto con la capacitación en vivo impartida por el equipo de StackHawk. También se puede utilizar de forma autodidacta para recorrer los pasos de introducción a HawkScan. A lo largo de la capacitación, haremos referencia a comandos específicos que se deben ingresar en su terminal. Esos comandos se describen a continuación con descripciones detalladas.

## Paso 1: _Instalación de Hawkscan_
Existen múltiples métodos de instalación y [descarga](https://docs.stackhawk.com/download.html) de la herramienta HawkScan CLI. A los fines de esta capacitación, instalaremos HawkScan a través de nuestro Instalador MSI. Ejecute el siguiente comando en su terminal para descargar e instalar la última versión de HawkScan.


```
msiexec.exe /i https://download.stackhawk.com/hawk/msi/hawk-4.3.0.msi /passive
```

Puede comprobar qué versión de HawkScan tiene instalada actualmente ejecutando,

```
hawk version
```
>La última versión es v4.3.0.


## Paso 2: _Autenticación de StackHawk_
Ahora que HawkScan está instalado localmente, necesitamos realizar la autenticación con la plataforma StackHawk para vincular los análisis a su cuenta.

Primero, necesitamos crear una clave de API. Inicie sesión en StackHawk a través de un navegador web y vaya a **Settings (Configuración) > API Keys (Claves de API) > Create New API Key** (Crear nueva clave de API). También puede hacer clic [aquí](https://app.stackhawk.com/settings/apikeys) para acceder a la página de creación de claves de API.

> [!WARNING]
> Solo se le mostrará su clave de API completa una vez. Asegúrese de copiarla y guardarla para usarla más tarde.

Ahora debemos usar esa clave de API para la autenticación en StackHawk. Ejecute el siguiente comando en su terminal.

```
hawk init
```

Después de introducir este comando, HawkScan le pedirá que introduzca su clave de API. Pegue la nueva clave de API que guardó en el paso anterior en el terminal y presione Intro. Si el proceso se realiza correctamente, recibirá la respuesta "**Authenticated!**".

## Paso 3: _Instalación y ejecución de Java Spring Vulny_
[Java Spring Vulny](https://github.com/kaakaww/javaspringvulny) es nuestra aplicación de pruebas intencionalmente creada con vulnerabilidades y es ideal para probar HawkScan.

Para empezar, debemos clonar el repositorio Java Spring Vulny en nuestra computadora local. Para obtener el repositorio, ejecute el siguiente comando Git en su terminal.

```
git clone git@github.com:kaakaww/javaspringvulny.git
```

Como alternativa, si no puede clonar repositorios con Git, puede descargar Java Spring Vulny como archivo Zip [aquí](https://github.com/kaakaww/javaspringvulny). El archivo estará en su carpeta de descargas y deberá moverlo al directorio de inicio.


A continuación, tenemos que ejecutar Java Spring Vulny para que funcione. Para facilitar las cosas, descargaremos y ejecutaremos una versión JAR de la aplicación. Ejecute el siguiente comando para descargar la aplicación.

```
Invoke-WebRequest -Uri "https://github.com/kaakaww/javaspringvulny/releases/download/0.2.0/java-spring-vuly-0.2.0.jar" -OutFile "java-spring-vuly-0.2.0.jar"
```
Después, utilice este comando para que la aplicación se ejecute localmente en su computadora.

```
Start-Process java -ArgumentList "-jar","java-spring-vuly-0.2.0.jar","--spring.profiles.active=windows"
```

Si el comando se ejecuta correctamente, ahora deberíamos poder acceder a la aplicación Java Spring Vulny en ejecución en https://localhost:9000/.
> Según el navegador, es posible que reciba una advertencia de sitio seguro y tenga que hacer clic para acceder a la aplicación.

Haga clic y observe las diferentes páginas.

## Paso 4: _Creación de una aplicación de StackHawk_

Deberá identificar cada aplicación que analice con StackHawk en la plataforma. Cada aplicación tendrá un ID asociado, y los análisis correspondientes se pueden dividir por entorno.
Podemos crear estas aplicaciones en StackHawk a través de la CLI. Para crear su aplicación, ejecute el siguiente comando en un terminal.

```
hawk create application --name 'jsv-[USERNAME]-test' -e dev
```

> Cuando ejecute este comando, sustituya [USERNAME] por su nombre. La plataforma StackHawk utilizará este nombre de aplicación para separar sus análisis de otras aplicaciones.
> 
> Si tiene varias organizaciones asociadas a su cuenta, agregue -o [ID de la org.] al final de este comando.

HawkScan responderá con un _**Kaakaww!!**_ para informarle que la creación fue exitosa. Luego, se le proporcionará un ID de aplicación. Guárdelo para referencia futura.

## Paso 5: _Ejecución de HawkScan_
Ha llegado el momento para el que hemos estado trabajando: ejecutar HawkScan contra nuestra aplicación. Ahora debemos ir del directorio de inicio al directorio de JavaSpringVulny dentro de su terminal.
```
cd javaspringvulny
```

Aquí es donde queremos ejecutar HawkScan, ya que es donde se encuentran los archivos de configuración específicos de nuestra aplicación.

> [!NOTE]
> Si descargó este repositorio a través de un archivo Zip, el directorio se llamará **javaspringvulny-main** y deberá ejecutar `cd javaspringvulny-main`.

HawkScan utiliza archivos YAML para indicar al escáner cómo interactuar con su aplicación. Estos archivos pueden incluir cualquier cosa, desde decirle al escáner dónde se encuentra la aplicación en ejecución hasta darle instrucciones para autenticarse con su aplicación.

### Análisis básico

El primer análisis que vamos a ejecutar es el archivo básico `stackhawk.yml` ubicado en el subdirectorio stackhawk.d. Este archivo incluye lo básico para que el escáner se ejecute.

```yaml
app:
  applicationId: 08846d92-5abd-4257-b98e-760ba8c050b9
  env: Development
  host: ${APP_HOST:https://localhost:9000}
```
- **Application ID**: Le indica al escáner qué aplicación está analizando (como se configuró en el último paso).

- **Environment**: StackHawk le permite definir un entorno en cada análisis. Esto le permitirá separar sus hallazgos más tarde en la plataforma por tipo de entorno.

- **Host**: Esto le indica al escáner dónde puede salir y acceder a la aplicación.

Para iniciar el análisis con JavaSpringVulny usando la configuración básica, inserte el ID de aplicación guardado en el siguiente comando y ejecútelo en su terminal.

```
$env:APP_ID = "XXXXX"; hawk scan /stackhawk.d/stackhawk.yml
```

Si se ejecuta correctamente, empezará a ver líneas en su terminal sobre su configuración, el escáner analizando las URL y finalmente las vulnerabilidades que se encontraron en la aplicación.

### Análisis autenticado
Ahora que hemos completado con éxito un análisis básico de Java Spring Vulny, vamos a realizar uno más exhaustivo que utilice autenticación. Si hace clic en la aplicación Java Spring Vulny en https://localhost:9000/, verá que hay diferentes tipos de autenticación que se pueden utilizar. A los fines de esta capacitación, vamos a utilizar la autenticación de formularios para que se inicie sesión en el escáner y luego inyectar una cookie para mantener la sesión iniciada.

Para ello, utilizaremos `stackhawk-auth-form-cookie.yml`, que se encuentra en el directorio stackhawk.d.

```yaml
app:
  env: ${APP_ENV:Form Cookie}
  excludePaths:
    - "/logout"
  antiCsrfParam: "_csrf"
  authentication:
    usernamePassword:
      type: FORM
      loginPath: /login
      loginPagePath: /login
      usernameField: username
      passwordField: password
      scanUsername: "janesmith"
      scanPassword: "password"
    cookieAuthorization:
      cookieNames:
        - "JSESSIONID"
    testPath:
      path: /search
      success: ".*200.*"
    loggedInIndicator: "\\QSign Out\\E"
    loggedOutIndicator: ".*Location:.*/login.*"
```
Esta configuración le indica al escáner dónde y cómo autenticarse con la aplicación. Le indicamos cómo encontrar los formularios de acceso, qué nombre de usuario y contraseña inyectar, e incluso qué cookie utilizar. Puede notar que esta configuración no incluye **ApplicationID**, **Environment** ni **Host**. Con HawkScan, podemos usar múltiples archivos YAML para instruir al escáner.

Para iniciar este análisis autenticado, usaremos el mismo comando que la última vez, pero agregaremos `stackhawk-auth-form-cookie.yml` al final del comando Hawk Scan. El comando que ejecute debe coincidir con el siguiente:

```
$env:APP_ID = "XXXXX"; hawk scan stackhawk.d/stackhawk.yml stackhawk.d/stackhawk-auth-form-cookie.yml
```

Al igual que en el primer análisis, verá cómo el escáner analiza la aplicación y las vulnerabilidades encontradas. Si inicia sesión en el sitio de StackHawk y navega a la sección Scans (Escaneos), puede profundizar en los detalles de cada vulnerabilidad.

Como sucede con el YAML anterior, el repositorio JavaSpringVulny contiene ejemplos de múltiples formas diferentes de autenticación que puede ejecutar y probar con la aplicación.

## Recursos adicionales
A continuación encontrará algunos recursos de nuestra documentación que puede utilizar para complementar la capacitación anterior. Si se encuentra con algún problema o si tiene alguna pregunta para nuestro equipo, no dude en comunicarse con nosotros. Puede contactarnos escribiendo al correo electrónico support@stackhawk.com.


- [Introducción a HawkScan](https://docs.stackhawk.com/getting-started/)

- [Análisis autenticado de HawkScan](https://docs.stackhawk.com/hawkscan/authenticated-scanning/)

- [Mejores prácticas de HawkScan](https://docs.stackhawk.com/support/best-practices.html)

- [Integraciones con StackHawk](https://docs.stackhawk.com/workflow-integrations/)
