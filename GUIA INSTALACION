-----GUÍA INSTALACIÓN-----

--Se necesita disponer de Docker, helm y kubectl para esta guía

-----Es necesario hacer login en docker antes de nada para poder pushear las imágenes que se construyan
		docker login

-----Con el makefile raíz se van a instalar dependencias y construir y pushear las imágenes Docker

--Instalar dependencias
		make install-dependencies

-----Para realizar tests unitarios del código:

coverage run -m pytest gateway/test/unit
		coverage run -m pytest orders/test/unit
--El microservicio products no dispone de tests unitarios


-----Para visualizar resultados generales de los tests:

coverage report -m


-----Para tests funcionales (sería necesario tener desplegado en la máquina de testing un RabbitMQ, PostgreSQL y Redis accesibles vía localhost) NO RECOMENDADO

		coverage run -m pytest gateway/test
		coverage run --append -m pytest orders/test
		coverage run --append -m pytest products/test


--Construir unas imágenes plantilla
		make build-base

--A partir de esas imágenes, se construyen las de los microservicios. Para ello, hay que personalizar unas líneas en los archivos:
(Esto solo será necesario cada vez que se vaya a utilizar un usuario de Docker distinto)

		./gateway/makefile
		./products/makefile
		./orders/makefile

--Se muestra como ejemplo la edición necesaria para el makefile de gateway. Hay que reemplazar el nombre de usuario "agimenezibanez" en todas las líneas por el usuario de docker con el que se haya hecho login, para subirlo a ese Dockerhub. Al reemplazar el usuario en estos makefile, más adelante se podrán construir y pushear las imágenes con solo dos instrucciones. El makefile a editar contiene lo siguiente:

		build-image:
			docker build -t agimenezibanez/nameko-example-gateway:$(TAG) .;

		docker-tag:
			docker tag agimenezibanez/nameko-example-gateway:$(FROM_TAG) \
			agimenezibanez/nameko-example-gateway:$(TAG)

		push-image:
			docker push agimenezibanez/nameko-example-gateway:dev

--Suponiendo que el usuario que ha hecho login en Docker del cual se va a utilizar el Dockerhub se llamase "citibankmx", el archivo quedaría:

		build-image:
			docker build -t citibankmx/nameko-example-gateway:$(TAG) .;

		docker-tag:
			docker tag citibankmx/nameko-example-gateway:$(FROM_TAG) \
			agimenezibanez/nameko-example-gateway:$(TAG)

		push-image:
			docker push citibankmx/nameko-example-gateway:dev

--Ahora se construyen las imágenes y se suben a Dockerhub

		make build
		make push-images


-----------KUBERNETES-------------

		kubectl create namespace testing

-----Instalaciones helm:

		helm install broker  --namespace testing stable/rabbitmq
		helm install db --namespace testing stable/postgresql --set postgresqlDatabase=orders
		helm install cache  --namespace testing stable/redis

-----Instalaciones kubectl

		kubectl --namespace=testing apply -f ./k8s/charts/gateway/templates/ingress.yaml
		kubectl --namespace=testing apply -f ./k8s/charts/gateway/templates/deployment.yaml
		kubectl --namespace=testing apply -f ./k8s/charts/gateway/templates/service.yaml

		kubectl --namespace=testing apply -f ./k8s/charts/products/templates/deployment.yaml

		kubectl --namespace=testing apply -f ./k8s/charts/orders/templates/deployment.yaml


-----------PRUEBAS FUNCIONALES DE MICROSERVICIOS---------------

--Pod para probar:
		kubectl --namespace=testing run podtest -it --image=centos --restart=Never /bin/bash

--En las uris (http://gateway/products) se utiliza "gateway" porque es el servicio expuesto y se está accediendo desde dentro del cluster. Para acceder desde fuera habría que utilizar la IP externa del servicio.

--Hay que tener en cuenta que las respuestas de la API en consola no incluyen un retorno de carro al final del mensaje, por lo que estarán solapadas con la siguiente línea de ejecución.

--Crear product:
		curl -XPOST -d '{"id": "the_odyssey", "title": "The Odyssey", "passenger_capacity": 101, "maximum_speed": 5, "in_stock": 10}' 'http://gateway/products'

--Obtener product:
		curl 'http://gateway/products/the_odyssey'

--Crear order:
		curl -XPOST -d '{"order_details": [{"product_id": "the_odyssey", "price": "100000.99", "quantity": 1}]}' 'http://gateway/orders'

--Obtener order:
		curl 'http://gateway/orders/1'

--Para salir del pod:
		exit

---------DESINSTALAR TODO DEL CLUSTER DE KUBERNETES-------------

--Bastará con desinstalar las releases de helm:

		helm --namespace testing del db
		helm --namespace testing del cache
		helm --namespace testing del broker

--Y con eliminar el namespace se limpiarán el resto de recursos (deployments, services, pvcs... ):

		kubectl delete namespace testing