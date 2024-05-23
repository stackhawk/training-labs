# Entrenamiento de Desarrolladores de StackHawk: Guía de Entrenamiento de MacOS / Linux para Comenzar con HawkScan

[![en](https://img.shields.io/badge/lang-en-red.svg)](MacOS-LinuxTrainingGuide.md)[![es](https://img.shields.io/badge/lang-es-yellow.svg)](MacOS-LinuxTrainingGuide.es.md)

Esta guía se utilizará junto con el entrenamiento en vivo impartido por el equipo de StackHawk. También se puede usar de forma autodidacta para recorrer los pasos de cómo comenzar con HawkScan. A lo largo del entrenamiento, haremos referencia a comandos específicos que deben ingresarse en su terminal. Esos comandos se detallan a continuación, con descripciones detalladas.

## Paso 1: _Instalación de HawkScan_
Existen múltiples métodos de instalación y [descargas](https://docs.stackhawk.com/download.html) de la herramienta CLI de HawkScan. Pero para el propósito de este entrenamiento, instalaremos HawkScan a través de nuestro instalador PKG. Ejecute el siguiente comando curl en su terminal para descargar e instalar la última versión de HawkScan.


```
curl -v https://download.stackhawk.com/hawk/pkg/hawk-3.9.0.pkg -o hawk-3.9.0.pkg &&\
sudo installer -pkg hawk-3.9.0.pkg -target /Applications &&\
rm hawk-3.9.0.pkg
```

Puede verificar qué versión de HawkScan tiene actualmente instalada ejecutando,

```
hawk version
```
>v3.9.0 es la última versión


## Paso 2: _Autenticación en StackHawk_
Ahora que HawkScan está instalado localmente, necesitamos autenticarnos con la plataforma de StackHawk para vincular sus escaneos a su cuenta.

Primero, necesitamos crear una clave API. Inicie sesión en StackHawk a través de un navegador web y navegue a **Settings> API Keys> Create New API Key**. También puede hacer clic [aquí](https://app.stackhawk.com/settings/apikeys) para acceder a la página de creación de la clave API.
> [!WARNING]
> Solo se le mostrará su **clave API completa** una vez. Asegúrese de copiarla y guardarla en algún lugar para más tarde.

Ahora, necesitamos usar esa clave API para autenticarnos en StackHawk. Ejecute el siguiente comando en su terminal.

```
hawk init
```

Después de ingresar este comando, HawkScan le pedirá que ingrese su clave API. Pegue su nueva clave API, que guardó en el paso anterior, en el terminal y presione enter. Si se hace correctamente, recibirá un mensaje de **Authenticated!** en su terminal.

## Paso 3: _Instalación y Ejecución de Java Spring Vulny_
[Java Spring Vulny](https://github.com/kaakaww/javaspringvulny) es nuestra aplicación de prueba, construida con vulnerabilidades a propósito, y es un objetivo perfecto para probar HawkScan.

Para comenzar, necesitamos clonar el repositorio de Java Spring Vulny a nuestra computadora local. Para descargar el repositorio, ejecute el siguiente comando Git en su terminal.

```
git clone git@github.com:kaakaww/javaspringvulny.git
```

Alternativamente, si no está configurado para clonar repositorios con Git, puede descargar Java Spring Vulny como un archivo Zip [aquí](https://github.com/kaakaww/javaspringvulny). Se ubicará en su carpeta de descargas y necesita ser movido al directorio principal.

A continuación, necesitamos iniciar Java Spring Vulny para que esté activo y funcionando. Para facilitar las cosas, vamos a descargar y ejecutar una versión JAR de la aplicación. Ejecute el siguiente comando para descargar la aplicación.

```
curl -Ls https://github.com/kaakaww/javaspringvulny/releases/download/0.2.0/java-spring-vuly-0.2.0.jar -o ./java-spring-vuly-0.2.0.jar

```
Luego, use este comando para ejecutar la aplicación localmente en su computadora.

```
java -jar ./java-spring-vuly-0.2.0.jar
```

Si se ejecuta correctamente, ahora deberíamos poder acceder a la aplicación Java Spring Vulny en funcionamiento en https://localhost:9000/
> Dependiendo del navegador, puede recibir una advertencia de sitio seguro y necesitará hacer clic para acceder a la aplicación.

Navegue por la aplicación y observe las diferentes páginas.

## Paso 4: _Creación de una Aplicación en StackHawk_

Para cada aplicación que escanee con StackHawk, necesitará identificar esa aplicación en la plataforma. Cada aplicación tendrá un ID asociado, y los escaneos de esa aplicación se pueden dividir por entorno.  
Podemos crear estas aplicaciones en StackHawk a través de la CLI. Ejecute el siguiente comando en un terminal para crear su aplicación.

```
hawk create application --name 'jsv-[USERNAME]-test' -e dev
```

> Al ejecutar este comando, reemplace [USERNAME]  con su nombre. La plataforma StackHawk usará este nombre de aplicación para separar sus escaneos de otras aplicaciones.
> 
> Si tiene múltiples organizaciones asociadas con su cuenta, agregue -o [Org ID] al final de este comando.

HawkScan responderá con un **_Kaakaww!!_** para informarle que la creación fue exitosa. Luego, se le proporcionará un ID de Aplicación. Guarde este ID para referencia futura.

## Paso 5: _Ejecutando HawkScan_
El momento que hemos estado esperando: ¡ejecutar HawkScan contra nuestra aplicación!
Debería seguir estando en el directorio **JavaSpringVulny** dentro de su terminal. Aquí es donde queremos ejecutar HawkScan, ya que es donde se encuentran nuestros archivos de configuración específicos de la aplicación.

>Si está de vuelta en el directorio principal, use `cd javaspringvulny` para moverse al directorio necesario.

HawkScan utiliza archivos YAML para instruir al escáner sobre cómo interactuar con su aplicación. Estos archivos pueden incluir todo, desde decirle al escáner dónde se encuentra la aplicación en funcionamiento, hasta instruirle sobre cómo autenticarse con su aplicación.

### Escaneo Básico

El primer escaneo que vamos a ejecutar es el archivo básico `stackhawk.yml` ubicado en el subdirectorio stackhawk.d. Este archivo incluye lo necesario para que el escáner funcione.

```yaml
app:
  applicationId: 08846d92-5abd-4257-b98e-760ba8c050b9
  env: Development
  host: ${APP_HOST:https://localhost:9000}
```
- **Application ID**: Esto permite que el escáner sepa qué aplicación está escaneando. (Configurado en el paso anterior)

- **Environment**: StackHawk le permite definir un entorno en cada escaneo. Esto le permite separar sus hallazgos más tarde en la plataforma por tipo de entorno.

- **Host**: Esto le dice al escáner dónde puede ir y acceder a la aplicación.

Para iniciar el escaneo contra JavaSpringVulny usando la configuración básica, inserte su ID de Aplicación guardado en el siguiente comando y ejecútelo en su terminal.

```
APP_ID=XXXXX hawk scan /stackhawk.d/stackhawk.yml
```

Si tiene éxito, comenzará a ver líneas en su terminal sobre su configuración, el escáner rastreando URLs y, finalmente, las vulnerabilidades encontradas en la aplicación.

### Escaneo Autenticado
Ahora que hemos completado con éxito un escaneo básico de Java Spring Vulny, hagamos un escaneo más exhaustivo que use autenticación.
Si navega por la aplicación Java Spring Vulny en https://localhost:9000/, puede ver que hay diferentes tipos de autenticación que se pueden usar. Para el propósito de este entrenamiento, vamos a usar la Autenticación de Formularios para que el escáner inicie sesión y luego inyecte una cookie para mantener la sesión.

Para hacer esto, usaremos el `stackhawk-auth-form-cookie.yml`  que se encuentra en el directorio stackhawk.d

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
Esta configuración le dice al escáner dónde y cómo autenticarse con la aplicación. Le decimos cómo encontrar los formularios de inicio de sesión, qué nombre de usuario y contraseña inyectar, e incluso qué cookie usar. Puede notar que esta configuración no incluye el **ApplicationID**, **Environment** o **Host**. Con HawkScan, podemos usar múltiples archivos YAML para instruir al escáner.

Para iniciar este escaneo autenticado, usaremos el mismo comando que la última vez, pero agregaremos el `stackhawk-auth-form-cookie.yml` al final. El comando que ejecute debe coincidir con el siguiente.

```
APP_ID=XXXXX hawk scan stackhawk.d/stackhawk.yml stackhawk.d/stackhawk-auth-form-cookie.yml
```

Al igual que el primer escaneo, verá el escáner rastreando la aplicación y las vulnerabilidades que se encuentran.
Si inicia sesión en el sitio de StackHawk y navega a la sección **Scans**, puede profundizar en los detalles específicos de cada vulnerabilidad.

Similar al YAML anterior, el repositorio de JavaSpringVulny contiene ejemplos de múltiples formas diferentes de autenticación que puede ejecutar y probar contra la aplicación.

## Recursos Adicionales
Los siguientes son algunos excelentes recursos de nuestra documentación, que se pueden utilizar para complementar el entrenamiento anterior. Si encuentra algún problema o tiene alguna pregunta para nuestro equipo, no dude en contactarnos. Puede contactarnos enviando un correo electrónico a support@stackhawk.com

- [Comenzando con HawkScan](https://docs.stackhawk.com/getting-started/)

- [Escaneo Autenticado con HawkScan](https://docs.stackhawk.com/hawkscan/authenticated-scanning/)

- [Mejores Prácticas de HawkScan](https://docs.stackhawk.com/support/best-practices.html)

- [Integraciones de StackHawk](https://docs.stackhawk.com/workflow-integrations/)
