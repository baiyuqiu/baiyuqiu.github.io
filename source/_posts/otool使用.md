---
title: otool使用
tags: 工具
categories: iOS
date: 2022-05-16 22:44:15
---


### otool简介
otool(object file displaying tool)
它是Xcode自带的工具，用于提取并显示Mach-O文件的相关信息，可以查看Mach-O文件Header、Load Command、Segment等信息.

<!--more-->

### otool命令
```
-f print the fat headers
-a print the archive header
-h print the mach header
-l print the load commands
-L print shared libraries used
-D print shared library id name
-t print the text section (disassemble with -v)
-x print all text sections (disassemble with -v)
-p <routine name>  start dissassemble from routine name
-s <segname> <sectname> print contents of section
-d print the data section
-o print the Objective-C segment
-r print the relocation entries
-S print the table of contents of a library (obsolete)
-T print the table of contents of a dynamic shared library (obsolete)
-M print the module table of a dynamic shared library (obsolete)
-R print the reference table of a dynamic shared library (obsolete)
-I print the indirect symbol table
-H print the two-level hints table (obsolete)
-G print the data in code table
-v print verbosely (symbolically) when possible
-V print disassembled operands symbolically
-c print argument strings of a core file
-X print no leading addresses or headers
-m don't use archive(member) syntax
-B force Thumb disassembly (ARM objects only)
-q use llvm's disassembler (the default)
-Q use otool(1)'s disassembler
-mcpu=arg use `arg' as the cpu for disassembly
-j print opcode bytes
-P print the info plist section as strings
-C print linker optimization hints 
```

#### 查看Mach-O头结构
```
➜  Mach-O otool -h WeChat
WeChat:
Mach header
      magic  cputype cpusubtype  caps    filetype ncmds sizeofcmds      flags
 0xfeedfacf 16777228          0  0x00           2   136      13576 0x04a10085
```

#### 查看是否加密
```
➜  Mach-O otool -l WeChat | grep -B 2 crypt
          cmd LC_ENCRYPTION_INFO_64
      cmdsize 24
     cryptoff 16384
    cryptsize 215269376
      cryptid 1
```
其中`cryptid`代表是否加壳（ 1: 加壳， 0: 已脱壳 ）

#### 查看Segments和Sections
```
➜  Mach-O size -m WeChat
Segment __PAGEZERO: 4294967296 (zero fill)
Segment __TEXT: 100253696
	Section __text: 84453412
	Section __stubs: 26652
	Section __stub_helper: 25788
	Section __const: 2231432
	Section __cstring: 6969905
	Section __objc_methname: 3129133
	Section __objc_classname: 379072
	Section __objc_methtype: 969217
	Section __ustring: 73664
	Section __gcc_except_tab: 1482156
	Section __unwind_info: 298784
	Section __eh_frame: 194484
	total 100233699
Segment __DATA: 90275840
	Section __got: 17312
	Section __la_symbol_ptr: 17768
	Section __mod_init_func: 8136
	Section __mod_term_func: 8
	Section __const: 1551840
	Section __cfstring: 2497056
	Section __objc_classlist: 100960
	Section __objc_nlclslist: 88
	Section __objc_catlist: 2080
	Section __objc_nlcatlist: 176
	Section __objc_protolist: 20152
	Section __objc_imageinfo: 8
	Section __objc_const: 10612712
	Section __objc_selrefs: 825512
	Section __objc_protorefs: 3984
	Section __objc_classrefs: 86984
	Section __objc_superrefs: 53528
	Section __objc_ivar: 232732
	Section __objc_data: 1009600
	Section __data: 868968
	Section __bss: 2196228 (zerofill)
	Section __common: 70166996 (zerofill)
	total 90272828
Segment __LINKEDIT: 5603328
total 4491100160
```

#### 查看依赖库
```
otool -L WeChat
```

#### 查看汇编
```
otool -v -t WeChat
```

##### 查看所有类
```
otool -v -s __TEXT __objc_classname WeChat
```

##### 查看所有方法
```
otool -v -s __TEXT __objc_methname WeChat
```

##### 查看所有定义
```
otool -v -s __TEXT __objc_methtype WeChat
```

##### 查看字符串
```
otool -v -s __TEXT __cstring WeChat
```

##### 查看所有类地址
```
otool -v -s __DATA __objc_classlist WeChat
```

##### 查看所有被引用类地址
```
otool -v -s __DATA __objc_classrefs WeChat
```

##### 查看所有被引用父类地址
```
otool -v -s __DATA __objc_superrefs WeChat
```

##### 查看所有协议地址
```
otool -v -s __DATA __objc_protolist WeChat
```

##### 查看所有被引用协议地址
```
otool -v -s __DATA __objc_protorefs WeChat
```

##### 查看所有属性地址
```
otool -v -s __DATA __objc_ivar WeChat
```
