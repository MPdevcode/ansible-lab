# Laboratorio de Ansible con Docker

## Objetivo de la práctica

Crear un laboratorio con dos servidores Linux basados en Ubuntu 24.04 mediante Docker y administrarlos de forma centralizada utilizando Ansible. El playbook obtiene información de cada servidor, crea directorios y archivos, utiliza un `loop` y ejecuta una tarea condicional para Ubuntu.

## Estructura del proyecto

```text
ansible-lab/
├── docker-compose.yml
├── inventory.ini
├── playbook.yml
└── README.md
```

## Requisitos

- Docker Engine y Docker Compose.
- Ansible instalado en el equipo que actuará como controlador.
- Colección `community.docker`.
- Python 3 y el paquete Docker SDK en el controlador.

> En Windows se recomienda utilizar WSL2 con Ubuntu y Docker Desktop con integración WSL habilitada. Los comandos de Ansible deben ejecutarse desde la terminal de Ubuntu (WSL), dentro de esta carpeta.

## 1. Instalar los componentes de Ansible

En Ubuntu/WSL, instala Ansible y el SDK de Docker:

```bash
sudo apt update
sudo apt install -y ansible python3-pip
python3 -m pip install --user docker --break-system-packages
ansible-galaxy collection install community.docker
```

Si tu distribución no admite `--break-system-packages`, utiliza un entorno virtual de Python e instala `docker` dentro de él.

## 2. Iniciar los contenedores

Desde la carpeta `ansible-lab`, ejecuta:

```bash
docker compose up -d
```

Verifica que los dos contenedores estén activos:

```bash
docker ps
```

## 3. Instalar Python en los servidores

Ansible necesita Python en los hosts administrados. Ejecuta:

```bash
docker exec server1 bash -c "apt-get update && apt-get install -y python3"
docker exec server2 bash -c "apt-get update && apt-get install -y python3"
```

## 4. Comprobar la conexión

El inventario define los dos contenedores como hosts administrados mediante la conexión Docker:

```bash
ansible docker -i inventory.ini -m ping
```

La respuesta esperada para ambos hosts contiene `"ping": "pong"`.

## 5. Ejecutar el playbook

```bash
ansible-playbook -i inventory.ini playbook.yml
```

El playbook realiza estas tareas en ambos servidores:

1. Muestra el nombre del host.
2. Muestra la distribución y versión del sistema operativo.
3. Crea `/tmp/ansible-demo`.
4. Crea `info.txt` con el nombre del servidor y el sistema operativo.
5. Crea `logs`, `backup` y `config` mediante un `loop`.
6. Muestra un mensaje únicamente cuando la distribución detectada es Ubuntu.

## 6. Verificar los resultados

Puedes comprobar los archivos creados en cada contenedor:

```bash
docker exec server1 find /tmp/ansible-demo -maxdepth 2 -type f -o -type d
docker exec server2 find /tmp/ansible-demo -maxdepth 2 -type f -o -type d
```

Consulta el contenido de los archivos:

```bash
docker exec server1 cat /tmp/ansible-demo/info.txt
docker exec server2 cat /tmp/ansible-demo/info.txt
```

## 7. Captura de pantalla de la ejecución

**Pendiente de completar:** ejecuta el playbook correctamente en tu equipo y toma una captura de la terminal donde se vean los dos servidores y el resumen final de Ansible (`PLAY RECAP`).

Guarda la imagen en esta carpeta con el nombre:

```text
captura-ejecucion.png
```

Luego agrega la imagen a este README con:

```markdown
![Ejecución exitosa del playbook](captura-ejecucion.png)
```

No se incluye una captura simulada: la evidencia debe corresponder a la ejecución real del laboratorio.

## 8. Detener el laboratorio

Cuando termines:

```bash
docker compose down
```

## Archivos entregables

- `docker-compose.yml`
- `inventory.ini`
- `playbook.yml`
- `README.md`
- `captura-ejecucion.png` (evidencia real que debes agregar después de ejecutar el laboratorio).
