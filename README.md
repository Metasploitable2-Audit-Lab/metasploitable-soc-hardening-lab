# 🛡️ Laboratorio de Auditoría Forense y Monitoreo Local en Linux (Metasploitable)

Este repositorio contiene la documentación, comandos ejecutados y análisis forense desarrollados durante el laboratorio práctico de ciberseguridad sobre una máquina **Metasploitable** (Linux víctima). El objetivo del proyecto es simular las tareas de un **Analista de SOC** (Security Operations Center) evaluando la infraestructura, gestionando permisos de archivos y auditando eventos de red.

## 🗺️ Estructura del Laboratorio

### 1. Acceso y Configuración de Terminal
- **Conexión:** Acceso remoto mediante el protocolo `Telnet` (Puerto 23).
- **Resolución de Errores:** Mitigación del error `'xterm-256color': unknown terminal type` forzando la variable de entorno nativa en el objetivo:
  ```bash
  export TERM=xterm
  ```

### 2. Monitoreo Local y Análisis de Hardware (Módulo 3)
- **Rendimiento de CPU y RAM:** Uso de `top` para verificar la salud del sistema.
  - *Resultado:* Servidor estable. CPU ociosa en un `99.3% id`. Memoria RAM con `314,640k` libres de un total de ~512MB.
- **Almacenamiento Físico:** Uso del comando `df -h` para verificar que el almacenamiento de logs no sufra denegación de servicio.
  - *Resultado:* Partición raíz (`/`) con apenas un **22%** de uso (3.3G libres de 4.4G disponibles).

### 3. Seguridad de Archivos y Mitigación de Permisos (Módulo 2)
- **Blindaje de Directorios:** Localización de la carpeta confidencial `mysql-ssl` expuesta debido a permisos heredados blandos.
- **Aislamiento Forense:** Restricción de acceso total aplicando el principio de mínimo privilegio:
  ```bash
  chmod 700 mysql-ssl/
  ```
  Esto asegura permisos exclusivos de lectura, escritura y ejecución (`rwx------`) únicamente para el propietario del servicio.

### 4. Auditoría de Redes e Inicios de Sesión (Módulos 3 y 4)
- **Mapeo de Puertos Activos:** Uso del comando `netstat -tuln` desde adentro de la víctima.
  - *Resultado:* Confirmación de múltiples servicios (Puertos 21, 22, 23, 25, 80) escuchando en la IP `0.0.0.0`, lo que ratifica la total exposición perimetral ante la ausencia de un Firewall.
- **Historial Forense de Conexiones:** Ejecución de `last -n 10` para trazar la línea de tiempo de accesos establecidos.
  - *Evidencia:* Correlación de la IP atacante/auditora (`10.0.0.3`) interactuando con la IP víctima (`10.0.0.2`).
- **Prueba de Conectividad Externa:** Ejecución de `ping -c 4 1.1.1.1`.
  - *Resultado:* Error `connect: Network is unreachable`. Confirma el aislamiento controlado del entorno de red interna (sin puerta de enlace/gateway hacia internet).

## 📊 Resumen Técnico del Auditor

| Componente | Comando | Estado Detectado | Severidad |
| :--- | :--- | :--- | :--- |
| **Acceso Perimetral** | `netstat -tuln` | Múltiples puertos críticos expuestos en `0.0.0.0` | 🔴 Crítica |
| **Cifrado de Tráfico** | `telnet` | Uso de texto plano para autenticación remota | 🔴 Crítica |
| **Hardware (CPU/RAM)** | `top` | Sistema ligero y estable (99.3% CPU libre) | 🟢 Saludable |
| **Espacio en Disco** | `df -h` | 22% en uso. Espacio suficiente para logs | 🟢 Saludable |
| **Permisos Locales** | `chmod 700` | Corregidos en bases de datos críticas | 🟡 Mitigado |

## 🛠️ Herramientas Utilizadas
- **Sistema Objetivo:** Metasploitable 2 Linux v2.0
- **Máquina Auditora:** Ubuntu Desktop Linux
- **Entorno de Red:** Red Interna Virtualizada (Host-Only / VirtualBox)

---
*Nota: Este laboratorio fue realizado con fines puramente educativos y de entrenamiento profesional en ciberseguridad defensiva.*
