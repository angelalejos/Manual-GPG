#          GNUPG
El cifrado de mensajes es necesario si vas a tratar con información sensible o confidencial. Ya sea en un entorno empresarial o uno personal, hay información que no debería ser vista por nadie más que las partes implicadas. Para eso vamos a aprender a utilizar una herramienta que es indispensable no solo se usa para preservar nuestra privacidad sino como parte integral en la gestión de repositorios de nuestras distribuciones linux, entre otras muchas cosas.

Gpg es la herramienta open source multiplataforma por excelencia para el cifrado de archivos, aunque también permite que firmemos archivos para poder asegurar que su origen es el que es.

# Generar las claves

Para poder cifrar asimétricamente primero tenemos que crear la pareja de claves (pública y privada) con el comando gpg --gen-key.

        ~/gpg$ gpg --gen-key
        gpg (GnuPG) 1.4.11; Copyright (C) 2010 Free Software Foundation, Inc.
        This is free software: you are free to change and redistribute it.
        There is NO WARRANTY, to the extent permitted by law.
        Por favor seleccione tipo de clave deseado:
            (1) RSA y RSA (predeterminado)
            (2) DSA y Elgamal
            (3) DSA (sólo firmar)
            (4) RSA (sólo firmar)
        ¿Su selección?:

GPG nos permite elegir el tipo de clave que queremos usar, hay opciones que solo permiten firmar y otras que permiten firmar y cifrar, en este caso usaremos DSA y Elgamal.

        las claves DSA pueden tener entre 1024 y 3072 bits de longitud.
        ¿De qué tamaño quiere la clave? (2048) 

A partir de aquí todo es más trivial, nos pide la fecha en la que expirará la clave, la información del emisor de la clave (nombre, mail y algunos datos extra que queramos dar) y por último nos pedirá la contraseña que salvaguarda la clave privada.

Tras generar las claves podemos verlas con el comando gpg -k que nos muestra nuestro anillo de claves, lo importante de este paso es que veremos la identificación de cada una, que es necesaria para poderlas exportar y enviar.

        ~/gpg$ gpg -k /home/pedro/.gnupg/pubring.gpg
            ------------------------------
            pub   2048D/18384645 2018-11-10
            uid                  {Name} (Manual GPG - Genbeta Dev) <info@example.es>
            sub   2048g/C4A9EA7A 2018-11-10

# Exportar y enviar la clave privada

El objetivo de esta pareja de claves es que cualquiera nos pueda mandar un archivo cifrado que solo veremos nosotros y esto se hace difundiendo la clave pública que acabamos de crear (la pública, nunca la privada), para exportarla en un archivo usaremos el comando gpg -output [archivo destino] --export [ID de a clave pública] (la clave pública generada antes tiene la ID 18384645).

        ~/gpg$ gpg --export 18384645 | base64 > terraform.pub

# Descifrar un archivo con la clave privada

Y ahora es el momento de descifrar con nuestra clave privada el documento tras recibirlo,  introduciendo la contraseña que creamos para salvaguardar la clave privada.
        
        ~/gpg$ --passphrase $decodeme --batch -q --decrypt password_file