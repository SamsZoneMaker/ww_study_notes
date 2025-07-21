





## 随记

- ```c
  static volatile BFLASH_STUS_T *s_pBftBootStus = NULL;
  ```

  `static` - 静态存储类

  - **作用域限制**：只能在当前源文件(.c文件)内访问，其他文件不可见
  - **生命周期**：程序运行期间一直存在，不会因函数结束而销毁
  - **内存位置**：存储在静态存储区，而非栈区
  - **初始化**：只在程序启动时初始化一次

  `volatile` - 易变关键字

  - **防止编译器优化**：告诉编译器这个变量可能被外部因素修改
  - **强制内存访问**：每次读取都必须从内存中获取最新值，不能使用寄存器缓存
  - 典型应用场景
    - 硬件寄存器映射
    - 中断服务程序中的共享变量
    - 多线程环境下的共享数据

  `*s_pBftBootStus` - 指针变量

  - `*` 表示这是一个指针变量
  - `s_p` 前缀通常表示 static pointer（静态指针）

  `NULL` - 初始化

  整体定义：这行代码声明了一个：**静态的**（仅本文件可见）、**易变的**（防止编译器优化）、、**指向BFLASH_STUS_T类型数据的指针** **初始值为NULL**。

  典型使用场景：

  ```c
  // 可能的使用示例
  typedef struct {
      uint32_t flash_status;
      uint32_t boot_progress;
      uint32_t error_code;
  } BFLASH_STUS_T;
  
  // 后续代码中可能会这样使用：
  s_pBftBootStus = (BFLASH_STUS_T*)0x20000000; // 指向特定内存地址
  if (s_pBftBootStus->flash_status == READY) {
      // 处理闪存就绪状态
  }
  ```

  

- ```c
  void bft_raw_show(char *vpPrefix, U8 *vp, U32 vLen);
  ```

  这是一个函数声明（函数原型）

  `void` - 返回类型

  - 表示这个函数不返回任何值
  - 调用后不会得到返回结果

  `bft_raw_show` - 函数名

  `(char *vpPrefix, U8 *vp, U32 vLen);` - 参数列表

  - `char *vpPrefix` - 参数1
    - `char *`：指向字符串的指针
    - `vpPrefix`：前缀字符串
    - **作用**：在显示数据时作为前缀标识
  - `U8 *vp` - 参数2
    - `U8`：无符号8位整数类型（通常定义为 `typedef unsigned char U8`）
    - `U8 *`：指向字节数组的指针
    - **作用**：指向要显示的原始数据缓冲区
  - `U32 vLen` - 参数3
    - `U32`：无符号32位整数类型（通常定义为 `typedef unsigned int U32`）
    - **作用**：指定要显示的数据长度（字节数）

  整个函数的作用：显示内存内容、记录原始数据。
  

- ```c
  void memset(void *str, int c, size_t n)
  ```

  用于将一段内存区域设置为指定的值

  在清空内存区域或者为内存区域赋值时，memset() 是一个常用的工具函数。

  C语言标准库中封装了该函数，dragon项目中简化了该函数
  

- ```c
  STATIC_F WW_RTN bft_case_wrong_b10_magic0(void)
  {
      BOOT_INFO_T *pBinfo = NULL;
      pBinfo = (BOOT_INFO_T *)(BFT_ADDR_SHM_FLASH_BASE + 0x4000);
      pBinfo->magic0++;
      return WW_OK;
  }
  ```

  - 声明一个指向`BOOT_INFO_T`类型的指针变量，初始设为NULL
  - `BOOT_INFO_T`是一个结构体类型，描述Boot信息（如magic、size、校验等）。
  - `pBinfo`指向一个特定的内存地址
  - `BFT_ADDR_SHM_FLASH_BASE`是某个共享内存或flash的基地址 （比如0x20000000），加上`0x4000`表示偏移后（16KB），指向那里的数据块
  - 用`(BOOT_INFO_T *)`强制把这个地址当成`BOOT_INFO_T`结构体来看待
  - `pBinfo->magic0++;` 将上述结构体中的`magic0`成员“加1”，目的通常是**人为制造一个“magic错误”** （制造数据异常），比如原本magic值是0xAABBCCDD，+1会让它变成0xAABBCCDE，从而在后续校验流程中被检测为错误，进而触发错误分支或保护机制。

  

  这个函数的作用：
  **把flash某处的boot信息区中的magic0值做自增，故意制造错的magic值，后续测试流程能检测这种异常并进入错误分支。**

