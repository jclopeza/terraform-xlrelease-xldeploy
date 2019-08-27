# Terraform templates
Esto es lo primero que necesitamos.

Si empezamos a trabajar con Terraform, pronto descubriremos los módulos y nos daremos cuenta que es la mejor forma de reutilizar nuestro trabajo. A medida que vayamos mejorando nuestros módulos, podremos ir versionándolos y siempre sabremos de forma exacta con qué código fuente estamos creando nuestra infraestructura.

En este tutorial vamos a utilizar el siguiente módulo:

https://registry.terraform.io/modules/jclopeza/java-bdd-project/module/2.0.0

## Módulo `Java Bdd Project`
Este módulo de Terraform se va a utilizar para proyectos con parte Java (o un frontal ejecutándose en Tomcat en el puerto 8080) y con parte de base de datos.

Creará una VPC, con una subred pública y dos instancias EC2. Una de ellas con acceso a los puertos 22 y 8080 (parte front) y la otra con acceso a los puertos 22 y 3306 (parte de base de datos).

Este módulo está parametrizado porque quiero reutilizarlo para provisionar varios entornos. Las variables que acepta se pueden consultar aquí:

https://registry.terraform.io/modules/jclopeza/java-bdd-project/module/2.0.0?tab=inputs

Y como salida obtendré, entre otras variables, las direcciones IP públicas de las dos instancias EC2 que se han creado.

https://registry.terraform.io/modules/jclopeza/java-bdd-project/module/2.0.0?tab=outputs

We assume that these files have been created by the right teams, have been reviewed, approved, versioned and published in the Terraform registry for later use. These modules comply with the best practices and meet the security requirements. They have been created using the corresponding templates in XL Release.

## ¿Cómo usar este módulo?
Para utilizar este módulo que está publicado en el registry de Terraform, bastarán crear los siguientes templates de Terraform:

También se pueden crear tres ficheros distintos, uno de ellos con las variables, otro con los outputs, y otro con la llamada al módulo. Quedaría así:

Fichero `variables.tf`
```
variable "aws_region" {}
variable "environment" {}
variable "instance_type" {}
variable "public_key_path" {}
variable "private_key_path" {}
variable "project_name" {}
```

Fichero `terraform.tf`
```
module "java-bdd-project" {
  source  = "jclopeza/java-bdd-project/module"
  version = "2.0.0"
  aws_region = "${var.aws_region}"
  environment = "${var.environment}"
  instance_type = "${var.instance_type}"
  project_name = "${var.project_name}"
  public_key_path = "${var.public_key_path}"
  private_key_path = "${var.private_key_path}"
}
```

Fichero `outputs.tf`
```
output "public_ip_front" {
  value = "${module.java-bdd-project.public_ip_front}"
}
output "public_ip_bdd" {
  value = "${module.java-bdd-project.public_ip_bdd}"
}
output "environment" {
  value = "${var.environment}"
}
output "private_key_path" {
  value = "${var.private_key_path}"
}
```

Ahora, cómo lanzamos estas templates?, desde qué equipo, con qué parámetros, cómo los gestionamos?
