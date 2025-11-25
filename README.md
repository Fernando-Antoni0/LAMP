Despliegue Automático Masivo de Entornos LAMP (Híbrido AWS/VMware)

![Ansible](https://img.shields.io/badge/ansible-%231A1918.svg?style=for-the-badge&logo=ansible&logoColor=white)
![Jenkins](https://img.shields.io/badge/jenkins-%232C5263.svg?style=for-the-badge&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![VMware](https://img.shields.io/badge/vmware-%23607078.svg?style=for-the-badge&logo=vmware&logoColor=white)

Este proyecto implementa un sistema de automatización completo para el despliegue y gestión de entornos **LAMP (Linux, Apache, MySQL, PHP)** en una infraestructura híbrida. Utiliza **Ansible** como herramienta de Infraestructura como Código (IaC) y **Jenkins** para orquestar pipelines de CI/CD.

El sistema es capaz de provisionar y configurar servidores simultáneamente en:
* **Nube:** Contenedores Docker alojados en instancias EC2 de AWS.
* **Local:** Máquinas virtuales en VMware Workstation distribuidas en laptops.

Además, incluye un módulo de **Seguridad Forense** para la preservación de evidencia digital en caso de incidentes.

Integrantes del Equipo

* **Fernando Antonio Pozo Martínez**
* **Gustavo Hernández Jiménez**
* **Jair Soto Saucedo**
* **Leilani Alvarado Joaquín**
* **Leonel Alberto Medina Nájera**
* **Leslie Griselle Lopez Cruz**

---

Arquitectura y Flujo de Trabajo

El proyecto se gestiona desde un **Nodo de Control (Ubuntu)** que ejecuta Jenkins y Ansible.

1.  **Desarrollo:** Los cambios se suben a este repositorio (rama `main`).
2.  **CI (Integración Continua):** Jenkins detecta los cambios, inicia un **agente Docker** (`python:3.10-slim`) y ejecuta pruebas de calidad de código (`ansible-lint`).
3.  **CD (Despliegue Continuo):** Si las pruebas pasan, Jenkins ejecuta el playbook maestro de Ansible.
4.  **Provisionamiento Híbrido:**
    * **AWS:** Se conecta vía SSH a instancias EC2 y orquesta contenedores Docker.
    * **VMware:** Se conecta a las VMs.
5.  **Configuración:** Ansible instala y configura el stack LAMP en todos los nodos y verifica el servicio desplegando una página `info.php`.

---

## 📂 Estructura del Proyecto

```text
.
├── roles/
│   ├── docker/              # Orquestación de contenedores en AWS
│   ├── vmware_provisioning/ # Automatización de clonación en VMware
│   └── lamp_stack/          # Instalación agnóstica de Apache/MySQL/PHP
├── hosts.ini                # Inventario (Dinámico y Estático)
├── playbook.yml             # Playbook Maestro (Despliegue LAMP)
├── playbook_snapshot.yml    # Playbook Forense (Snapshots AWS)
├── Jenkinsfile              # Pipeline de CI/CD (Automático)
├── Jenkinsfile_snapshot     # Pipeline de Seguridad (Botón de Pánico)
├── ansible.cfg              # Configuración de Ansible
└── .gitignore               # Archivos excluidos
