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

Patch FastCGI
```bash
patch -p0 <<EOF
--- include/fcgio.h     2002-02-25 13:16:11.000000000 +0000
+++ include/fcgio.h     2014-07-14 01:40:07.914260118 +0000
@@ -31,6 +31,7 @@
 #define FCGIO_H
 
 #include <iostream>
+#include <stdio.h>
 
 #include "fcgiapp.h"
 
EOF
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
