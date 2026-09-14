# beebags-deploy

Canal de despliegue del conector de Bee Bags (SERVERHP).

**Aquí no hay código legible.** Cada archivo de `paquetes/` es un blob cifrado y autenticado con
AES-256-GCM. La llave vive únicamente en el `.env` del servidor y en la máquina desde la que se
despliega; nunca en este repositorio. Sin esa llave un paquete no se puede leer **ni fabricar**:
el servidor rechaza cualquier paquete cuyo tag de autenticación no cuadre.

## Cómo funciona

El servidor no expone nada a Internet. Cada minuto **sale** a leer `estado.json` de aquí. Si el
paquete que dice no es el que tiene puesto, lo baja, comprueba el hash y la firma, revisa que los
archivos vayan a rutas permitidas, respalda lo que va a pisar, instala, se reinicia y se prueba
solo. Si la prueba falla, se regresa a como estaba sin que nadie intervenga.

## `estado.json`

```json
{ "paquete": "p-260914-1105-a1b2", "sha256": "…", "nota": "qué cambia", "pausado": false }
```

- **Para regresar a una versión anterior** (se puede desde el celular): edita `estado.json`, pon el
  `paquete` anterior y su `sha256`, y guarda. El servidor se regresa en menos de dos minutos.
- **Para detener todos los despliegues**: pon `"pausado": true`.

## Lo que un paquete NO puede tocar

`.env`, las claves y sesiones de la gente (`data/usuarios.json`, `data/admins.json`,
`data/permisos.json`, `data/portal-sesiones.json`), la sesión de WhatsApp (`data/wa-auth`), el
propio agente (`_deploy/`) y cualquier cosa fuera de `C:\SeriOS-Connector`. El SAE vive en otra
máquina y este canal no tiene forma de alcanzarlo.
