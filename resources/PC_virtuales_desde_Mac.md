


To be able to run virtual machines in a Mac host you will need to install some additionally programs. 

You will need, as Windows users:

- UDS client (Download from  https://laboratoriovirtual.eps.uam.es)
- Microsoft remote desktop (app store)

Additionally, we will need to install:

- Install [Brew](https://brew.sh/)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

- [Xfreerdp](https://formulae.brew.sh/formula/freerdp) from brew:
```bash
brew install freerdp
```
- [Xquartz](https://www.xquartz.org/)
```bash
brew install --cask xquartz 
# Can also be install by downloading and installation package from the web
````
Finally, we must restart our mac. 