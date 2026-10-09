**Práctica 1 de SAD · SecureCorp — Respuestas**  
**Nombre y apellidos:**Claudia Gonzalez de los Reyes  
 **Usuario:** **cgonlos en terminal anywaysmimi en el git**  
Responde con tus palabras, en 1-3 líneas. En la defensa te preguntaré lo mismo en voz alta.  
**Contraseñas que has usado** (solo porque es un laboratorio; en una empresa, jamás en un fichero):  
- Tu usuario:Claudia2026  
- mtorres:Marta2026  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANklEQVR4nO3OMQ2AABAAsSNBCkLfFDZwwIgHRiywEZJWQZeZ2ao9AAD+4lyruzq+ngAA8Nr1AOH0BedHjjlfAAAAAElFTkSuQmCC)  
**1. (A1)** ¿Quién es el issuer de tu ca.crt? ¿Hasta qué fecha es válido? ¿Por qué el subject  
   
 y el issuer de la CA son iguales y los de ldap.crt no?  
El issuer es "CN=SecureCorp Root CA - Claudia Gonzalez, O=SecureCorp, C=ES" y dura hasta 2036 (10 años). En la CA coinciden el subject y el issuer porque se firma a sí misma al ser la raíz (autofirmada), mientras que ldap.crt lo ha firmado la CA y por eso su issuer es la CA y su subject el servidor.  
**2. (A3)** Pega el comando y el resultado de tus dos búsquedas:  
a) miembros de rrhh: Comando: ldapsearch -x -LLL -H ldaps://ldap.securecorp.local -b "ou=groups,dc=securecorp,dc=local" "(cn=rrhh)" member  
Resultado: dn: cn=rrhh,ou=groups,dc=securecorp,dc=local member: uid=lromero,ou=people,dc=securecorp,dc=local member: uid=mtorres,ou=people,dc=securecorp,dc=local  
b) cn y mail de todas las personas:Comando: ldapsearch -x -LLL -H ldaps://ldap.securecorp.local -b "ou=people,dc=securecorp,dc=local" "(objectClass=inetOrgPerson)" cn mail  
Resultado: dn: uid=lromero,ou=people,dc=securecorp,dc=local cn: Lucía Romero mail: lromero@securecorp.local  
dn: uid=cgonlos,ou=people,dc=securecorp,dc=local cn: Claudia Gonzalez mail: cgonlos@securecorp.local  
dn: uid=mtorres,ou=people,dc=securecorp,dc=local cn: Marta Torres mail: mtorres@securecorp.local  
   
**3. (A4)** ¿Por qué la clave ldap.key tiene que ser de openldap y tener permisos 600?  
Porque el servicio de LDAP (slapd) corre con el usuario `openldap` y si no es el dueño no puede abrir la clave para activar TLS. Tiene permisos 600 para que solo ese usuario pueda leerla y ningún otro usuario del sistema pueda robársela.  
**4. (A4)** ¿Qué valor has puesto en SLAPD_SERVICES y por qué?  
Puse `SLAPD_SERVICES="ldaps:/// ldapi:///"`. Usamos `ldaps:///` para obligar a usar el puerto 636 cifrado con TLS, quitamos `ldap:///` para bloquear el puerto 389 en claro por seguridad, y dejamos `ldapi:///` para poder administrar el servidor en local mediante sockets de UNIX.  
**5. (A4)** Antes de añadir TLS_CACERT en el cliente, ldaps:// no funcionaba. ¿Por qué?  
Porque la CA la hemos creado nosotros mismos en la práctica y el cliente no la conoce. Sin la línea `TLS_CACERT` indicando dónde está el `ca.crt`, el cliente no se fía del certificado que le manda el servidor y corta la conexión por seguridad.  
**6. (B3)** Pega la salida de klist con tus dos tickets. ¿Para qué sirve cada uno? ¿Ha viajado tu  
   
 contraseña por la red?  
 Ticket cache: FILE:/tmp/krb5cc_0 Default principal: [cgonlos@SECURECORP.LOCAL](http://cgonlos@SECURECORP.LOCAL "http://cgonlos@SECURECORP.LOCAL")  
El primero (`krbtgt`) es la pulsera principal o TGT que me da el KDC para demostrar quién soy. El segundo (`host/web`) es el ticket específico que le pido al servidor para entrar al servicio web. La contraseña no ha viajado nunca por la red; solo se usa en local para cifrar y descifrar las peticiones.  
**7. (C)** En el docker-compose.yml, ¿qué diferencia hay entre build: e image:? ¿Qué  
   
 significa la línea - "8081:80" del servicio phpldapadmin?  
`build:` le dice a Docker que construya la imagen desde cero leyendo un `Dockerfile`, mientras que `image:` usa una imagen que ya está creada o se descarga de Docker Hub. La línea `- "8081:80"` redirige el puerto 8081 de nuestro ordenador físico al puerto 80 web dentro del contenedor.  
**8. (C)** ¿Por qué en la máquina web no has tenido que escribir a mano TLS_CACERT, y en el  
   
 cliente sí? ¿Qué pasaría con esa línea del cliente si hicieras ./lab.sh reset?  
En la máquina web se configuró sola al construir la imagen porque lo dejamos escrito dentro del `Dockerfile`. En el cliente lo tuvimos que poner a mano sobre la máquina ya encendida. Si hiciera `./lab.sh reset`, la máquina cliente se borraría del todo y perderíamos esa línea que escribimos en su `/etc/ldap/ldap.conf`.  
