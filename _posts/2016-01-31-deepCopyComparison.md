---
layout: post
title: 三种深度拷贝方式的比较
---

对于深度拷贝，常见有主要有三种方式。通过序列化和反序列化，通过json转换，以及手动赋值，下面对这三种方式进行简单的比较。
用来拷贝测试的对象为：
`
class User implements Serializable{
    private String name;
    private String pwd;
    private int age;

    public User() {
    }

    public User (User u){
        this.name = u.getName();
        this.age = u.getAge();
        this.pwd = u.getPwd();
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
`
* 序列化方式：
这里采用apache.common.lang包中的SerializationUtils.clone()工具类。这里测试进行10w次简单对象的深度拷贝所用的时间, 
序列化方式深拷贝的测试代码如下：
```
 User user = new User();
 user.setName("shamo");
 user.setPwd("123");
 user.setAge(25);

 long start = System.currentTimeMillis();
 for(int i = 0; i < 100000; i++){
    SerializationUtils.clone(user);
 }
 System.out.println(System.currentTimeMillis() - start);
```
在我的笔记本上的平均耗时约为**2100ms**。

* json转换方式：
这里采用阿里的fastjson。同时测试10w次简单对象的深拷贝，测试代码如下：

```
 long start = System.currentTimeMillis();
 for(int i = 0; i < 100000; i++){
    deepCopyByJson(user);
 }

 System.out.println(System.currentTimeMillis() - start);

 private Object deepCopyByJson(Object obj) {
     String json = JSON.toJSONString(obj);
     return JSON.parseObject(json, Object.class);
 }
```
耗时约为**900ms**。

* 手动赋值方式：
	为了方便，这里采用构造方法的方式，即通过new User(user)进行深拷贝;<br>
10w次的平均耗时约为**5ms**。

**总结：** <br>
可以看出手工赋值的方式相对其他两种方式在速度上有绝对的优势，但是会有大量的对象属性赋值代码，特别是如果属性中含有引用类型或其他复杂自定义的属性，使用手工赋值会很困难。<br>
使用序列化的方式速度最慢，而且待拷贝的对象的类必须实现Serializable接口。<br>
使用json的方式速度较快，对类除了需要符合java bean规则也没有其他限制，是比较理想的深度拷贝方式。<br>