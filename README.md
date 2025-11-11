# 🚀 Instalación y Configuración de Docker con Usuario y Acceso SSH

Este documento describe el proceso completo para instalar **Docker**, crear un usuario con permisos adecuados (sudo y docker), y configurar el acceso **SSH** seguro, incluyendo el cambio de puerto.

---

## 🧩 1. Instalación de Docker

Ejecuta los siguientes comandos como **root** o con un usuario con privilegios de `sudo`:

```bash
# Actualizar el sistema
sudo apt update && sudo apt upgrade -y

# Instalar dependencias necesarias
sudo apt install -y ca-certificates curl gnupg lsb-release

# Agregar la clave GPG oficial de Docker
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Agregar el repositorio oficial de Docker
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg]   https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker Engine, CLI, containerd y Docker Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Habilitar e iniciar el servicio Docker
sudo systemctl enable docker
sudo systemctl start docker

# Verificar la instalación
sudo docker --version
sudo docker compose version
```

---

## 👤 2. Crear un usuario con permisos para Docker y Sudo

```bash
# Crear el usuario (reemplaza "usuario" por el nombre deseado)
sudo adduser usuario

# Agregar el usuario al grupo docker
sudo usermod -aG docker usuario

# Agregar el usuario al grupo sudo
sudo usermod -aG sudo usuario
```

🔹 **Nota:**  
- El grupo `docker` permite ejecutar comandos sin `sudo`.  
- Para aplicar los cambios de grupo, el usuario debe cerrar y volver a iniciar sesión.

---

## 🔐 3. Configuración de SSH (con cambio de puerto)

### 3.1 Editar configuración del servicio SSH

```bash
sudo nano /etc/ssh/sshd_config
```

Busca y modifica (o agrega) las siguientes líneas:

```
# Desactivar acceso root
PermitRootLogin no

# Permitir autenticación por contraseña (puede cambiarse a "no" si usas llaves SSH)
PasswordAuthentication yes

# Permitir solo al nuevo usuario
AllowUsers usuario

# Cambiar el puerto SSH (por ejemplo, 2222)
Port 2222
```

### 3.2 Reiniciar el servicio SSH

```bash
sudo systemctl restart ssh
```

### 3.3 Configurar el firewall (si está activo)

```bash
sudo ufw allow 2222/tcp
sudo ufw reload
```

### 3.4 Conectarse al servidor

```bash
ssh usuario@IP_DEL_SERVIDOR -p 2222
```

---

## 🐳 4. Verificar permisos de Docker

Con el nuevo usuario, verifica que Docker se ejecute sin `sudo`:

```bash
docker run hello-world
```

Si el mensaje indica que Docker se ejecutó correctamente, la configuración es correcta.

---

## ⚙️ 5. Ejemplo de uso con Docker Compose

### 📁 Estructura de ejemplo

```bash
mi_proyecto/
├── docker-compose.yml
└── app/
    └── index.html
```

### 🧱 Ejemplo de `docker-compose.yml`

```yaml
version: "3.8"
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./app:/usr/share/nginx/html
```

### ▶️ Iniciar los contenedores

```bash
docker compose up -d --build
```

### ⏹️ Detener y eliminar los contenedores

```bash
docker compose down --rmi all
```

---

## ✅ Verificación final

```bash
# Verificar estado de Docker
systemctl status docker

# Verificar contenedores activos
docker ps

# Verificar redes y volúmenes creados
docker network ls
docker volume ls
```

---
