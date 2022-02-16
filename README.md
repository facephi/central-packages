GitHub de manera nativa no nos permite subir un paquete de forma manual a diferencia de MavenCentral. No obstante mediante Github Actions y Gradle parte de este proceso se puede automatizar. 

Cuando tengamos un pom y un aar que queramos subir a GitHub lo haremos de la siguiente manera.

Desde el repo de central-packages, crearemos una nueva rama a partir de upload-multiple-artifacts, el nombre no influye para la subida, ya que esta rama se borrará después.

  Repositorio central-packages

 

Una vez creada, clonamos el repositorio y cambiamos a la rama que hayamos creado. Nos centraremos en 3 detalles, el primero es poner el fichero aar en cada carpeta que hayamos creado, si queremos subir 3 paquetes, tendremos que tener 3 carpetas, cada una con un build.gradle y su fichero aar. La idea es copiar una carpeta hasta el número de paquetes que queramos subir y añadir el aar. El segundo es indicar en el fichero settings.gradle las carpetas que se van a subir. El tercero es rellenar todos los datos necesarios desde donde pone INICIO MODIFICAR hasta FINAL MODIFICAR de todos los build gradle que hay en las carpetas, el build.gradle externo(directorio root) no hace falta modificarlo para nada ya que este se ocupa de llamar de manera recursiva a los demás.

Una vez rellenado los datos, se hace git add y su respectivo push. GitHub Actions ejecutará el publish automáticamente  a cada push que se haga. El paquete creado se puede encontrar a los 2 minutos en el apartado de paquetes.

Ejemplo práctico: 

Tenemos 2 aar y 2 pom.

El primer pom

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.facephi.phingers.androids</groupId>
  <artifactId>phingers-widget</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <packaging>aar</packaging>
  <dependencies>
    <dependency>
      <groupId>androidx.multidex</groupId>
      <artifactId>multidex</artifactId>
      <version>2.0.1</version>
    </dependency>
    <dependency>
      <groupId>com.facephi.phingers.android</groupId>
      <artifactId>phingers-camera</artifactId>
      <version>7.1.1</version>
    </dependency>
    <dependency>
      <groupId>com.facephi.phingers.android</groupId>
      <artifactId>phingers-core</artifactId>
      <version>7.1.2</version>
    </dependency>
    <dependency>
      <groupId>androidx.core</groupId>
      <artifactId>core-ktx</artifactId>
      <version>1.6.0</version>
    </dependency>
    <dependency>
      <groupId>org.jetbrains.kotlin</groupId>
      <artifactId>kotlin-stdlib-jdk7</artifactId>
      <version>1.6.0</version>
    </dependency>
    <dependency>
      <groupId>androidx.constraintlayout</groupId>
      <artifactId>constraintlayout</artifactId>
      <version>2.0.4</version>
    </dependency>
    <dependency>
      <groupId>androidx.constraintlayout</groupId>
      <artifactId>constraintlayout</artifactId>
      <version>2.0.4</version>
    </dependency>
  </dependencies>
</project>

Una vez creada y clonada la rama, añadimos el fichero aar a una de las carpetas, por ejemplo paquete1 (el nombre de la carpeta no influye, si se cambia hay que indicarlo en settings.gradle). En caso de que el fichero no se llame file.aar , tendremos que indicar en el nombre en el build.gradle de paquete1. Teniendo ya el fichero, solo nos queda rellenar el build gradle de paquete1.

La primera parte, necesitamos indicar el groupid, artifact_id y version, además del nombre del fichero.

Para saber que poner en esos campos, tenemos que ver el pom que nos han pasado y fijarnos en el apartado antes de las dependencias. En este caso, sería el siguiente

<groupId>com.facephi.phingers.androids</groupId>
  <artifactId>phingers-widget</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <packaging>aar</packaging>

Estos datos, los pondremos en el build.gradle, quedando tal que así:

Ahora falta añadir las dependencias del POM, para esto usaremos la siguiente herramienta. Desde esta página web podemos copiar nuestro POM entero y este nos dará lo que hay que poner en el apartado dependencies. 

 

Copiaremos las dependencias de la derecha con el pequeño detalle de que compile pasa a ser implentation debido a que está deprecado. Quedando tal que así:

Segundo POM

<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.facephi</groupId>
  <artifactId>fphi-selphid-core</artifactId>
  <version>1.11.7</version>
  <packaging>aar</packaging>
</project>


Añadimos el aar respectivo a paquete2 y rellenamos el build gradle como en el anterior. Este caso es especial porque no tiene dependencias, al ponerlo en la página sale vacío. Esto significa que no hace falta rellenar el campo dependencias 

Quedando el segundo build gradle tal que así 

Una vez de completados todos los build.gradle internos, nos aseguramos que el nombre de la carpeta está en settings.gradle.

La estructura de nuestro proyecto debería ser parecida a la siguiente, lo marcado en rojo son los apartados que han sido modificados/deberian ser modificados.

Finalmente lo subimos a GitHub para que GitHub Actions lo realice 

git add .
git commit -m "[CD-000] Package phinger added"
git push

Si se ha ejecutado correctamente, el apartado de actions saldrá en verde

Y el paquete estará en packages (a veces tarda un poco)
