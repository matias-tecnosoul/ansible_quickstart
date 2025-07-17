# Ansible + Nginx + Vagrant - Laboratorio de Aprendizaje

Este repositorio contiene ejemplos prácticos para aprender Ansible desplegando Nginx en máquinas virtuales Vagrant.

## 📋 Tabla de Contenidos

- [Requisitos](#requisitos)
- [Setup Inicial](#setup-inicial)
- [Estructura del Proyecto](#estructura-del-proyecto)
- [Configuración](#configuración)
- [Ejemplos Disponibles](#ejemplos-disponibles)
- [Comandos de Ejecución](#comandos-de-ejecución)
- [Troubleshooting](#troubleshooting)
- [Recursos Adicionales](#recursos-adicionales)

## 🛠️ Requisitos

### Software necesario:
- **VirtualBox** - Para ejecutar las VMs
- **Vagrant** - Para gestionar las VMs
- **Python 3.9+** - Para Ansible
- **pipx** - Para instalar Ansible globalmente

### Instalación en Arch/Manjaro:
```bash
# Instalar dependencias del sistema
sudo pacman -S virtualbox vagrant python-pipx

# Instalar Ansible globalmente
pipx install ansible-core

# Verificar instalación
ansible --version
ansible-playbook --version
```

### Instalación en Ubuntu/Debian:
```bash
# Instalar dependencias
sudo apt update
sudo apt install virtualbox vagrant python3-pip

# Instalar pipx
python3 -m pip install --user pipx
pipx ensurepath

# Instalar Ansible
pipx install ansible-core

# Verificar instalación
ansible --version
```

## 🚀 Setup Inicial

### 1. Clonar el repositorio:
```bash
git clone <tu-repo-url>
cd ansible-nginx-vagrant
```

### 2. Levantar VM con Vagrant:
```bash
# Levantar VM (debe estar configurada para usar IP 192.168.56.25)
vagrant up

# Verificar que la VM está corriendo
vagrant status

# (Opcional) Conectarse por SSH para verificar
vagrant ssh
```

### 3. Verificar conectividad:
```bash
# Test de ping SSH
ansible webservers -i inventory.ini -m ping

# Si funciona, deberías ver:
# vagrant-vm | SUCCESS => { "ping": "pong" }
```

## 📁 Estructura del Proyecto

```
ansible-nginx-vagrant/
├── README.md                    # Este archivo
├── Vagrantfile                  # Configuración de la VM
├── inventory.ini                # Inventario de servidores
├── ansible.cfg                  # Configuración de Ansible (opcional)
├── playbooks/                   # Playbooks principales
│   ├── nginx-minimal.yml        # Instalación básica de nginx
│   ├── nginx-custom-inline.yml  # Página personalizada (inline)
│   ├── nginx-custom-file.yml    # Página desde archivo externo
│   ├── nginx-template.yml       # Página con template Jinja2
│   ├── test-connection.yml      # Test de conectividad
│   └── network-info.yml         # Información de red detallada
├── files/                       # Archivos estáticos
│   ├── index.html              # Página HTML personalizada
│   └── style.css               # Estilos CSS
├── templates/                   # Templates Jinja2
│   └── index.html.j2           # Template de página web
└── docs/                       # Documentación adicional
    └── examples.md             # Ejemplos y casos de uso
```

## ⚙️ Configuración

### Archivo inventory.ini:
```ini
[webservers]
vagrant-vm ansible_host=192.168.56.25 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/default/virtualbox/private_key

[webservers:vars]
ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

**Nota:** Ajusta la ruta de `ansible_ssh_private_key_file` según tu configuración de Vagrant.

### Archivo ansible.cfg (opcional):
```ini
[defaults]
inventory = inventory.ini
host_key_checking = False
timeout = 30

[privilege_escalation]
become = True
become_method = sudo
```

## 🎯 Ejemplos Disponibles

### 1. **nginx-minimal.yml** - Instalación básica
- Instala nginx
- Inicia el servicio
- Página por defecto de nginx

### 2. **nginx-custom-inline.yml** - Página personalizada (inline)
- Página HTML moderna con CSS incluido
- Muestra información del servidor automáticamente
- Responsive design

### 3. **nginx-custom-file.yml** - Página desde archivos externos
- Copia archivos HTML/CSS desde directorio `files/`
- Separación de código y contenido
- Ideal para sitios más complejos

### 4. **nginx-template.yml** - Template con variables
- Usa templates Jinja2
- Variables personalizables
- Generación dinámica de contenido

### 5. **test-connection.yml** - Diagnóstico
- Verifica conectividad
- Muestra información del sistema
- Debug de configuración

## 🚀 Comandos de Ejecución

### Test de conectividad:
```bash
# Verificar que Ansible puede conectarse
ansible webservers -i inventory.ini -m ping

# Información detallada del sistema
ansible-playbook -i inventory.ini playbooks/test-connection.yml

# Ver información de red
ansible-playbook -i inventory.ini playbooks/network-info.yml
```

### Instalación básica de Nginx:
```bash
# Instalación mínima
ansible-playbook -i inventory.ini playbooks/nginx-minimal.yml

# Verificar en navegador: http://192.168.56.25
curl http://192.168.56.25
```

### Página personalizada (inline):
```bash
# Desplegar página moderna con info del servidor
ansible-playbook -i inventory.ini playbooks/nginx-custom-inline.yml

# Ver resultado en navegador
firefox http://192.168.56.25
```

### Página desde archivos externos:
```bash
# Asegurarse de que existen los archivos
ls files/index.html files/style.css

# Desplegar
ansible-playbook -i inventory.ini playbooks/nginx-custom-file.yml
```

### Template con variables personalizadas:
```bash
# Ejecutar con variables por defecto
ansible-playbook -i inventory.ini playbooks/nginx-template.yml

# O con variables personalizadas
ansible-playbook -i inventory.ini playbooks/nginx-template.yml \
  -e "site_title='Mi Sitio Personalizado'" \
  -e "admin_email='mi@email.com'"
```

### Comandos útiles:
```bash
# Ejecutar en modo dry-run (ver qué cambiaría)
ansible-playbook -i inventory.ini playbooks/nginx-minimal.yml --check

# Ejecutar con verbose para debugging
ansible-playbook -i inventory.ini playbooks/nginx-minimal.yml -v

# Ejecutar solo tasks con tag específico
ansible-playbook -i inventory.ini playbooks/nginx-minimal.yml --tags="install"
```

## 🔧 Troubleshooting

### Problemas comunes:

#### 1. Error de SSH / Permission denied:
```bash
# Verificar permisos de la clave SSH
chmod 600 .vagrant/machines/default/virtualbox/private_key

# Test manual de SSH
ssh -i .vagrant/machines/default/virtualbox/private_key vagrant@192.168.56.25

# Si no funciona, usar password
ansible-playbook -i inventory.ini playbooks/nginx-minimal.yml --ask-pass
```

#### 2. VM no accesible:
```bash
# Verificar estado de la VM
vagrant status

# Reiniciar VM si es necesario
vagrant reload

# Ver configuración de red
vagrant ssh -c "ip addr show"
```

#### 3. Ansible no encuentra hosts:
```bash
# Verificar inventario
ansible-inventory -i inventory.ini --list

# Test de ping con debug
ansible webservers -i inventory.ini -m ping -vvv
```

#### 4. Nginx no responde:
```bash
# Verificar estado en la VM
vagrant ssh -c "sudo systemctl status nginx"

# Ver logs de nginx
vagrant ssh -c "sudo journalctl -u nginx -f"

# Verificar puertos abiertos
vagrant ssh -c "sudo ss -tlnp | grep :80"
```

## 🎓 Conceptos de Aprendizaje

Este laboratorio te enseña:

### Conceptos básicos de Ansible:
- ✅ **Inventarios** - Definir hosts y grupos
- ✅ **Playbooks** - Automatización declarativa
- ✅ **Tasks** - Unidades de trabajo individuales
- ✅ **Módulos** - package, service, copy, template
- ✅ **Variables** - Parametrización y reutilización
- ✅ **Templates** - Generación dinámica con Jinja2
- ✅ **Idempotencia** - Ejecución segura múltiple

### Buenas prácticas:
- ✅ **Backup automático** - `backup: yes`
- ✅ **Permisos correctos** - owner, group, mode
- ✅ **Organización de archivos** - files/, templates/
- ✅ **Variables de sistema** - ansible_facts
- ✅ **Verificación** - tasks de validación

## 🔗 Recursos Adicionales

### Documentación oficial:
- [Ansible Documentation](https://docs.ansible.com/)
- [Ansible Modules](https://docs.ansible.com/ansible/latest/collections/index_module.html)
- [Vagrant Documentation](https://www.vagrantup.com/docs)

### Comandos útiles de referencia:
```bash
# Ver módulos disponibles
ansible-doc -l

# Ayuda de módulo específico
ansible-doc copy

# Ver variables disponibles del host
ansible webservers -i inventory.ini -m setup

# Ver solo variables de red
ansible webservers -i inventory.ini -m setup -a "filter=ansible_default_ipv4"
```

## 📝 Notas

- **IP de acceso:** http://192.168.56.25
- **Usuario VM:** vagrant
- **Password VM:** vagrant (si se necesita)
- **Directorio web:** /var/www/html/
- **Backups:** Se crean automáticamente con extensión .backup

## 🤝 Contribuciones

¡Las contribuciones son bienvenidas! Por favor:

1. Fork el repositorio
2. Crea una rama para tu feature
3. Commit tus cambios
4. Push a la rama
5. Abre un Pull Request

## 📄 Licencia

Este proyecto está bajo licencia MIT. Ver archivo LICENSE para más detalles.

---

**¡Happy Learning!** 🚀

Si tienes preguntas o encuentras problemas, abre un issue en el repositorio.