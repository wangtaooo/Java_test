https://blog.csdn.net/weiwangchao_/article/details/48137417
编译C语言动态链接库，给java调用，以上是参考链接：
1、创建文件vim HelloNative.java
  1 public class HelloNative {
  2     public native void sayHello();
  3 }
生成.java文件
javac HelloNative.java
生成.h文件
javah HelloNative
2、创建vim HelloNative.cpp
  1 #include <stdio.h>
  2 #include "HelloNative.h"
  3 
  4 JNIEXPORT void JNICALL Java_HelloNative_sayHello(JNIEnv *, jobject)
  5 {
  6     printf("Hello World!\n");
  7 }
编译动态链接库
g++ -fPIC -I/usr/lib/jvm/java-8-openjdk-amd64/include/ -I/usr/lib/jvm/java-8-openjdk-amd64/include/linux/ -shared -o libHelloNative.so HelloNative.cpp
3、创建vim TestNative.java
  1 public class TestNative
  2 {
  3     static {
  4         try {
  5             System.loadLibrary("HelloNative"); //没有lib前缀
  6         }
  7         catch(UnsatisfiedLinkError e) {
  8             System.out.println( "Cannot load hello library:\n " + e.toString() );
  9         }
 10     }
 11     public static void main(String[] args) {
 12         HelloNative test = new HelloNative();
 13         test.sayHello();
 14     }
 15 }
4、先编译
javac TestNative.java
直接编译会出错，所以修改环境变量：
export LD_LIBRARY_PATH=.:$LD_LIBRARY_PATH
source ~/.bashrc 
java TestNative
