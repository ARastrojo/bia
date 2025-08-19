Para poder conectarnos al sistema de PC virtuales de la UAM desde un ordenador Mac debemos instalar algunas cosas adicionales.  

Como en Windows debemos instalar:  

- Cliente UDS (Se descarga desde https://pc-virtual.uam.es/uds/page/client-download)
- Microsoft remote desktop, ahora llamado Windows app (se instala desla la App store)

Aemás necesitamos instalar lo siguiente:

- [Brew](https://brew.sh/): un gestor de instalación de paquetes que necesitaremos para instalar el resto de componentes adicionales. 

Abrimos un terminal y pegamos el siguiente comando:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- [Xfreerdp](https://formulae.brew.sh/formula/freerdp): implementación del protocolo de escritorio remoto a través de X11. 

```bash
brew install freerdp
```
- [Xquartz](https://www.xquartz.org/): gestor de ventanas X11. 

```bash
brew install --cask xquartz 
# Can also be install by downloading and installation package from the web
````

Finalmente reiniciamos el equipo y ya podremos conectarnos a los PC virtuales de la UAM (si lo hacemos desde fuera del campus debemos estar conectados a la [VPN de la UAM](https://www.uam.es/uam/tecnologias-informacion/servicios-ti/acceso-remoto-red))