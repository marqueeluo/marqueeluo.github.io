# 一、安装python39
# 二、在python39中安装Twisted
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210106100037650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

# 三、在pycharm中新建base interceptor（包含Twisted及相关依赖）
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021010610000460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210106095858131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)

# 四、新建virtualenv，继承base interceptor及其相关依赖，且对所有用户可见
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210106100212235.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x1bzE1MjQyMjA4MzEw,size_16,color_FFFFFF,t_70)
<font color='red'><b>注：</b></font>
之后新建的virtualenv都需要基于此base interceptor，且继承安装的Twisted及相关依赖，如果新的virtualenv不继承Twisted则会报pip安装包异常。

# 五、基于新的virtualenv安装相应的包
