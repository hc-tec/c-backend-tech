@[TOC](CLion 编译运行 redis)
## 环境
* Ubuntu 18.04.5
* CLion 2020.3.2
* Redis 6.0.14
## 前言
发现很多利用 clion 编译调试 redis 的博文提到 clion 不支持 makefile 编译 redis，转而写了个 CMakeLists 文件编译，其实这 duck 不必。
clion 本身支持 makefile 编译 (见下图)，因此咱们没必要花太多心思在源码编译问题上，更没必要重新去写个 CMakeLists 

![clion支持makefile](https://img-blog.csdnimg.cn/20210720114501909.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)
## 编译
当然，咱们还是得编译 redis 项目，利用 clion 对 makefile 的支持就彳亍。
用 clion 打开 redis 项目，默认情况下对 **all** 编译。

![默认为 all ](https://img-blog.csdnimg.cn/20210720115129449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)
在终端用过 redis 的小伙伴应该知道，咱们启用 redis 的两条命令。
```c
// 启动 redis
redis-server

// 操作 redis
redis-cli
```
因此，咱们至少需要把 redis-server 和 redis-cli 给编译成可执行文件，才可以进一步使用并调试 redis。
### redis-server 编译
首先将编译配置换成 redis-server

![配置换成 redis-server](https://img-blog.csdnimg.cn/2021072011590987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

再点击运行按钮

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021072011595471.png)

打开后，发现有错误提示

![错误提示](https://img-blog.csdnimg.cn/2021072012005140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

这儿咱们可以暂时不用管它，点击 Run 按钮，弹出个提示框，直接 Continue Anyway 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720120442722.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

点击之后，左下角出现编译信息

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021072012062640.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

如果是这种信息，就表明编译成功了。
在 src/ 文件夹下面就能找到 redis-server 可执行文件。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720120749714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)
### redis-cli 
和 redis-server 用上同样的方法也能把 redis-cli 给编译出来，同样在 src/ 文件夹下面。

### redis 运行与调试
应该没忘记在编译配置那儿出现的错误信息叭。其实那个选项是要跟编译成功之后得到的可执行文件关联起来，现在咱们把 redis-server 和 redis-cli 可执行文件给生成了，也就可以将相应的编译配置与相应的可执行文件给关联起来，这步是必需的，关系到 redis 的成功运行与否。
#### 将 redis-cli 编译配置与 redis-cli 可执行文件关联起来。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720121309409.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)
#### redis 运行
```c
// 进入到 src/ 文件夹下
cd src/
// 运行 redis-server
./redis-server
// 运行 redis-cli
./redis-cli
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720124330544.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

运行成功！
上面只是在终端运行，下面咱们需要能在 clion 上运行，方便之后的调试工作。

redis 的操作主要由 redis-cli 完成，因此咱们需要找到相应的 .c 文件，也就是 src/ 文件夹下的  redis-cli.c 。
找到 main 函数

![main函数](https://img-blog.csdnimg.cn/2021072013012445.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

由于咱们前面已经将编译配置与可执行文件关联起来，运行与调试可以正常执行。
点击 运行 按钮。
这时会发现下面这种问题，

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720130602672.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

为什么没有出现 127.0.0.1:6379> 
这和在终端运行(下图)时情况不一样呐？

![终端运行](https://img-blog.csdnimg.cn/20210720130725543.png)

问题出现在一个叫 linenoise 的函数上

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720131105539.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720131014553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

clion 上运行方式不受支持，导致采取第三种方式处理输入，咱们可以手动改变 linenoise 源码强制让其使用第二种输入处理方式。
比如直接将第二个 if 条件换成 1 ，强制使用第二种方式处理输入。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720131516738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

仅修改代码还不够，还需要对项目重新编译。而且 linenoise.c 是属于第三方依赖，咱们得优先重新编译这个第三方依赖。
删除 linenoise 动态链接库

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720131744322.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

注意取消勾选

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720131921859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

之后选中 linenoise 文件夹下的 Makefile ，点击鼠标右键，选择 Run "MakeFile"，linenoise 就会重新编译生成 linenoise.o 动态链接库。

![编译生成 linenoise.o ](https://img-blog.csdnimg.cn/2021072013224931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)

接着对 redis-cli 重新编译下，注意重新切换回 redis-cli

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720132411640.png)

点击标签栏的 Build ，选择 Rebuild "redis-cli"，等待编译成功。
点击 运行 按钮，接下来就可以像在终端使用那样使用 redis 。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720132644602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)
#### redis 调试
在调试模式下也能正常运行

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210720132934473.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NjYzMDAy,size_16,color_FFFFFF,t_70)
## 结语
redis 执行成功，去享受调试 redis 源码带来的乐趣叭！
