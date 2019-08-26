# Cómo provisiono la nueva infraestructura creada?
Tenemos creada nuestra infraestructura en Amazon (dos instancias EC2) y hemos creado dos nuevas máquinas en XL Deploy con las direcciones IP y demás información necesaria para acceder a ellas.

Ahora queremos instalar el middleware necesario en estas máquinas para poder ejecutar nuestras aplicaciones (tomcat, mysql-server, etc.).

Sería deseable que los playbooks de Ansible que se utilizasen para el provisioning, estuviesen versionados y que tuviésemos la oportunidad de seleccionar qué versión de los playbooks es la que queremos utilizar para instalar el middleware en nuestras instancias EC2.

Esta será nuestra cuarta fase en XL Release.

## Provisioning

Vamos a crear una cuarta fase en XL Release en la que vamos a proviosnar nuestras instancias EC2 recién creadas.

### Paso 1: Obtención de las versiones disponibles de playbooks (Script: Jython Script)
En el siguiente repositorio Git, tenemos los playbooks de Ansible con los distintos roles que se necesitan para provisionar las nuevas instancias EC2 `https://github.com/jclopeza/playbooks-provisioning`

En este repositorio hay varias versiones disponibles. El primer paso será obtener todas las versiones disponibles. Lo conseguimos con el siguiente código:
```
import json
gitServer = 'https://api.github.com'
request = HttpRequest({'url': gitServer})
response = request.get('/repos/jclopeza/playbooks-provisioning/tags', contentType='application/json')
listTags = []
data = json.loads(response.response)
for i in data:
    listTags = listTags + [i['name']]
releaseVariables['list_tags'] = listTags
```

### Paso 2: Selección de versión de provisioning (User Input)
En este paso se muestran todas las versiones existentes en el repositorio Git para que el usuario seleccione cuál desea utilizar para provisionar las instancias EC2.

Las versiones se obtuvieron en el paso anterior.

### Paso 3: Checkout a la versión de los playbooks seleccionada (Remote Script: Unix)
En este paso clonamos el repositorio Git con los playbooks de Ansible y hacemos checkout a la versión previamente seleccionada.
```
cd /tmp && rm -fr playbooks-provisioning
git clone https://github.com/jclopeza/playbooks-provisioning.git
cd playbooks-provisioning
git checkout ${tag_ansible_selected}
```
