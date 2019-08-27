# ¿Dónde almacenar y ejecutar estas templates?
Hemos creado tres ficheros, `variables.tf`, `terraform.tf` y `outputs.tf`.

Voy a desplegar estas templates con XL Deploy, la mejor herramienta del mercado para desplegar software. Al utilizar XL Deploy tendremos resueltas muchas de las preguntas que nos hicimos anteriormente.

## Cargar las templates de Terraform en XL Deploy
Vamos a crear una nueva aplicación en XL Deploy en la que registremos las distintas versiones o Deployment Package. Para ello se pueden utilizar los siguientes comandos:
```
$ git clone https://github.com/jclopeza/xl-deploy-apps.git
$ cd xl-deploy-apps/application-infrastructure-java-bdd-project
$ xl apply -f app.yaml --values project=petportal,version=1.0.0
```
Esto creará una nueva aplicación de nombre 'infrastructure-petportal' bajo el directorio 'Infrastructures' y creará el Deployment Package de nombre 1.0.0.
```
Applications
└── Infrastructures
    └── infrastructure-petportal
        └── 1.0.0
            ├── infrastructure-petportal
            ├── template-host-bdd
            └── template-host-front
```
Esta versión de nuestra infraestructura tendrá un único componente de tipo 'terraform.Module' y tendrá definidas unas 'Inputs Variables'
* aws_region
* environment
* instance_type
* project_name
* public_key_path
* private_key_path

Pero el valor de estas variables no está definido, en su lugar, hemos puesto unos 'placeholders' que es lo que me permitirá reutilizar esta misma versión de mi infraestructura para provisionar varios entornos. Esto lo gestionaré mediante diccionarios, en ellos almacenaré la configuración y los distintos valores que aplicaré a los entornos de desarrollo, preproducción y produccción.

Además del componente de infraestructura, hemos asociado dos templates. ¿Para qué?. Bien, cuando apliquemos las templates de Terraform, se crearán dos nuevas instancias EC2 en Amazon a las que luego querremos acceder para instalar software, aplicaciones, etc. Cada una de estas instancias tendrá una dirección IP y la clave pública que hemos pasado como parámetro.

Con las templates que hemos asociado, automáticamente se crearán en XL Deploy dos CIs de infraestructura del tipo overthere.SshHost listos para utilizarse y asociarse a entornos de despliegue.