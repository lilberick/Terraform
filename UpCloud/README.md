# [UpCloud](https://upcloud.com/)

![](.img/upcloudlogo.png)

## Levantar servidor ubuntu
1. definimos credenciales como variables de entorno agregando al final. Usuario y password de cuenta de UpCloud
	```
	$ vim .zshrc
	```
	```bash
	export UPCLOUD_USERNAME="Username for Upcloud API user"
	export UPCLOUD_PASSWORD="Password for Upcloud API user"
	```
2. Inicio
	```
	$ vim version.tf
	```

	```bash
	terraform {
	  required_providers {
	    upcloud = {
	      source = "UpCloudLtd/upcloud"
	      version = "~>2.0.0"
	    }
	  }
	}

	provider "upcloud" {
	  # Your UpCloud credentials are read from the environment variables
	  # export UPCLOUD_USERNAME="Username for Upcloud API user"
	  # export UPCLOUD_PASSWORD="Password for Upcloud API user"
	  # Optional configuration settings can be depclared here
	}
	```
	```
	$ terraform init
	```

	![](.img/init.png)

3. Planificando la infraestructura
	1. Definimos el servidor
		```
		$ vim server1.tf
		```
		```bash
		resource "upcloud_server" "servidor1" {
		  hostname = "ubuntero"
		  zone = "us-nyc1"
		  plan = "1xCPU-1GB"
		  template {
		    # System storage device size
		    size = 25
		    # Template UUID for Ubuntu 20.04
		    storage = "01000000-0000-4000-8000-000030200200"
		  }
		  network_interface {
		    type = "public"
		  }
		network_interface {
		    type = "utility"
		  }
		  # Include at least one public SSH key
		  login {
		    user = "lilberick"
		    # $ cat ~/.ssh/id_rsa.pub
		    keys = [
			"ssh-rsa AAAABBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB lilberick@debian"
		    ]
		    create_password = true
		    password_delivery = "email"
		  }
		  connection {
		    # The server public IP address
		    host        = self.network_interface[0].ip_address
		    type        = "ssh"
		    user        = "lilberick"
		    private_key = file("~/.ssh/id_rsa")
		  }
		 # Remotely executing a command on the server
		  provisioner "remote-exec" {
		    inline = [
		      "echo 'Hello world!'"
		    ]
		  }
		}
		```
	2. Verificamos que todo este bien

		```
		$ terraform plan
		```

		![](.img/plan1.png)
		![](.img/plan2.png)

	3. Aplicamos y creamos el servidor
		```
		$ terraform apply
		```

		![](.img/apply.png)

	4. En nuestro correo gmail recibiremos la password registrado en la cuenta de UpCloud 

		![](.img/Correo.png)

4. Verificamos que hemos creado el servidor en nuestra cuenta de UpCloud
	1. Ir a	: [UpCloud](https://hub.upcloud.com/)

		![](.img/servidorCreado.png)

5. Conexion por ssh

	```
	$ ssh lilberick@209.151.154.139
	```
	1. Vemos el espacio usado

		![](.img/df.png)

	2. si queremos entrar como super usuario

		![](.img/dfsu.png)

6. Conexion por VNC
	1. Ir a	: [UpCloud](https://hub.upcloud.com/)
	2. Clic en nuestro servidor

		![](.img/vnc1.png)

	3. Clic en: **Console**

		![](.img/vnc2.png)

	4. Habilitamos

		![](.img/vnc3.png)

	5. Clic en: Save changes

		![](.img/vnc4.png)

	6. Abrimos VNC
		1. Ejecutamos el programa
		
			```
			$ ./VNC-Viewer-6.20.529-Linux-x64
			```

		2. Clic en: File/New connection
		3. Colocamos el VNC address y el VNC port: **`us-nyc1.vnc.upcloud.com:65414`**

			![](.img/vnc5.png)

		4. clic para iniciar la conexion

			![](.img/vnc6.png)

			![](.img/vnc7.png)

		5. Colocamos la password

			![](.img/vnc8.png)

		6. Colocamos usuario y password de usuario que creamos (password que llego a nuestro correo gmail)

			![](.img/vnc10.png)

			![](.img/vnc11.png)

			![](.img/vnc12.png)

		7. Para darle interfaz grafica

			```
			$ sudo apt update
			$ sudo apt upgrade
			$ sudo apt install lightdm
			```

			![](.img/vnc13.png)

			![](.img/vnc14.png)

			![](.img/vnc15.png)


