# HAPI-FHIR-Mirth-Connect-Postman
Vamos a implementar una arquitectura de interoperabilidad clínica que conecte HAPI FHIR, Mirth Connect y Postman sobre Linux. Esta integración permitirá recibir, transformar y almacenar datos clínicos —especialmente información de pacientes— de forma estandarizada y segura.
# requisitos princripal para conectarlo
1. Tener el previamente Docker o docker.desktop
2. Tener el Minikube
3. Tener el hapi fhir instalado y previamente configurado con el puerto que se le asigna
# Proceso de instalacion
1. Primero nos aseguramos que el docker en linea con el comando
   ```bash
   docker run -p  8080:8080 hapiproject/hapi:latest p
   ```
   y el minikube
   con el comando
   ```bash
   minikube start
   ```
   ponemos en linea el minikube
2. Instalamos el mirthconnect yendo a la ruta donde tengamos el archivo cuando entremos en la carpeta con el comando cd procedemos a la instalacion con el comando
   ```bash
   sudo ./"nombre del archivo".sh 
3. a medida de la instalacion elegiremos el tipo de puerto web de preferencia y el tipo de puerto adminstrador en mi caso usaremos el siguiente
   
<img width="912" height="513" alt="image" src="https://github.com/user-attachments/assets/842a8637-6088-48ce-918a-e1302eae93db" />


   luego de instalacion procedemos a configurar el address con "locashost" con el puerto correspondiente usado en mi caso es

```bash
https://localhost:8443/
```
dejando el resto de manera predeterminada.
4. luego nos pide usuario y contraseña, predeterminadamente es admin en ambos campos, en mi caso ya yo la habia cambiado
# Proceso de conexión
5. les saldra esto, lo que haran sera llenar la informacion para la creacion del usuario administrador, luego ya tendremos el acceso al mirth Connect
   
   <img width="921" height="518" alt="image" src="https://github.com/user-attachments/assets/66865f2f-aad7-4688-a826-5c642db51787" />

6. nos vamos al apartado de Channels para crear un canal de comunicacion y click en el New channels que aparece en la parte inferior izquierda
7. Agregamos el nombre que querramos para llamar a nuestro channels y nos pasamos pasamos al apartado de "Source", en la opcion Connector Typer elegimos la opcion HTTP Listener (elegimos esta opcion ya que la conmunicacion se hara atraves del puerto local que decimos en nuestro caso elegimos 9091)
8. Procedemos a irnos a Destinations el menu al lado del Source, ahi mismo repetiremos el proceso de "Connector Typer elegimos la opcion HTTP sender", cambimos la opcion Queue Messages por on failure (Mirth intentará reenviarlo más tarde, según la configuración de reintentos del destino)
9. luego entramos en el menu Advanced Queue Settings decidiendo el tiempo que demora en repetir el proceso de reenviar el mensaje "retry interval (ms)" le damos en ok, usaremos en este caso usamos 60000 ms que equivalen a 60 segundos
# conexion de mirth Connect con Hapi Fhir
11. ahora en En la configuración del HTTP Sender colocamos la URL http://localhost:8080/fhir/Patient.
De esta manera, Mirth Connect enviará las solicitudes HTTP al servidor HAPI FHIR que está escuchando en el puerto 8080, permitiendo el intercambio de datos clínicos mediante el recurso Patient.
12. Nos desplazamos hasta abajo en la opcion Headers y agregamos un header con el encabezado Content-Type y el valor application/fhir+json y luego otro con el encabezado Accept y el valor siendo el mismo que el anterior quedando de la siguiente manera
   
<img width="537" height="69" alt="image" src="https://github.com/user-attachments/assets/77f7dfda-9488-4061-8cac-d7f595e9c75e" />

esto le indica al servidor en qué formato está el cuerpo del mensaje que le estás enviando, es obligatorio en la mayoría de los casos para que el servidor (ej. HAPI FHIR) pueda interpretar correctamente el mensaje.
13. en el apartado de content agregaremos el escrito
```bash
${message.rawData}
```
lo que hara esto sera mandar el mensaje original sin transformarlo , siendo este el ultimo paso de la creacion de channels le damos en save changes que esta en la parte lateral donde habiamos visto la opcion create channels, luego se cambiara la opcion por calide connector, dando click ahi y nos debe arrojar el siguiente mensaje.

<img width="948" height="535" alt="image" src="https://github.com/user-attachments/assets/014849fd-3c6e-414c-b38e-04b3e81d997a" />

Regresamos a la URL y testeamos la conexion con el hapi fhir regresandonos el mensaje 

<img width="613" height="198" alt="image" src="https://github.com/user-attachments/assets/e7b3acd3-cd06-4a67-8d3a-14d41f97cf39" />

Después de configurar el canal utilizamos la opción Deploy Channels para ponerlo en ejecución. Esto permite que el canal ya esté disponible para recibir mensajes, transformarlos y enviarlos al destino configurado (en este caso HAPI FHIR)
mostrandose de la siguiente manera

<img width="921" height="518" alt="image" src="https://github.com/user-attachments/assets/708d1190-30b4-45c3-84f5-3046f03103ab" />

#Instalamos el Postman
14. usaremos el comando para la instalacion del postman 
```bash
sudo snap install postman
```
luego de instalar abrimos una nueva ventana de http y en la opcion donde esta el GET(consulta de la informacion) cambimos a POST (para creacion de pacientes) e ingresamos la URL de conexion del hapi fhir que en mi caso es http://localhost:9091/fhir/Patient, de la misma manera dejare el archivo para que puedan importar directamente el [json](HAPI-FHIR-Mirth-Connect-Postman/Parcial.postman_collection.json) con el POST y el GET

 


