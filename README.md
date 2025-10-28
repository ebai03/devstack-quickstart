# Guía de Inicio Rápido para DevStack
DevStack es una serie de scripts extensibles utilizados para crear rápidamente un entorno completo de OpenStack basado en las últimas versiones de todo desde git master. Se utiliza de forma interactiva como entorno de desarrollo y como base para gran parte de las pruebas funcionales del proyecto OpenStack.

El código fuente está disponible en: https://opendev.org/openstack/devstack

>**Advertencia**: DevStack realizará cambios sustanciales en el sistema durante la instalación. Solo ejecutar DevStack en servidores o máquinas virtuales dedicadas a este propósito.

## Guía de Inicio Rápido

### 1. Instalar Linux
Comenzar con una instalación limpia y mínima de un sistema Linux. DevStack intenta soportar las dos últimas versiones LTS de Ubuntu, Rocky Linux 9 y openEuler.

Si no hay una preferencia específica, **Ubuntu 24.04 (Noble)** es la más probada y probablemente será la más fluida (según la documentación oficial).

### 2. Agregar Usuario Stack (opcional)
DevStack debe ejecutarse como un usuario no root con sudo habilitado (los logins estándar a imágenes en la nube como "ubuntu" o "cloud-user" generalmente funcionan bien).

Si no se está usando una imagen en la nube, se puede crear un usuario stack separado para ejecutar DevStack:
```bash
sudo useradd -s /bin/bash -d /opt/stack -m stack
```

![img1](/img/sc1.png)

Asegurar que el directorio home del usuario stack tenga permisos de ejecución para todos, ya que las distribuciones basadas en RHEL lo crean con 700 y Ubuntu 21.04+ con 750, lo que puede causar problemas durante el despliegue:

```bash
sudo chmod +x /opt/stack
```

Dado que este usuario realizará muchos cambios en el sistema, debe tener privilegios de sudo:

```bash
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
sudo -u stack -i
```

### 3. Descargar DevStack
```bash
git clone https://opendev.org/openstack/devstack
cd devstack
```

El repositorio devstack contiene un script que instala OpenStack y plantillas para archivos de configuración.

### 4. Crear un local.conf
Crear un archivo `local.conf` con cuatro contraseñas preestablecidas en la raíz del repositorio git de devstack:
```ini
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD
```

Esta es la configuración mínima requerida para comenzar con DevStack.

>**Nota**: Hay un archivo [local.conf de ejemplo](https://docs.openstack.org/devstack/latest/_downloads/d6fbba8d6ab5e970a86dd2ca0b884098/local.conf) en el directorio samples en el repositorio devstack.

> **Advertencia**: Usar solo caracteres alfanuméricos en las contraseñas, ya que algunos servicios fallan al usar caracteres especiales.

### 5. Iniciar la Instalación

```bash
./stack.sh
```

Esto tomará de 15 a 30 minutos, dependiendo en gran medida de la velocidad de la conexión a internet. Durante este proceso se instalarán muchos árboles git y paquetes.
## Resultado de la Instalación

Al completarse la instalación, el entorno DevStack estará funcionando con los siguientes servicios instalados:

- **Keystone** (servicio de identidad)
- **Glance** (servicio de imágenes)
- **Nova** (servicio de computación)
- **Placement** (servicio de ubicación de recursos)
- **Cinder** (servicio de almacenamiento en bloque)
- **Neutron** (servicio de redes)
- **Horizon** (interfaz web)

Las IPs flotantes estarán disponibles y las instancias tendrán acceso al mundo exterior.

Al finalizar la instalación, el script mostrará las direcciones sobre las que trabaja Horizon (GUI web):

![img2](/img/sc2.png)

Finalmente se deben cargar las variables de entorno necesarias para que la OpenStack CLI se autentique y conecte con los servicios de OpenStack:
```sh
# Desde el mismo directorio donde se encuentra devstack
source openrc admin admin
```
### Funcionalidades disponibles:

1. **Acceso a Horizon**: La interfaz web de OpenStack permite administrar VMs, redes, volúmenes e imágenes.

![img3](/img/sc3.png)

2. **Línea de comandos**: Después de hacer source de openrc en el shell, se puede usar la herramienta de línea de comandos openstack para administrar el entorno devstack.

![img4](/img/sc4.png)

3. **Creación y acceso a VMs**: Es posible crear máquinas virtuales y acceder a ellas mediante SSH.

4. **Pruebas Tempest**: En `/opt/stack/tempest` se pueden ejecutar pruebas tempest configuradas para trabajar con el entorno devstack.

5. **Validación de cambios**: Se pueden realizar cambios de código a OpenStack y validarlos en el entorno.

## Información del Entorno de Implementación

- **Sistema Operativo**: Ubuntu 24.04 (máquina virtual)
- **Usuario**: Implementación siguiendo las mejores prácticas con usuario stack
- **Propósito**: Investigación sobre SDC y demostración de capacidades del componente Nova de OpenStack

---

*Documento basado en la documentación oficial de DevStack disponible en https://opendev.org/openstack/devstack*
