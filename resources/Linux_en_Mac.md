### Mac users

Mac OS X, el sistema operativo que llevan los ordenadores de Apple, está basado en Linux (concretamente deriva de un distribución denominada [FreeBSD](https://www.freebsd.org/)), por lo que esencialmente, Mac es Linux. La mayoría de los comandos y programas de Linux funcionan en Mac, aunque hay algunas excepciones como es habitual en los productos de Apple que complican un poco las cosas. 

Para poder usar adecuadamente el Linux que hay bajo Mac OS X, denominado Darwin, debemos instalar algunas cosas:

1. Desde la App Store instalaremos Xcode, una herramienta de Apple para la progamación (tardará bastante porque ocupa ~25Gb).  

2. Después intalaremos "Xcode Command Line Tools" escriendo lo siguiente en el _Terminal_:  

```bash
 xcode-select --install
# seguimos las indicaciones del proceso de instalación
```

3. Instalaremos [Brew](https://brew.sh/) que es un gestor de paquetes (similar a apt en ubuntu) que nos permitirá instalar varios programas en el futuro:  

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh"
```

4. Instalamos la última versión de [Bash](https://itnext.io/upgrading-bash-on-macos-7138bd1066ba): por defecto, los nuevos equipos de Mac, utilizas _zsh_ como _shell_. Si queremos trabajar en _bash_ como en la mayoría de la distribuciones de Linux podemos cambiar la _shell_ de nuestro Mac (_chsh -s /bin/bash_), pero la versión de _bash_ que traen los Mac está bastante anticuada (version 3.2.57 de 2007) y además, es una versión de Apple, con algunos cambios respecto al estándar. Para instalar la última versión de _bash_ vamos a utilizar _brew_:

```bash
brew install bash
```

Se instalará en _/usr/local/bin/bash_. Ahora debemos decirle al sistema que use ese _bash_ de aquí en adelante. Para ello tenemos primero que modificar el fichero con el listado de las posibles _shells_:

```bash
sudo nano /etc/shells
```

Añadimos al final _/usr/local/bin/bash_ y guardamos. 

Después, en el _terminal_ debemos ejecutar lo siguiente:  

```bash
sudo chsh -s /usr/local/bin/bash
```

Ya estamos listos para usar nuestro Mac como si fuera Linux.  