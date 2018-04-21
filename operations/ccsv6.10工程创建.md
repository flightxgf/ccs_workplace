# ccsv6.10工程创建使用 #


## 第一步本地创建项目管理目录： xgf_project ##
* copy svn中的 tm.em.comm_lib_M4
* copy svn中的 tm.em.DemoAPP
* copy svn中的 tm.em.tmOS_M4 
> 结果是xgf_project目录下含上述三个包

## 第二步进入tm.em.DemoAPP\project目录 ##
* 保留目录 app
* 保留目录 comm_lib
* 保留目录 tmOS
* 保留目录 Debug
* 保留目录 targetConfigs
> 其余的全部删除

## 第三步进入xgf_project\tm.em.DemoAPP\project\Debug ##
* 保留目录 app
* 保留目录 comm_lib
* 保留目录 tmOS
> 其余的全部删除

## 第四步进入xgf_project\tm.em.DemoAPP\project\targetConfigs ##
* 这个目录中的内容表示工程项目使用的是哪个芯片，后续烧录程序用于查看是否和连接器正确连通
* 比如：Tiva TM4C1294KCPDT.ccxml
> 先清理这个文件，建立工程时，在选定connect环节xds100v3会自动创建这个文件到此目录

## 第五步删除和新增 ##
* 查看xgf_project\tm.em.comm_lib_M4目录是否需要新增库和删除库，如果有直接拷贝库到此目录
* 查看xgf_project\tm.em.DemoAPP只关心app目录
1. 查看include目录 是否需要新增和删除
2. 查看src目录是否需要新增和删除
* 查看xgf_project\tm.em.tmOS_M4\M4_lib目录是否需要新增库和删除库，如果有直接拷贝库到此目录
1.查看xgf_project\tm.em.tmOS_M4\目录关心driver目录
2. 查看include目录 是否需要新增和删除
3. 查看src目录是否需要新增和删除
> 5步进行完工程需要的文件已准备完毕

## 注意： 在创建工程环节：##
1. project->new ccs project
2. target: 选择需要的芯片系列、类型
3. Connection:选择下载器类型--xds100vs
4. Project name
5. 去掉use default loction ，点击Browse选择自己建立的本地工程文件
> 注意路径： xgf_project\tm.em.DemoAPP\project
6. Empty project
7. Finish
> 这里工程建立完毕


## 将准备好的项目：xgf_project 需要的文件 拖到建立的ccs工程中 ##
1. 到xgf_project\tm.em.DemoAPP\app\src目录下，选中需要用到的文件拖到app位置，选择Link to files
2. 到xgf_project\tm.em.comm_lib_M4\canopen\src目录下，选中需要用到的文件拖到comm_lib下canopen位置，选择Link to files
3. 到xgf_project\tm.em.comm_lib_M4\lwip-1.4.1\tm目录下，选中(lwiplib_1.4.1.c)需要用到的文件拖到comm_lib下lwip-1.4.1位置，选择Link to files
4. 到xgf_project\tm.em.comm_lib_M4\usb\usb_bulk目录下，选中(usb_bulk_structs.c)需要用到的文件拖到comm_lib下usb位置，选择Link to files
> 如果还有其他的库操作方法类似
5. 到xgf_project\tm.em.tmOS_M4 目录下，操作方法同上一致
>  注意： 
1. \xgf_project\tm.em.tmOS_M4\driver\src目录下只需选择此次项目需要驱动不需要全部选中 链接
2. xgf_project\tm.em.tmOS_M4\kernel\src 全部选中，链接到工程相应位置
3. xgf_project\tm.em.tmOS_M4\M4_lib下可能有许多需要的sdk 和库，选择需要的即可，比如：
* xgf_project\tm.em.tmOS_M4\M4_lib\driverlib--->driverlib.lib
* xgf_project\tm.em.tmOS_M4\M4_lib\usblib----->usblib.lib
> 需要的工程文件已经拖完毕


## 给工程天剑头文件 ##

* 右键项目 Properties-->Build--->ARM Compiler--->include
* 右键项目 Properties-->Build--->ARM linker--->Basice options（set c systesm）:设置需要的栈：8042
* 右键项目 Properties-->Build--->ARM Compiler--->Advanced options-->Predefined Symbos这里是天剑工程中需要的宏（宏的添加出处），
> 注意： 因为lwip 以及 usb 驱动需要使用  MOP开头字样驱动api 需要添加宏---TARGET_IS_TM4C129_RA0

```
"${PROJECT_ROOT}/../app/include"
"${PROJECT_ROOT}/../../tm.em.tmOS_M4"
"${PROJECT_ROOT}/../../tm.em.comm_lib_M4"
```

## 修改启动文件 ##

* xxxxxx_startup_ccs.c

```
// To be added by user
void        SysTick_ISR(void);
void        LwipEthernet_ISR(void);
void 		USB0OTGModeIntHandler(void);

SysTick_ISR,                         // The SysTick handler
LwipEthernet_ISR,                    // Ethernet
USB0OTGModeIntHandler,               // USB0

```
## 编译 ##

> xgf_project\tm.em.DemoAPP\project\Debug --->xx.out
> xxx.bin的生成方法：xgf_project\tm.em.DemoAPP\project\Debug---->xxx.bin
* 右键项目 Properties-->Build-->Steps-->Post-build steps 添加如下：

```
"${CCS_INSTALL_ROOT}/utils/tiobj2bin/tiobj2bin" "${BuildArtifactFileName}" "${BuildArtifactFileBaseName}.bin" "${CG_TOOL_ROOT}/bin/armofd" "${CG_TOOL_ROOT}/bin/armhex" "${CCS_INSTALL_ROOT}/utils/tiobj2bin/mkhex4bin"

```
