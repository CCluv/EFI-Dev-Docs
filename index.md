 [How to Build [UDK2018] 原文链接](https://github.com/tianocore/tianocore.github.io/wiki/UDK2018-How-to-Build)

以下翻译windows平台编译方法并截取windows部分的原文

---
##                          如何构建UDK2018 (WINDOWS 系统)

以下步骤已在Windows 10企业版测试通过*:

1.  设置构建环境
    1)  安装微软的Visual Studio 2015*,确保安装时勾选了64位编译器.
    2)  从http://www.nasm.us/下载NASM 2.12.01或更新版本并安装到
        C:\Nasm. 确保 C:\Nasm 被添加到系统环境变量PATH中.
    3)  Set up for using GitHub for Windows. See:
    https://github.com/tianocore/tianocore.github.io/wiki/Getting-Started-with-EDK-II#github-help
    4)  为了构建basetools需下载并安装Python2.7.x(https://www.python.org/)到默认目录C:/Python27 
    5)  从 https://wiki.openssl.org/index.php/Binaries 下载最新OpenSSL预编译好的二进制可直行文件. 解压到某目录并将其目录添加到系统环境变量PATH中.

2.  为UDK2018创建全量源代码目录
    1)  创建工作目录,例如C:\MyWorkspace
    2)  从[UDK2018 Release Page](https://github.com/tianocore/edk2/releases/tag/vUDK2018)下载官方发布UDK2018 .zip 文件
        1) 下载 - UDK2018 edk-vUDK2018 工作目录 [Source code (zip file)](https://github.com/tianocore/edk2/archive/vUDK2018.zip) 
        2) 将 [edk2-vUDK2018] 中的文件解压到工作目录C:\MyWorkspace.
    3) **或者**  用以下git命令从GitHub上检出(Checkout) vUDK2018 标签(Tag)<br>
        1)  运行  `git clone  https://github.com/tianocore/edk2.git vUDK2018`
        2)  进入 vUDK2018 目录, 在此处运行: `git checkout tags/vUDK2018 -b vUDK2018`
        3)  将vUDK2018中的所有文件和目录移至C:\MyWorkspace

3.  生成 OpenSSL* 加解密库
    1)  打开文件 "C:\MyWorkspace\CryptoPkg\Library\OpensslLib\OpenSSL-HOWTO.txt",并依照其     中的OpenSSL安装说明进行操作.
        对于本次发布,请使用OpenSSL-1.1.0g. 从 
        https://github.com/openssl/openssl/archive/OpenSSL_1_1_0g.zip
        下载并解压到C:\MyWorkspace\CryptoPkg\Library\OpensslLib,且将其目录改名为openssl

4.  编译基础工具(BaseTools): 
     https://github.com/tianocore/tianocore.github.io/wiki/Windows-systems#compile-tools
    1)  打开微软Visual Studio 命令提示符并输入`cd C:\MyWorkspace`以进入工作目录
    2)  用以下命令编译基础工具(BaseTools)中的C 代码工具  
<pre>
	set PYTHON_HOME=C:\Python27
        edksetup.bat Rebuild
</pre>

5.  构建步骤

     **(例)NT32**
    1)  打开微软Visual Studio 命令提示符并输入`cd C:\MyWorkspace`以进入工作目录
    2)  用edksetup.bat命令来初始化环境
       `edksetup --nt32`
    3)  输入以下命令来为Nt32平台构建
        `build -t VS2015x86`
    4)  若构建完全成功则会在C:\MyWorkspace\Build\NT32IA32\DEBUG_VS2015x86\IA32 directory中出现UEFI应用程序"`HelloWorld.efi`"  

[译注]

1. 安装微软的Visual Studio 2015或以上(任意版本,没有正版制授权的话推荐[VS 2017 community版](https://visualstudio.microsoft.com/zh-hans/vs/community/)),UEFI开发其实只用到了其中的C++工具链并且可以是任意版本,可以用Cygwin+gcc代替,安装时只在工作负载选择“使用C++的桌面开发”即可。
2. 在开始菜单中找到“适用于 VS 2017 的 x64 本机工具命令提示”，若工作目录不在C盘需要用D:这样的命令换分区号(dos遗留问题),不然仅仅是用cd命令无效。
3. 若是遇到错误:
    1. edk2_vUDK2018\BaseTools\Source\C\makefiles\ms.common 文件里找到/WX并删除(cl.exe的/WX选项是将warining视为错误)
    2. edk2_vUDK2018\BaseTools\Source\C\VfrCompile\Makefile 文件里找到/WX并删除
    3. edk2_vUDK2018\BaseTools\Source\C\Include\IndustryStandard\PeImage.h 文件中(665行)改为
        ```C
        #ifndef _WINNT_
        typedef struct {
        UINT32  FunctionStartAddress;
        UINT32  FunctionEndAddress;
        UINT32  UnwindInfoAddress;
        } RUNTIME_FUNCTION;
        #endif
        ```
4. 如果仅仅是学习UEFI而不是大批编译的话,UDK里的python工具可以不用编译,默认直接用相应的.py文件干活.要是想编译的话,需要`pip.exe install cx_Freeze`然后`set PYTHON_FREEZER_PATH=C:\Python27\Scripts`(如果你是这个目录的话),重新运行一遍rebuild在`BaseTools\Bin\Win32`目录下多出一个`lib`目录和一堆`exe`就是完成了.预编译可能会稍稍提高以后UEFI程序的编译速度. (Makefile中提到Using cx_Freeze 4.2.3 with Python 2.7.2,反正我这里用最新版本编译了后就不能用了)
---
原文


##                          HOW TO BUILD (WINDOWS SYSTEM)

The steps below are verified on Microsoft Windows 10 Enterprise*:

1.  Setup Build Environment
    1)  Install Microsoft Visual Studio 2015* in the build machine and make
        sure that AMD64 complier was selected when installing.
    2)  Download NASM 2.12.01 or later from http://www.nasm.us/ and install it to
        C:\Nasm. Make sure C:\Nasm is added to system environment variable 'PATH'
    3)  Set up for using GitHub for Windows. See:
    https://github.com/tianocore/tianocore.github.io/wiki/Getting-Started-with-EDK-II#github-help
    4)  Download and install Python2.7.x  https://www.python.org/ for building the BaseTools
        Default install directory is: C:/Python27
    5)  Download the pre-compiled Openssl binary from https://wiki.openssl.org/index.php/Binaries.  Search for a
        Windows binary in the list of "_Third Party OpenSSL Related Binary Distributions_" table that will be 
        Windows binary. Go to the third party site to download the latest version of Windows x64 86 or Win32 binary.
        Download and extract to C:\Openssl. Make sure C:\Openssl is added to system environment 
        variable 'PATH'. 

2.  Create the full Source Code directory for the UDK2018 release
    1)  Create a working space directory in the build machine, for example, C:\MyWorkspace
    2)  Download the official UDK2018 release .zip file from the [UDK2018 Release Page](https://github.com/tianocore/edk2/releases/tag/vUDK2018)
        1) Download - UDK2018 edk-vUDK2018 Workspace [Source code (zip file)](https://github.com/tianocore/edk2/archive/vUDK2018.zip) 
        2) Extract files in [edk2-vUDK2018] to the working space directory C:\MyWorkspace.
    3) **OR**  Checkout the vUDK2018 Tag from GitHub with the following "git" command <br>
        1)  run  `git clone  https://github.com/tianocore/edk2.git vUDK2018`
        2)  Go to the vUDK2018 directory, and from there run: `git checkout tags/vUDK2018 -b vUDK2018`
        3)  Move all files and folders under "vUDK2018" to "C:\MyWorkspace"

3.  Generate OpenSSL* Crypto Library
    1)  Open file "C:\MyWorkspace\CryptoPkg\Library\OpensslLib\OpenSSL-HOWTO.txt"
        and follow the instruction to install OpenSSL* for UEFI building.
        For this release, please use OpenSSL-1.1.0g. Download it from 
        https://github.com/openssl/openssl/archive/OpenSSL_1_1_0g.zip
        Extract it to C:\MyWorkspace\CryptoPkg\Library\OpensslLib,
        and rename its directory name to openssl

4.  Compile the BaseTools See: 
     https://github.com/tianocore/tianocore.github.io/wiki/Windows-systems#compile-tools
    1)  Open a Microsoft Visual Studio* command prompt, type `cd C:\MyWorkspace`
        to enter the  workspace directory
    2)  Compile the BaseTools C source tools  
<pre>
	set PYTHON_HOME=C:\Python27
        edksetup.bat Rebuild
</pre>

5.  Build Steps

    **NT32**

    1)  Open a Microsoft Visual Studio* command prompt, type `cd C:\MyWorkspace`
        to enter the workspace directory
    2)  Use edksetup.bat command to initialize the working environment.
       `edksetup --nt32`
    3)  Type following command to build Nt32 platform
        `build -t VS2015x86`
    4)  Upon the build completing successfully there should be the UEFI Application "`HelloWorld.efi`"  in the C:\MyWorkspace\Build\NT32IA32\DEBUG_VS2015x86\IA32 directory

