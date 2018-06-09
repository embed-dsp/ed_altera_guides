
# ModelSim

## Post Installation Setup

This guide here has been used on a **Fedora-28 64-bit** installation and tested with the following **ModelSim** versions:
* 16.0
* 16.1
* 17.1
* 18.0

**NOTE**: As of **ModelSim 18.0** it appears that it is not necessary to install `freetype-2.4.12` in **Step-3** anymore!
It seems to be working fine with the builtin `freetype-2.8.10` in **Fedora-28**.


ModelSim Starter Edition is a 32-bit application!
```bash
file /opt/altera/17.1/modelsim_ase/linux/vsim

linux/vsim: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.4, BuildID[sha1]=be0130f25d5768e095133f4ed1d9d69902768269, stripped
```


## Step-1
Make sure that ModelSim works with Linux Kernel 4.x

```bash
/opt/altera/17.1/modelsim_ase/bin/vsim 

Error: cannot find "/opt/altera/17.1/modelsim_ase/bin/../linux_rh60/vsim"
```

```bash
sudo vim /opt/altera/17.1/modelsim_ase/vco

# Around line 208 add this ...
4.[0-9]*)         vco="linux" ;;
```


## Step-2
Install missing 32-bit libraries.

```bash
/opt/altera/17.1/modelsim_ase/bin/vsim 

/opt/altera/17.1/modelsim_ase/bin/../linux/vish: error while loading shared libraries: libX11.so.6: cannot open shared object file: No such file or directory
```

```bash
# Install 32-bit core X11 protocol client library.
sudo dnf install libX11.i686
```

```bash
/opt/altera/17.1/modelsim_ase/bin/vsim 

/opt/altera/17.1/modelsim_ase/bin/../linux/vish/bin/../linux/vish: error while loading shared libraries: libXext.so.6: cannot open shared object file: No such file or directory
```

```bash
# Install 32-bit X.Org X11 libXext runtime library.
sudo dnf install libXext.i686
```

```bash
/opt/altera/17.1/modelsim_ase/bin/vsim 

/opt/altera/17.1/modelsim_ase/bin/../linux/vish/bin/../linux/vish: error while loading shared libraries: libXft.so.2: cannot open shared object file: No such file or directory
```

```bash
# Install 32-bit X.Org X11 libXft runtime library.
sudo dnf install libXft.i686
```

```bash
/opt/altera/17.1/modelsim_ase/bin/vsim 

/opt/altera/17.1/modelsim_ase/bin/../linux/vish/bin/../linux/vish: error while loading shared libraries: libncurses.so.5: cannot open shared object file: No such file or directory
```

```bash
# Install 32-bit ABI version 5 of the ncurses libraries for compatibility.
sudo dnf install ncurses-compat-libs.i686
```


## Step-3
Install 32-bit version of `freetype-2.4.12`

**NOTE**: As of **ModelSim 18.0** it appears that it is not necessary to install `freetype-2.4.12` anymore!
It seems to be working fine with the builtin `freetype-2.8.10` in **Fedora-28**.

```bash
/opt/altera/17.1/modelsim_ase/bin/vsim 

Error in startup script: 
Initialization problem, exiting.

Initialization problem, exiting.

    while executing
"InitializeINIFile quietly"
    invoked from within
"ncFyP12 -+"
    (file "/mtitcl/vsim/vsim" line 1)
** Fatal: Read failure in vlm process (0,0)
```

Install standard C library files for 32-bit development with `gcc`.
Otherwise the **Configure** and **Compile** steps will fail!

```bash
sudo dnf install glibc.i686
sudo dnf install glibc-devel.i686
```

Download source code for `freetype-2.4.12`
```bash
wget https://download.savannah.gnu.org/releases/freetype/freetype-2.4.12.tar.gz
```

Unpack source code.
```bash
tar zxf freetype-2.4.12.tar.gz
```

Configure.
```bash
cd freetype-2.4.12

./configure CFLAGS="-Wall -m32 -O2" --prefix=/opt/freetype/freetype-2.4.12 --exec-prefix=/opt/freetype/freetype-2.4.12/linux_x86
```

Compile using 4 cores.
```bash
make -j4
```

Install.
```bash
sudo make install
```


```bash
sudo vim /opt/altera/17.1/modelsim_ase/vco

# Around line 12 add this ...
export LD_LIBRARY_PATH="/opt/freetype/freetype-2.4.12/linux_x86/lib"
```


## Step-4
Everything should be working now!

```bash
/opt/altera/17.1/modelsim_ase/bin/vsim 
```
