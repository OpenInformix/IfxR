### [IfxR](https://openinformix.github.io/IfxR/)
Informix native R driver is a high performing data access interface suitable for highly scalable data science and machine learning operations. The core of the driver is written in C language for better efficiency and performance to makes it suitable for IoT and enterprise application while maintaining platform compatibility.  Are your IoT devices generating lot of data? Try applying ML algorithms to uncover hidden patterns and get new insights. Whether you are building an IoT solution or an Artificial Intelligence application, Informix can offer something for you and we are more than happy to help you on your journey to success!  
  
If you have any suggestion we would like to hear from you, The development activities of the driver are powered by passion, dedication and independent thinking. You may send pull request, together we grow as an open community; relevant discussion and queries are answered by community through stackoverflow. [http://stackoverflow.com/questions/tagged/informix](http://stackoverflow.com/questions/tagged/informix)  


#### Project Status : **Alpha**


### Driver installation
We will be working on creating R package for the driver on all major platform. For the time being you may use a prebuilt binary (IfxR.zip) of the driver if you are on Windows 64bit platform. All other platforms you may perform local build to create the driver. 


##### Windows: prebuilt driver binary
```bash

IfxR\prebuilt\Win64\IfxR.zip

# Unzip the IfxR.zip at library folder of your R installation.
# Eg:
# git clone https://github.com/OpenInformix/IfxR.git
# if your R installation is at C:\R\R-3.4.3 then
# Unzip IfxR\prebuilt\Win64\IfxR.zip at C:\R\R-3.4.3\library
C:\R\R-3.4.3\library
```



### Building the driver from its source

The two main activities involved in building an R package is building the R runtime library and building R manuals.  For building the native shared library from it C/C++ source code R uses GNU C/C++ compiler.  When you are building it on windows platform you may install Rtools, it contain all necessary utility to complete the build. In short Rtools provides a toolchain for Windows platform that work well with R. It mainly includes GNU make, GNU gcc (from MinGW), and other utilities commonly used on UNIX-ish platform.

#### Windows: Software requirements
* [Install R](https://cran.r-project.org/)
* [Rtools](https://cran.rstudio.com/bin/windows/Rtools/) (GNU C/C++ compiler is part of it. **FYI**: the advice is to install it at default location which is **C:\Rtools**, to avoid extra build setup)
* [LaTeX](https://miktex.org/download) (To create R manuals; a complete build of R including PDF manuals too)
* [RStudio](https://www.rstudio.com/) (Optional: it is convenient to use, but no need of it if you plan to use only command line build.)


#### Environment
```bash
# Make the version/dir change according to your installations. 
SET CSDK_HOME=c:/informix
SET PATH=C:\Dev\R\R-3.4.3\bin\x64;C:\Dev\MiKTeX2.9\miktex\bin\x64\;c:\Rtools\bin;c:\Rtools\mingw_64\bin;%PATH%
```


##### Firing build to creare shared library
R CMD SHLIB *.c
```bash
C:\IfxR\src>R CMD SHLIB *.c
# The above command that you typed will translate to the following commands.
c:/Rtools/mingw_64/bin/gcc  -I"C:/Dev/R/R-3.4.3/include" -DNDEBUG     -I"d:/Compiler/gcc-4.9.3/local330/include"     -O2 -Wall  -std=gnu99 -mtune=core2 -c IfxR.c -o IfxR.o

c:/Rtools/mingw_64/bin/gcc -shared -s -static-libgcc -o IfxR.dll tmp.def IfxR.o -Ld:/Compiler/gcc-4.9.3/local330/lib/x64 -Ld:/Compiler/gcc-4.9.3/local330/lib -LC:/Dev/R/R-3.4.1/bin/x64 -lR
```

##### Build & Install
```bash
R CMD build IfxR
#It will create something like IfxR_0.1.0.tar.gz file (or the equivalent for your package).
R CMD INSTALL  IfxR_0.1.0.tar.gz
```


#### Linux 
You will need a working environment of R (https://cran.r-project.org/bin/linux/) and the LaTeX tools for vignetters creation (e.g. sudo apt-get install texinfo texlive)

Clone the IfxR repo:
```bash
       informix@ifx1:/home/informix/OpenInformix/$ git clone https://github.com/OpenInformix/IfxR
```
Change to the IfxR directory:
```bash
       informix@ifx1:/home/informix/OpenInformix/$ cd IfxR
       informix@ifx1:/home/informix/OpenInformix/IfxR$
```       
Create/Copy a Makefile based on your OS:
```bash
       informix@ifx1:/home/informix/OpenInformix/IfxR$ cp src/Makevars.linux src/Makevars
```
Set CSDK_HOME and LD_LIBRARY_PATH environment variables pointing to your informix CSDK directory:
```bash
       informix@ifx1:/home/informix/OpenInformix/IfxR$ echo $INFORMIXDIR
       /home/informix/12.10.FC10
       informix@ifx1:/home/informix/OpenInformix/IfxR$ export CSDK_HOME=$INFORMIXDIR
       informix@ifx1:/home/informix/OpenInformix/IfxR$ export LD_LIBRARY_PATH=$INFORMIXDIR/lib:$INFORMIXDIR/lib/esql:$INFORMIXDIR/lib/cli
```
Install the driver in the global R library directory. This process will compile, build and install the library in one simple step: 
```bash
       informix@ifx1:/home/informix/OpenInformix/IfxR$ sudo env CSDK_HOME=$CSDK_HOME LD_LIBRARY_PATH=$LD_LIBRARY_PATH  R --verbose CMD INSTALL  .
       * installing to library `/usr/local/lib/R/site-library'
       * installing *source* package `IfxR' ...
       ** libs
       gcc -std=gnu99 -I/usr/share/R/include -DNDEBUG -fpic      -fpic  -g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -g  -c IfxR.c -o IfxR.o
       In file included from /usr/share/R/include/Rdefines.h:29:0,
                                  from IfxR.c:63:
       IfxR.c: In function `IfxRDriverConnect':
       /usr/share/R/include/Rinternals.h:1253:24: warning: initialization discards `const' qualifier from pointer target type [-Wdiscarded-qualifiers]
       #define translateChar  Rf_translateChar
                                                ^
       IfxR.c:294:28: note: in expansion of macro `translateChar'
                     char *UserConStr = translateChar(STRING_ELT(connection, 0));
                                                       ^
       gcc -std=gnu99 -shared -L/usr/lib/R/lib -Wl,-Bsymbolic-functions -Wl,-z,relro -o IfxR.so IfxR.o -L/home/informix/12.10.FC10/lib/cli -lthcli -L/usr/lib/R/lib -lR
       installing to /usr/local/lib/R/site-library/IfxR/libs
       ** R
       ** inst
       ** preparing package for lazy loading
       ** help
       *** installing help indices
       ** building package indices
       ** installing vignettes
       ** testing if installed package can be loaded
       * DONE (IfxR)
       informix@ifx1:/home/informix/OpenInformix/IfxR$ 
```
If there was no errors during the install process, the IfxR library will be copied to:
```bash
       informix@ifx1:/home/informix/OpenInformix/IfxR$ ls -la /usr/local/lib/R/site-library/IfxR/libs
       total 100
       drwxrwxr-x 2 root staff  4096 Mar 13 16:48 .
       drwxrwxr-x 7 root staff  4096 Mar 13 16:48 ..
       -rwxrwxr-x 1 root staff 91312 Mar 13 16:48 IfxR.so
       informix@ifx1:/home/informix/OpenInformix/IfxR$ 
```
Check that it works using the Sample1.R R script (remember to adjust the connection string for your Informix server)
```bash       
       informix@ifx1:/home/informix/OpenInformix/IfxR$ grep irk1210 Sample1.R 
       ch <- IfxConnect( "SERVER=irk1210;DATABASE=stores7;HOST=127.0.0.1;SERVICE=9088;UID=informix;PWD=mypasswd;" )

       informix@ifx1:/home/informix/OpenInformix/IfxR$ 

       informix@ifx1:/home/informix/OpenInformix/IfxR$ Rscript Sample1.R 
       [1] " DROP TABLE returns [1] "
       [1] "create table t1 ( c1 int, c2 char(20), c3 int, c4 int ) "
       [1] "insert into t1 values( 1, 'Sunday', 101, 201 );"
       [1] "insert into t1 values( 2, 'Monday', 102, 202 );"
       [1] "insert into t1 values( 3, 'Tuesday', 103, 203 );"
       [1] "insert into t1 values( 4, 'Wednesday', 104, 204 );"
       [1] "insert into t1 values( 5, 'Thursday', 105, 2005 );"
       [1] "insert into t1 values( 6, 'Friday', 106, 206 );"
       [1] "insert into t1 values( 7, 'Saturday', 107, 207 );"
       [1] "****** The Select Output is ********"
       $data
       $data[[1]]
       [1] 1 2 3 4 5 6 7

       $data[[2]]
       [1] "Sunday              " "Monday              " "Tuesday             "
       [4] "Wednesday           " "Thursday            " "Friday              "
       [7] "Saturday            "

       $data[[3]]
       [1] 101 102 103 104 105 106 107

       $data[[4]]
       [1]  201  202  203  204 2005  206  207


       $stat
       [1] 1

       informix@ifx1:/home/informix/OpenInformix/IfxR$ 
```
#### Sample
```R
# Rscript  Sample1.R 
library(IfxR)

ch <- IfxConnect( "SERVER=ids0;DATABASE=db1;HOST=127.0.0.1;SERVICE=9088;UID=informix;PWD=xxxx;" )

sql <- c( "create table t1 ( c1 int, c2 char(20), c3 int, c4 int ) ", 
"insert into t1 values( 1, 'Sunday', 101, 201 );", 
"insert into t1 values( 2, 'Monday', 102, 202 );", 
"insert into t1 values( 3, 'Tuesday', 103, 203 );", 
"insert into t1 values( 4, 'Wednesday', 104, 204 );", 
"insert into t1 values( 5, 'Thursday', 105, 2005 );", 
"insert into t1 values( 6, 'Friday', 106, 206 );", 
"insert into t1 values( 7, 'Saturday', 107, 207 );" )

rc <- IfxQuery( ch, "drop table t1;" )
x <- sprintf( " DROP TABLE returns [%d] ", rc)  
print( x )


for(s in sql)
{
  rc <- IfxQuery(ch, s) 
  print( s )
}

rc <- IfxQuery(ch, "select * from t1") 
res <- IfxFetchRows(ch)

print ( "****** The Select Output is ********" )
print (res)

IfxClose(ch)

```

#### Output
```
[1] " DROP TABLE returns [1] "
[1] "create table t1 ( c1 int, c2 char(20), c3 int, c4 int ) "
[1] "insert into t1 values( 1, 'Sunday', 101, 201 );"
[1] "insert into t1 values( 2, 'Monday', 102, 202 );"
[1] "insert into t1 values( 3, 'Tuesday', 103, 203 );"
[1] "insert into t1 values( 4, 'Wednesday', 104, 204 );"
[1] "insert into t1 values( 5, 'Thursday', 105, 2005 );"
[1] "insert into t1 values( 6, 'Friday', 106, 206 );"
[1] "insert into t1 values( 7, 'Saturday', 107, 207 );"
[1] "****** The Select Output is ********"
$data
$data[[1]]
[1] 1 2 3 4 5 6 7

$data[[2]]
[1] "Sunday              " "Monday              " "Tuesday             "
[4] "Wednesday           " "Thursday            " "Friday              "
[7] "Saturday            "

$data[[3]]
[1] 101 102 103 104 105 106 107

$data[[4]]
[1]  201  202  203  204 2005  206  207


$stat
[1] 1

```
