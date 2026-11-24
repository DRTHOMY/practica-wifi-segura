# Informe de Auditoría de Red Wi-Fi Insegura

**Autor:** Thomas De Santis
**Fecha:** 24 de noviembre de 2026
**Rol simulado:** Auditor de seguridad junior

---

## 1. Introducción

Cuando me conecto a una red Wi-Fi pública (una cafetería, un aeropuerto, un hotel) comparto el "aire" con decenas de desconocidos. En esta práctica quise comprobar con mis propios ojos qué información viaja al descubierto cuando navego por un sitio que usa **HTTP** en lugar de **HTTPS**, y explicar cómo una **VPN** cambia ese escenario.

Para eso usé las herramientas de desarrollador del navegador (pestaña **Network**) y analicé la solicitud que hace mi equipo al visitar un sitio sin cifrado.

## 2. Sitio analizado

- **Host visitado:** `neverssl.com`
- **URL:** `http://neverssl.com`
- **Protocolo:** HTTP (sin cifrado)

Elegí este sitio porque está pensado justamente para funcionar siempre por HTTP, así que permite ver cómo es una comunicación sin protección.

## 3. Evidencia observada

Al visitar `http://neverssl.com` y revisar la pestaña **Network** de las herramientas de desarrollador del navegador (F12), la primera solicitud expone los siguientes datos:

| Dato | Qué muestra |
|------|-------------|
| URL solicitada | `http://neverssl.com/` |
| Método HTTP | `GET` |
| Host | `neverssl.com` |
| Protocolo | HTTP/1.1, sin cifrado (puerto 80) |
| Headers enviados | Información adicional que el navegador manda automáticamente (`Accept`, `Accept-Language`, etc.) |
| User-Agent | Cadena que identifica el navegador, su versión y el sistema operativo |

**Estructura de la solicitud enviada:**

```
GET / HTTP/1.1
Host: neverssl.com
User-Agent: <navegador, versión y sistema operativo>
Accept: <tipos de contenido que acepta el navegador>
Accept-Language: <idiomas preferidos>
```

Todos estos campos viajan en texto plano, legibles para cualquiera que tenga acceso al tráfico.

### ¿Qué protocolo utiliza el sitio?

Utiliza **HTTP**, no HTTPS. Lo confirmo por el esquema `http://` de la URL, la ausencia del candado en el navegador (que marca el sitio como "No seguro") y porque la solicitud viaja al puerto 80.

### ¿Qué información puede observarse durante la solicitud?

Sin ningún esfuerzo, en la solicitud quedan a la vista al menos estos elementos:

- **Host:** el dominio al que me estoy conectando.
- **URL:** la ruta exacta que pido.
- **Método:** `GET`, que indica qué tipo de operación hago.
- **Headers:** información adicional que mi navegador envía solo.
- **User-Agent:** revela mi navegador, su versión y mi sistema operativo.

## 4. Riesgos encontrados

Navegar por HTTP desde una Wi-Fi pública tiene varios riesgos, porque todo viaja en **texto plano**:

1. **Intercepción de datos (sniffing):** alguien conectado a la misma red puede usar una herramienta como Wireshark para "escuchar" los paquetes y leer lo que envío y recibo.
2. **Robo de credenciales y sesiones:** si ingresara usuario y contraseña en un sitio HTTP, o si mi sesión usara cookies sin protección, un atacante podría capturarlas y suplantarme.
3. **Ataques de intermediario (Man-in-the-Middle):** un atacante puede hacer que mi equipo crea que él es el router, de modo que todo mi tráfico pase primero por su computadora, donde puede leerlo o incluso modificarlo (por ejemplo, inyectar contenido en la página).
4. **Redes malignas (Evil Twin):** puedo conectarme sin darme cuenta a un hotspot falso con un nombre parecido al del local, creado solo para robar datos.
5. **Perfilado y rastreo:** aunque no haya contraseñas, los hábitos de navegación, los sitios visitados y los datos del equipo quedan expuestos.

Un detalle que aprendí: que la red Wi-Fi tenga contraseña **no** la hace segura, porque si todos los clientes comparten esa clave, todos pueden intentar descifrar el tráfico de los demás.

## 5. Cómo ayuda una VPN

Una **VPN** (red privada virtual) cambia por completo el escenario:

- **Cifrado:** mi equipo cifra los datos antes de que salgan hacia la red. Aunque un atacante los capture, solo verá ruido ilegible.
- **Túnel seguro:** la VPN crea un túnel entre mi dispositivo y el servidor VPN. Dentro de ese túnel viaja todo mi tráfico, incluso el que usa un protocolo inseguro como HTTP.
- **Encapsulamiento:** cada paquete original se encapsula dentro de otro paquete cifrado. Para quien mira la red local, solo se ve una conexión hacia el servidor VPN, no el contenido ni los destinos reales.
- **Protección del tráfico y privacidad:** el dueño del Wi-Fi y cualquier curioso en la red no pueden ver qué sitios visito ni qué datos envío.

Aclaración importante: la VPN protege el tramo entre mi equipo y el servidor VPN. Desde ahí hacia el sitio final, el tráfico HTTP sigue sin cifrar, así que la VPN **no reemplaza a HTTPS**; ambas se complementan.

## 6. Mis 3 Reglas de Oro para redes Wi-Fi públicas

1. **Usar siempre VPN.** Antes de hacer cualquier cosa importante en una red pública, activo la VPN para que mi tráfico viaje cifrado dentro de un túnel.
2. **Verificar que el sitio use HTTPS.** Compruebo el candado y el `https://` antes de ingresar datos. Si un sitio pide credenciales por HTTP, no las escribo.
3. **Reducir la exposición.** Desactivo la compartición de archivos, olvido las redes que ya no uso, evito conectarme a redes con nombres sospechosos y mantengo mi sistema y mi navegador actualizados.

---

## Conclusión

Esta práctica me mostró que con solo abrir la pestaña Network se ve cuánta información sale de mi equipo en cada solicitud, y que en HTTP todo eso viaja a la vista de cualquiera. Entender esto cambia la forma en que uso las redes públicas: HTTPS y VPN dejan de ser "recomendaciones" y pasan a ser hábitos básicos.
