# openpgm
from steve-o/openpgm, modify to adapt vcpkg zeromq
因为现在 zeromq 的vcpkg ports并没有增加openpgm的特性(虽然CMakeLists.txt内部已经大体支持)，所以这里fork了一个openpgm版本做修改.

## 大概的操作步骤描述:
* 1. vcpkg主目录下 ports目录 增加openpgm-xl目录，配置openpgm-xl的vcpkg
* 2. vcpkg install openpgm-xl
	openpgm-xl 在 https://github.com/shadowxiali/openpgm/archive/v5.2.122_vcpkg1.zip
* 3. 如果你的zeromq的vcpkg源码没有下载解压，那么需要先运行 vcpkg install zeromq 进行源码的解压，才能进行后续部分
* 4. 如果你的zeromq的版本是 2019-09-20, vcpkg版本 2020.01.17-nohash，那么直接替换ports/zeromq, zeromq_mkfile/CMakeLists.txt
   目标CMakeLists.txt 位置 vcpkg/buildtrees/zeromq/src/903c0feb7c-34456404d0/CMakeLists.txt
   如果位置与我的一样，那么直接替换
* 5. 如果不一样，请根据原理，进行合适的修改:

	修改 ports/zeromq/CONTROL
		增加
> Feature: openpgm-xl
> 
> Description: using openpgm-xl for the vcpkg version of openpgm

	修改 ports/zeromq/portfile.cmake
		在 vcpkg_check_features( FEATURES )中增加 openpgm-xl WITH_OPENPGM 项
	
	修改zeromq的CMakeLists.txt
		在 WITH_OPENPGM 项 原来的find_path中，修改替换(参考zeromq_mkfile/CMakeLists.txt中内容修改)
``` cmake
	# find vcpkg generated openpgm-xl begin
	if (${OPENPGM_ROOT} STREQUAL "OPENPGM_ROOT-NOTFOUND")
		message(STATUS "OPENPGM_ROOT try to find_path")
		find_path(
			OPENPGM_ROOT
			NAMES include/pgm/pgm.h
		)
		message(STATUS "OpenPGM find detected - ${OPENPGM_ROOT}")
		set(OPENPGM_INCLUDE_DIRS ${OPENPGM_ROOT}/include)
		set(OPENPGM_LIBRARY_DIRS 
		  optimized ${OPENPGM_ROOT}/lib
		  debug ${OPENPGM_ROOT}/debug/lib)
	else()
		set(OPENPGM_INCLUDE_DIRS ${OPENPGM_ROOT}/include)
		set(OPENPGM_LIBRARY_DIRS ${OPENPGM_ROOT}/lib)
	endif()
	# find vcpkg generated openpgm-xl end
```
* 6. 修改完成后，执行 vcpkg install zeromq[openpgm-xl]，如果出错，在看错误日志分析吧。
