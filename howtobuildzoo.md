# How to install ZOO on a nectar instance.

Install necessary utilities.
```bash
sudo yum install tar bzip2 gcc gcc-c++ autoconf bison flex
```

Install dependencies
```bash
sudo yum install gdal-devel libxml2-devel python-devel libcurl-devel \
                 openssl-devel
```

Download and extract ZOO.
```bash
curl http://www.zoo-project.org/dl/zoo-project-1.3.0.tar.bz2 \
     -o zoo-project-1.3.0.tar.bz2
tar -xjf zoo-project-1.3.0.tar.bz2
rm zoo-project-1.3.0.tar.bz2
```

Download and extract FastCGI
```bash
curl http://www.fastcgi.com/dist/fcgi.tar.gz -o fcgi.tar.gz
tar -xzf fcgi.tar.gz
cd $(ls fcgi*/ -d | head -n 1)
```

Fix FastCGI
```bash
cd include
vi fcgio.h
```

Insert `#include <stdio.h>` after `#include <iostream>`.
```c++
// FITNESS FOR A PARTICULAR PURPOSE.  If it breaks, you get to keep
// both halves.

#ifndef FCGIO_H
#define FCGIO_H

#include <iostream> // Line 33
#include <stdio.h>  // INSERT THIS LINE

#include "fcgiapp.h"

#ifndef DLLAPI
```

Save and close the file.

```bash
cd ..
```

Build and install FastCGI
```bash
autoconf
./configure
make
sudo make install
```

Add `/usr/local/lib` to the library path.

```bash
sudo su
echo "/usr/local/lib" > /etc/ld.so.conf.d/climate-analyser.conf
exit
sudo ldconfig
```

Navigate to the zoo folder.
```bash
cd ../zoo-project-1.3.0
```

Build the CGIC library
```bash
cd thirds/cgic*
sed -e "s:/usr/lib\(64\)\{0,1\}/libfcgi\.so:-lfcgi:g" Makefile > Makefile.tmp \
    && mv Makefile.tmp Makefile
make
make install
```

Build the ZOO Kernel
```bash
cd ../../zoo-project/zoo-kernel/
autoconf
./configure --with-python
make
```
