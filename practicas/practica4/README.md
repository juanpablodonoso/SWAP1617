# Práctica 4. Asegurar la granja web 

Objetivos de la praćtica 

- [x] Instalar **certificador SSL** para configurar el acceso HTTPS a los servidores
- [ ] Configurar reglas del cortafuegos con **IPTABLES** para proteger granja web, permitiendo acceso por puertos HTTP y HTTPS en una máquina servidora final, automatizado con un script.
- [ ] Instalación de un certificado del proyecto [Certbot](https://github.com/certbot/certbot) en lugar de un certificado automfirmado
- [ ] Configuración del cortafuegos en una máquina situada por delantes del balanceador en la organización de la web para hacer el filtrado y posterior reencaminamiento del tráfico hacia el balanceador de carga. 

La configuración del balanceo de carga actual puede consultarse [aquí](https://github.com/juanpablodonoso/SWAP1617/tree/master/practicas/Practica3). 

La granja web actual y que pretendemos asegurar es la siguiente

![](../Practica3/img/red_ponderado.jpg)

## Instalación de un certificado autofirmado 

Para generar un certificado autofirmado SSL en Ubuntu server debemos activar el [módulo SSL de Apache](https://httpd.apache.org/docs/2.4/mod/mod_ssl.html) mediante la orden `a2enmod ssl && service apache2 restart`. 

Tras esto será necesario crear la carperta`ssl` donde almacenaremos los certificados de `apache2` en el directorio `/etc/apache2/` 

![](img/ssl_carpeta_certificados.png)

Una vez creada esta carpeta especificaremos la ruta a los certificados de configuración

```openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout 
	/etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt
```

 Se nos pedirá una información que formará parte del certificado que ha sido completada de la siguiente manera: 

![](img/conf_ssl.png)

Una vez generados los certificados debemos especificar dónde se encuentra el archivo del certificado y el archivo con la clave `ssl` , en el archivo de configuración `/etc/apache2/sites-avilable/default-ssl.conf`  mediante las siguientes líneas: 

```
SSLCertificateFile /etc/apache2/ssl/apache.crt
SSLCertificateKeyFile /etc/apache2/ssl/apache.key
```

![](img/default_ssl_conf.png)

Mediante `a2ensite default-ssl` activamos el default que hemos configurado con la ubicación de los certificados. 

![](img/ssl_activar_default.png)

Accediendo al sitio por defecto de nuestro servidor `apache2` veremos que le certificado se encuentra activo pero no es de confianza, ya que es autofirmado

![](img/ssl_selfsigned.png)