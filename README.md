### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

## Francisco Márquez - Carlos sorza



**Preguntas**

1. ¿Cuántos y cuáles recursos crea Azure junto con la VM? 

   Son muchos los recursos que se crean junto a la VM, pero a contuniación se presetan los 10 mas importantes:
      - Redes virtuales (Virtual Network)
      - Discos (Disks)
      - Direcciones IP (IP Addresses)
      - Grupos de seguridad de red (Network Security Groups)
      - Configuraciones de disponibilidad (Availability Sets)
      - Registros de diagnóstico (Diagnostic Logs)
      - Almacenamiento de blobs (Blob Storage)
      - Grupos de recursos (Resource Groups)
      - Configuraciones de equilibrio de carga (Load Balancer Configurations)
      - Registros de actividad (Activity Logs)

2. ¿Brevemente describa para qué sirve cada recurso?

      Redes virtuales (Virtual Network): Es un recurso que permite a las máquinas virtuales comunicarse entre sí y con otros recursos de Azure. También se utiliza para definir la conectividad entre redes locales y las redes virtuales de Azure.

      Discos (Disks): Son recursos de almacenamiento que se utilizan para almacenar el sistema operativo y los datos de la máquina virtual. Los discos pueden ser discos duros virtuales (VHD) o discos de estado sólido (SSD).

      Direcciones IP (IP Addresses): Se pueden asignar direcciones IP públicas y privadas a la máquina virtual. Las direcciones IP públicas permiten que la máquina virtual sea accesible desde Internet.

      Grupos de seguridad de red (Network Security Groups): Son recursos que permiten configurar reglas de seguridad para controlar el tráfico de red hacia y desde la máquina virtual. Los grupos de seguridad de red se utilizan para proteger la máquina virtual de posibles amenazas de seguridad.

      Configuraciones de disponibilidad (Availability Sets): Son grupos de máquinas virtuales que se configuran para ofrecer alta disponibilidad y tolerancia a fallos. Las configuraciones de disponibilidad se utilizan para garantizar que las aplicaciones y los servicios sigan funcionando incluso si una o varias máquinas virtuales fallan.

      Registros de diagnóstico (Diagnostic Logs): Son recursos que permiten realizar el seguimiento y la supervisión de las máquinas virtuales y otros recursos de Azure. Los registros de diagnóstico se utilizan para analizar y solucionar problemas de rendimiento y disponibilidad.

      Almacenamiento de blobs (Blob Storage): Es un servicio de almacenamiento de objetos que se utiliza para almacenar y administrar grandes cantidades de datos no estructurados, como imágenes, videos y archivos de registro.

      Grupos de recursos (Resource Groups): Son contenedores lógicos que se utilizan para agrupar los recursos relacionados de Azure en un solo lugar. Los grupos de recursos se utilizan para organizar, administrar y supervisar los recursos de Azure.

      Configuraciones de equilibrio de carga (Load Balancer Configurations): Son recursos que permiten distribuir el tráfico de red entre varias máquinas virtuales. Las configuraciones de equilibrio de carga se utilizan para mejorar el rendimiento y la disponibilidad de las aplicaciones y los servicios.

      Registros de actividad (Activity Logs): Son registros que contienen información detallada sobre las operaciones que se realizan en Azure. Los registros de actividad se utilizan para supervisar y auditar las actividades de los usuarios y los recursos de Azure.

3. ¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el comando `npm FibonacciApp.js`? ¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?

   Al cerrar la conexión ssh con la VM, la aplicación que se ejecuta con el comando npm FibonacciApp.js se cae porque el proceso que lo ejecuta se asocia con la sesión de ssh en la que se inició. Cuando se cierra la sesión ssh, se termina el proceso y la aplicación ya no se está ejecutando.

   Es necesario crear una regla de puerto de entrada (Inbound port rule) para acceder al servicio porque, por defecto, Azure bloquea todos los puertos de entrada a una VM por razones de seguridad. Al crear una regla de puerto de entrada, se especifica qué puertos se deben abrir para permitir el tráfico entrante. En este caso, la regla de puerto de entrada permite que el tráfico entrante en el puerto 3000 pueda acceder a la aplicación FibonacciApp.js.

4. Adjunte tabla de tiempos e interprete por qué la función tarda tando tiempo.

   ![tiempo  - fibonacci](https://github.com/franciscoMarquezBocanegra/lab-09/assets/98216991/dbe7584f-3aac-4263-a09d-3fd3b3b7fbaf)

   

   (Las imagenes estan adjuntas arriba) Bien se nos explica que es una aplicación que no esta optimizada para ser rapida y mientras mayor sea la peticion, el tiempo aumentara, junto a esto el poder de procesamiento de la VM es relativamente pequeño.

6. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.

   ![arsw](https://github.com/franciscoMarquezBocanegra/lab-09/assets/98216991/097c157c-5470-4927-9d92-9ce99dc88c7e)


    El consumo es grando debido a que es una petición grande y la maquina tiene poca memoria para el procesamiento de la cpu

7. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:
    * Tiempos de ejecución de cada petición.
    * Si hubo fallos documentelos y explique.
    
      B1Ls:

      


      B2ms:

      
    
    
8. ¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?

   La diferencia entre los tamaños B2ms y B1ls en Azure no se encuentra solo en las especificaciones de infraestructura, sino que también se diferencian en la capacidad de procesamiento y rendimiento que ofrecen.

      El tamaño B2ms es una instancia de VM que se encuentra en la serie B de Azure. Esta instancia tiene una capacidad de procesamiento mayor que la instancia B1ls, lo que significa que puede manejar cargas de trabajo más grandes y complejas. Además, ofrece más memoria RAM y núcleos de CPU que la instancia B1ls, lo que se traduce en una mejor capacidad de procesamiento y un mejor rendimiento para las aplicaciones que se ejecutan en ella.
   
9. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?

    Aumentar el tamaño de la VM puede ser una buena solución en algunos escenarios, dependiendo de la naturaleza de la carga de trabajo y los recursos requeridos por la aplicación. Si la aplicación requiere más recursos de procesamiento y memoria para funcionar correctamente, aumentar el tamaño de la VM puede ayudar a mejorar el rendimiento y la capacidad de la aplicación para manejar una mayor cantidad de solicitudes.

   Sin embargo, también hay que considerar que aumentar el tamaño de la VM puede aumentar el costo de la solución y que puede haber límites en cuanto a la escalabilidad vertical de una sola VM

   Lo que pasa con FibonacciApp al cambiar el tamaño de la VM es que la maquina virtual no se vera afectada si se realizan varias peteciones ya que tiene los recursos para correr las peticiones.

10. ¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?

   Reinicio de la VM: En algunos casos, puede ser necesario reiniciar la VM para aplicar los cambios de tamaño. Esto puede provocar una interrupción en el servicio.

   Cambio en el rendimiento de la red: Al aumentar el tamaño de la VM, puede haber un cambio en la capacidad de la red subyacente para soportar el tráfico generado por la aplicación. Es posible que se requiera ajustar los recursos de red para asegurarse de que la VM tenga suficiente ancho de banda para su tráfico de red.

   Cambio en el costo: A medida que se aumenta el tamaño de la VM, también aumenta el costo de la solución. Esto puede ser una consideración importante a la hora de evaluar la escalabilidad de la aplicación.

11. ¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?

   Solo Hubo mejoria en el consumo de la CPU ya que para eso se aumento el tamaño del disco, para aumentar el poder de procesamiento.
      
11. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

   El tiempo de respuesta disminuyo:

   


### Parte 2 - Escalabilidad horizontal

**Preguntas**

* ¿Cuáles son los tipos de balanceadores de carga en Azure y en qué se diferencian?, ¿Qué es SKU, qué tipos hay y en qué se diferencian?, ¿Por qué el balanceador de carga necesita una IP pública?

RTA: Existen dos tipos principales de balanceadores de carga en Azure: el Balanceador de Carga de Aplicaciones (Application Load Balancer) y el Balanceador de Carga de Red (Network Load Balancer).

El primero se encarga de distribuir el trafico de las aplicaciones web, mientras que el otro es para protocolos no http, tambien se diferencia en la cantidad de datos que trasmiten, el balanceador de carga de red transmite mayor volumen de datos

* ¿Cuál es el propósito del *Backend Pool*?

RTA: Backend Pool en un balanceador de carga  especifica que recursos de backend que deben recibir el tráfico entrante y administrar el escalado de los recursos según sea necesario

* ¿Cuál es el propósito del *Health Probe*?

RTA :  Health Probe  monitorea el estado de los recursos de backend en el Backend Pool para garantizar que estén disponibles y funcionando correctamente.


* ¿Cuál es el propósito de la *Load Balancing Rule*? ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.

RTA: Define cómo se debe distribuir el tráfico de red entrante entre los recursos de back-end en el grupo de back-end. Las reglas de equilibrio de carga se pueden basar en varios criterios, como el puerto de destino, el protocolo de transporte, el método de equilibrio de carga, etc.

Hay dos tipos principales de sesiones persistentes: basadas en IP y basadas en cookies. Las sesiones persistentes son importantes porque garantizan que las solicitudes de los clientes se enruten al mismo recurso de back-end en el grupo de back-end durante la sesión. Esto es especialmente importante para las aplicaciones que mantienen el estado de la sesión del usuario, como las aplicaciones web, y puede afectar la escalabilidad del sistema al aumentar la carga en ciertos recursos de back-end.

* ¿Qué es una *Virtual Network*? ¿Qué es una *Subnet*? ¿Para qué sirven los *address space* y *address range*?

RTA: sirve para conectar diferentes recursos de Azure. Las subredes son subconjuntos de direcciones IP en una red virtual que organizan y separan los recursos de Azure. El rango de direcciones define el rango de direcciones IP disponibles para una red virtual, mientras que el rango de direcciones define el rango de direcciones IP disponibles para una subred en particular.

* ¿Qué son las *Availability Zone* y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea *zone-redundant*?

RTA : Una zona de disponibilidad es una ubicación física aislada dentro de una región de Azure que proporciona redundancia adicional y disponibilidad de recursos. Al elegir tres regiones diferentes, los recursos se distribuyen en diferentes ubicaciones físicas para una alta disponibilidad y redundancia. IP con redundancia de zona significa que una dirección IP está disponible en múltiples zonas de disponibilidad para alta disponibilidad y tolerancia a fallas.

* ¿Cuál es el propósito del *Network Security Group*?

RTA: El propósito del grupo de seguridad de red es controlar el acceso de red a los recursos en la red virtual de Azure. Se pueden crear reglas para permitir o denegar el tráfico entrante o saliente según la dirección IP, el puerto, el protocolo y más. Ayuda a proteger los recursos de Azure de posibles amenazas externas y garantiza la seguridad de la red.
* Informe de newman 1 (Punto 2)



* Presente el Diagrama de Despliegue de la solución.






