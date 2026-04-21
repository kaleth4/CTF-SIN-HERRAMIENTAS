```markdown
# README

## Resolución de Niveles 1 al 24 de OverTheWire Bandit con Python

Resolver los niveles 1 al 24 de OverTheWire Bandit utilizando exclusivamente scripts propios de Python puede tomarte entre 10 y 20 horas de trabajo neto, dependiendo de tu nivel de experiencia previa en scripting y redes.

### Desglose del Tiempo Estimado por Fases del Reto

1. **Niveles Iniciales (1-10): "Fundamentos"**
   - **Tiempo Estimado**: 2 a 4 horas.
   - **Complejidad**: Baja.
   - **Descripción**: La mayoría de estos niveles se resuelven con comandos básicos de Linux. Si decides automatizarlos con Python, pasarás más tiempo configurando la conexión SSH (usando librerías como Paramiko) o manipulando archivos locales que resolviendo el reto en sí.
   - **Enfoque Python**: Scripts simples para leer archivos con nombres especiales o buscar cadenas específicas dentro de archivos grandes.

2. **Niveles Intermedios (11-20): "Manipulación de Datos y Redes"**
   - **Tiempo Estimado**: 4 a 8 horas.
   - **Complejidad**: Media.
   - **Descripción**: Aquí enfrentas rotación de caracteres (ROT13), compresión múltiple y comunicación por sockets.
   - **Enfoque Python**: Crearás herramientas para decodificar datos y, lo más importante, usarás el módulo socket de Python para enviar y recibir datos a través de puertos específicos, lo cual es una excelente práctica de programación de red.

3. **Niveles Avanzados (21-24): "Scripting y Automatización"**
   - **Tiempo Estimado**: 4 a 8 horas.
   - **Complejidad**: Alta.
   - **Descripción**: Estos niveles requieren lógica de automatización real, como el nivel 24, donde debes realizar un ataque de fuerza bruta.
   - **Nivel 24 (Brute Force)**: Este nivel específico requiere probar 10,000 combinaciones de un PIN de 4 dígitos.
     - **Tiempo de Ejecución del Script**: Un script de Python bien hecho puede completar las 10,000 pruebas en menos de 5 minutos, dependiendo de la latencia de la red.
     - **Tiempo de Desarrollo**: Escribir el script para enviar la contraseña de bandit24 junto con cada PIN (contraseña PIN) a través de un socket te llevará unos 30-60 minutos si eres principiante.

### Factores que Afectan tu Tiempo
- **Curva de Aprendizaje de Python**: Si ya sabes manejar sockets y procesos en Python, el tiempo se reduce drásticamente. Si estás aprendiendo estas librerías mientras resuelves, el tiempo podría duplicarse.
- **Depuración (Debugging)**: La mayor parte del tiempo se pierde en entender por qué el servidor no responde como esperas o en manejar errores de conexión SSH.
- **Investigación**: Consultar la documentación oficial de Python para módulos como `timeit` o `subprocess` es parte esencial del proceso.

---

### Writeup & Resumen (Niveles 1-24)

Este documento detalla los conceptos y habilidades aplicadas para resolver los niveles del 1 al 24 de OverTheWire Bandit utilizando scripts personalizados de Python y comandos de terminal.

#### 📁 Fase 1: Fundamentos y Sistema de Archivos (0-10)
- **Conceptos Clave**: Lectura de archivos con caracteres especiales (guiones, espacios), búsqueda de archivos por atributos (tamaño, usuario, grupo) y filtrado de texto.
- **Habilidades de Python**: Uso de `os` y `subprocess` para listar directorios y leer archivos de difícil acceso.
- **Nivel Destacado**: Uso de strings y `grep` para encontrar contraseñas en archivos binarios.

#### 🔐 Fase 2: Criptografía Básica y Transferencia (11-15)
- **Conceptos Clave**: Decodificación de Base64, rotación de caracteres (ROT13) y descompresión de archivos anidados (GZIP, BZIP2, TAR).
- **Habilidades de Python**: Desarrollo de scripts para automatizar la descompresión recursiva y uso de la librería `codecs` para manipulación de strings.
- **Nivel Destacado**: Envío de la contraseña actual a un puerto específico en localhost para recibir la siguiente.

#### 📡 Fase 3: Comunicación por Red y SSH (16-20)
- **Conceptos Clave**: Escaneo de puertos (identificar cuáles hablan SSL), uso de certificados RSA para acceso sin contraseña y comunicación a través de sockets.
- **Habilidades de Python**: Uso del módulo `socket` para crear clientes TCP y `ssl` para establecer conexiones seguras (sustituyendo a `openssl s_client`).
- **Nivel Destacado**: Implementación de un script que escucha en un puerto local para interceptar una señal del servidor.

#### 🤖 Fase 4: Automatización y Fuerza Bruta (21-24)
- **Conceptos Clave**: Análisis de scripts en `/etc/cron.d/`, explotación de race conditions simples y bypass de scripts de shell.
- **Habilidades de Python**: Creación de un "Brute Forcer" para el nivel 24.
- **El Reto del PIN (24)**: Desarrollo de un script que itera del 0000 al 9999, enviando la combinación `[Contraseña_Bandit24] [PIN]` a través de una conexión de socket persistente.

### 🛠 Tecnologías Utilizadas
- **Lenguaje**: Python 3.x
- **Librerías**: `socket`, `subprocess`, `base64`, `hashlib`, `paramiko` (opcional).
- **Herramientas Linux**: `ssh`, `ls`, `find`, `grep`, `cat`, `cut`, `nc`, `openssl`.

---

### Bandit Level 14 → Level 15

**Level Goal**

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

**Commands you may need to solve this level**
- `ssh`, `telnet`, `nc`, `openssl`, `s_client`, `nmap`

**Helpful Reading Material**
- [How the Internet works in 5 minutes (YouTube)](https://www.youtube.com) (Not completely accurate, but good enough for beginners)
- [IP Addresses](https://en.wikipedia.org/wiki/IP_address)
- [Localhost on Wikipedia](https://en.wikipedia.org/wiki/Localhost)
- [Ports](https://en.wikipedia.org/wiki/Port_(computer_networking))

---

### Metodología Personal

La metodología que todo el mundo usó fue la de `nc ip + puerto` y enviaron la contraseña y ya.

**Mi Metodología**

Utilicé un escáner de puertos creado por mí y ver los abiertos y utilizar una función de Linux **Redirección con Bash** (`/dev/tcp`).

Esta es la opción más avanzada y "pura" de Linux, ya que utiliza descriptores de archivo de Bash para abrir un socket de red. El comando en una sola línea (One-liner):

```bash
cat /etc/bandit_pass/bandit14 | (exec 3<>/dev/tcp/localhost/30000; cat >&3; cat <&3)
```

### Mi Port Scanner

```python
import socket

target = "127.0.0.1"
ports = [21, 22, 80, 443]

def print_banner():
    print("""
K P O R T S C A N N E R v5.0 (kaleth corcho)
""")

def main():
    print_banner()  # ← llamas el banner

    for port in ports:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(0.2)

        if s.connect_ex((target, port)) == 0:
            print(f"[+] Puerto {port} OPEN")

        s.close()

if __name__ == "__main__":
    main()
```

### Nivel 20 sin `nc` ni Herramientas Automatizadas

Este comando crea un servidor de red minimalista en una sola línea. Básicamente, le dice a Python que se quede esperando a que el programa `./suconnect` lo llame para entregarle la contraseña.

```bash
python3 -c 'import socket; s=socket.socket(); s.bind(("localhost", 4444)); s.listen(1
