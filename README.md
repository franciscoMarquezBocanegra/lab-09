### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

## Francisco Márquez - Carlos sorza

### Dependencias
* Cree una cuenta gratuita dentro de Azure. Para hacerlo puede guiarse de esta [documentación](https://azure.microsoft.com/es-es/free/students/). Al hacerlo usted contará con $100 USD para gastar durante 12 meses.

### Parte 0 - Entendiendo el escenario de calidad

Adjunto a este laboratorio usted podrá encontrar una aplicación totalmente desarrollada que tiene como objetivo calcular el enésimo valor de la secuencia de Fibonnaci.

**Escalabilidad**
Cuando un conjunto de usuarios consulta un enésimo número (superior a 1000000) de la secuencia de Fibonacci de forma concurrente y el sistema se encuentra bajo condiciones normales de operación, todas las peticiones deben ser respondidas y el consumo de CPU del sistema no puede superar el 70%.

### Parte 1 - Escalabilidad vertical

1. Diríjase a el [Portal de Azure](https://portal.azure.com/) y a continuación cree una maquina virtual con las características básicas descritas en la imágen 1 y que corresponden a las siguientes:
    * Resource Group = SCALABILITY_LAB
    * Virtual machine name = VERTICAL-SCALABILITY
    * Image = Ubuntu Server 
    * Size = Standard B1ls
    * Username = scalability_lab
    * SSH publi key = Su llave ssh publica

![Imágen 1](images/part1/part1-vm-basic-config.png)

2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM (Revise la sección "Connect" de la virtual machine creada para tener una guía más detallada).

    `ssh scalability_lab@xxx.xxx.xxx.xxx`

3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).
4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`

5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    ` node FibonacciApp.js`

6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)

7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:
    * 1000000
   
      ![image](https://user-images.githubusercontent.com/108955358/234457078-77b10f99-ec94-439f-b544-6af199ec22f0.png)

    * 1010000
    
      ![image](https://user-images.githubusercontent.com/108955358/234457222-d9264c6a-6cbb-4765-8ef3-804663617ba8.png)

    * 1020000

      ![image](https://user-images.githubusercontent.com/108955358/234726139-56864d0f-e91a-4458-84a4-80bc536898e7.png)

    * 1030000
    
      ![image](https://user-images.githubusercontent.com/108955358/234726868-53684314-ef53-41aa-9a81-c7f2fcb3a6c1.png)
    
    * 1040000
    
      ![image](https://user-images.githubusercontent.com/108955358/234727693-9a411195-b253-435b-ba5b-39020da5d2d3.png)
    
    * 1050000
    
      ![image](https://user-images.githubusercontent.com/108955358/234728016-29544e6d-b1bd-4324-80eb-03fe771a8303.png)
    
    * 1060000
    * 1070000
    * 1080000
    * 1090000    

8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

![Imágen 2](images/part1/part1-vm-cpu.png)

9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).
    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.
    * Ejecute el siguiente comando.

    ```
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
    ```


10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.

12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.

-Se logra observar que al cambiar el tamaño del disco en la VM se evidencia que el escenario de escalabilidad es favorable puesto que el consumo de la cpu se reduce a la mitad y permite que no se presente un fallo como denegación de servicios (ocurrido en el escenario cuando el disco de la maquina era de 0.5GB).
El cambio de tamaño de una máquina virtual en Azure puede ser una medida útil para aumentar la capacidad del sistema, pero es importante considerar que la escalabilidad implica no solo aumentar el tamaño de la máquina, sino también tener en cuenta otros aspectos como la distribución de la carga y la capacidad de recuperación ante fallos.

13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

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

   (Las imagenes estan adjuntas arriba) Bien se nos explica que es una aplicación que no esta optimizada para ser rapida y mientras mayor sea la peticion, el tiempo aumentara, junto a esto el poder de procesamiento de la VM es relativamente pequeño.

5. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.

   ![image](https://user-images.githubusercontent.com/108955358/234457431-417d3b84-3d30-4262-85b5-bd5cafdbee03.png)

    El consumo es grando debido a que es una petición grande y la maquina tiene poca memoria para el procesamiento de la cpu

6. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:
    * Tiempos de ejecución de cada petición.
    * Si hubo fallos documentelos y explique.
    
      B1Ls:

      ![image](https://user-images.githubusercontent.com/108955358/234724711-7ce23c54-a856-41cb-a53f-5079bee2928e.png)


      B2ms:

      ![image](https://user-images.githubusercontent.com/108955358/234724245-011fbe03-0c8c-4b6b-b03c-eb658397664d.png)
    
    
7. ¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?

   La diferencia entre los tamaños B2ms y B1ls en Azure no se encuentra solo en las especificaciones de infraestructura, sino que también se diferencian en la capacidad de procesamiento y rendimiento que ofrecen.

      El tamaño B2ms es una instancia de VM que se encuentra en la serie B de Azure. Esta instancia tiene una capacidad de procesamiento mayor que la instancia B1ls, lo que significa que puede manejar cargas de trabajo más grandes y complejas. Además, ofrece más memoria RAM y núcleos de CPU que la instancia B1ls, lo que se traduce en una mejor capacidad de procesamiento y un mejor rendimiento para las aplicaciones que se ejecutan en ella.
   
8. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?, ¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?

    Aumentar el tamaño de la VM puede ser una buena solución en algunos escenarios, dependiendo de la naturaleza de la carga de trabajo y los recursos requeridos por la aplicación. Si la aplicación requiere más recursos de procesamiento y memoria para funcionar correctamente, aumentar el tamaño de la VM puede ayudar a mejorar el rendimiento y la capacidad de la aplicación para manejar una mayor cantidad de solicitudes.

   Sin embargo, también hay que considerar que aumentar el tamaño de la VM puede aumentar el costo de la solución y que puede haber límites en cuanto a la escalabilidad vertical de una sola VM

   Lo que pasa con FibonacciApp al cambiar el tamaño de la VM es que la maquina virtual no se vera afectada si se realizan varias peteciones ya que tiene los recursos para correr las peticiones.

9. ¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM? ¿Qué efectos negativos implica?

   Reinicio de la VM: En algunos casos, puede ser necesario reiniciar la VM para aplicar los cambios de tamaño. Esto puede provocar una interrupción en el servicio.

   Cambio en el rendimiento de la red: Al aumentar el tamaño de la VM, puede haber un cambio en la capacidad de la red subyacente para soportar el tráfico generado por la aplicación. Es posible que se requiera ajustar los recursos de red para asegurarse de que la VM tenga suficiente ancho de banda para su tráfico de red.

   Cambio en el costo: A medida que se aumenta el tamaño de la VM, también aumenta el costo de la solución. Esto puede ser una consideración importante a la hora de evaluar la escalabilidad de la aplicación.

10. ¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?

   Solo Hubo mejoria en el consumo de la CPU ya que para eso se aumento el tamaño del disco, para aumentar el poder de procesamiento.
      
11. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

   El tiempo de respuesta disminuyo:

   ![image](https://user-images.githubusercontent.com/108955358/234730989-c1f99858-d8f9-4c8c-bfce-5a33a53941bb.png)


### Parte 2 - Escalabilidad horizontal

#### Crear el Balanceador de Carga

Antes de continuar puede eliminar el grupo de recursos anterior para evitar gastos adicionales y realizar la actividad en un grupo de recursos totalmente limpio.

1. El Balanceador de Carga es un recurso fundamental para habilitar la escalabilidad horizontal de nuestro sistema, por eso en este paso cree un balanceador de carga dentro de Azure tal cual como se muestra en la imágen adjunta.

![](images/part2/part2-lb-create.png)

## Balanceador de carga creado

![image](https://user-images.githubusercontent.com/96396177/234675794-de311d34-6256-46d5-944b-4c04dd78f7ef.png)


2. A continuación cree un *Backend Pool*, guiese con la siguiente imágen.

![](images/part2/part2-lb-bp-create.png)

## Backend pool creado

![image](https://user-images.githubusercontent.com/96396177/234676052-a52a18ab-2e23-45c8-a33a-9545caf961b6.png)


3. A continuación cree un *Health Probe*, guiese con la siguiente imágen.

![](images/part2/part2-lb-hp-create.png)

## Healt probe creado

![image](https://user-images.githubusercontent.com/96396177/234676138-e0a7563b-cf64-43a3-9973-a123a74b6ff0.png)

4. A continuación cree un *Load Balancing Rule*, guiese con la siguiente imágen.

![](images/part2/part2-lb-lbr-create.png)

## Load balance rule creado

![image](https://user-images.githubusercontent.com/96396177/234676218-57506c13-c1df-4923-86c3-811e00417500.png)


5. Cree una *Virtual Network* dentro del grupo de recursos, guiese con la siguiente imágen.

![](images/part2/part2-vn-create.png)

## Network creada con subnet

![image](https://user-images.githubusercontent.com/96396177/234676367-27954031-d667-4105-a7cd-ef1658d8a109.png)

![image](https://user-images.githubusercontent.com/96396177/234676440-3fdcfbed-939c-4cb4-abda-2955b154fbc8.png)


#### Crear las maquinas virtuales (Nodos)

Ahora vamos a crear 3 VMs (VM1, VM2 y VM3) con direcciones IP públicas standar en 3 diferentes zonas de disponibilidad. Después las agregaremos al balanceador de carga.

1. En la configuración básica de la VM guíese por la siguiente imágen. Es importante que se fije en la "Avaiability Zone", donde la VM1 será 1, la VM2 será 2 y la VM3 será 3.

![](images/part2/part2-vm-create1.png)

2. En la configuración de networking, verifique que se ha seleccionado la *Virtual Network*  y la *Subnet* creadas anteriormente. Adicionalmente asigne una IP pública y no olvide habilitar la redundancia de zona.

![](images/part2/part2-vm-create2.png)

3. Para el Network Security Group seleccione "avanzado" y realice la siguiente configuración. No olvide crear un *Inbound Rule*, en el cual habilite el tráfico por el puerto 3000. Cuando cree la VM2 y la VM3, no necesita volver a crear el *Network Security Group*, sino que puede seleccionar el anteriormente creado.

![](images/part2/part2-vm-create3.png)

4. Ahora asignaremos esta VM a nuestro balanceador de carga, para ello siga la configuración de la siguiente imágen.

![](images/part2/part2-vm-create4.png)

## VMS creadas, cabe aclarar que solo se pudieron dos maquinas virtuales, por las siguientes razones:

- la licencia de estudiante solo deja que tres recursos tengan una ip publica, por lo tanto solo se pudo crear el load balance, y dos maquinas virtuales con ip publica.

![image](https://user-images.githubusercontent.com/96396177/234721201-d2cce603-a3ae-4b43-b765-5cca41d6ed55.png)



- en la region que dispone la guia solo estan disponibles dos zonas de redundancia


![image](https://user-images.githubusercontent.com/96396177/234719891-b97489da-04e8-4623-baa1-3fe837e2df1a.png)




5. Finalmente debemos instalar la aplicación de Fibonacci en la VM. para ello puede ejecutar el conjunto de los siguientes comandos, cambiando el nombre de la VM por el correcto

```
git clone https://github.com/daprieto1/ARSW_LOAD-BALANCING_AZURE.git

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
source /home/vm1/.bashrc
nvm install node

cd ARSW_LOAD-BALANCING_AZURE/FibonacciApp
npm install

npm install forever -g
forever start FibonacciApp.js
```

Realice este proceso para las 3 VMs, por ahora lo haremos a mano una por una, sin embargo es importante que usted sepa que existen herramientas para aumatizar este proceso, entre ellas encontramos Azure Resource Manager, OsDisk Images, Terraform con Vagrant y Paker, Puppet, Ansible entre otras.

#### Probar el resultado final de nuestra infraestructura

1. Porsupuesto el endpoint de acceso a nuestro sistema será la IP pública del balanceador de carga, primero verifiquemos que los servicios básicos están funcionando, consuma los siguientes recursos:

```
http://52.155.223.248/
http://52.155.223.248/fibonacci/1
```

## Prueba del endpoint del balanceador de carga

![image](https://user-images.githubusercontent.com/96396177/234723112-e23a1fe5-b7a5-4f9f-a976-fd1a1787140d.png)


2. Realice las pruebas de carga con `newman` que se realizaron en la parte 1 y haga un informe comparativo donde contraste: tiempos de respuesta, cantidad de peticiones respondidas con éxito, costos de las 2 infraestrucruras, es decir, la que desarrollamos con balanceo de carga horizontal y la que se hizo con una maquina virtual escalada.

3. Agregue una 4 maquina virtual y realice las pruebas de newman, pero esta vez no lance 2 peticiones en paralelo, sino que incrementelo a 4. Haga un informe donde presente el comportamiento de la CPU de las 4 VM y explique porque la tasa de éxito de las peticiones aumento con este estilo de escalabilidad.

```
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
```
RTA:
La tasa de éxito en la escalabilidad horizontal es mejor que en la escalabilidad vertical porque en la escalabilidad horizontal se agregan más recursos (máquinas virtuales, instancias, etc.) al sistema para manejar el aumento de la carga de trabajo, mientras que en la escalabilidad vertical se aumentan los recursos (CPU, RAM, etc.) en una sola instancia.

En conclusion, en la escalabilidad horizontal, el sistema se distribuye en múltiples instancias, lo que permite que el sistema maneje una mayor cantidad de peticiones y aumente su capacidad de procesamiento. Por lo tanto, se espera que la tasa de éxito sea mayor en la escalabilidad horizontal porque se están utilizando más recursos y se está distribuyendo la carga de trabajo de manera más efectiva.

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


| Condiciones | Escalabilidad Horizontal | Escalabilidad Vertical |
|---|---|---|
| Tiempo de Respuesta Promedio | 18,4 s - 18,7 s | 120,7 s - 120,9 s |
| Cantidad de Peticiones Respondidas con Éxito | 10 | 10 |
| Costo CPU | 37% por VM | 60% |
| Costo Red | 573.76 MB | 736.89 MB |


* Presente el Diagrama de Despliegue de la solución.


![Untitled Diagram drawio (17)](https://user-images.githubusercontent.com/96396177/234740273-277ec5dd-3663-4da4-a80b-7d28c3e98467.png)



