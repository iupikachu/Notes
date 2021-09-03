## Protocol Buffers



### 1. java序列化缺陷



* #### 无法跨语言

  现在很多系统的复杂度很高，采用多种语言来编码，而Java序列化目前只支持Java语言实现的框架，其它语言大部分都没有使用Java的序列化框架，也没有实现Java序列化这套协议，因此，如果两个基于不同语言编写的应用程序之间通信，使用Java序列化，则无法实现两个应用服务之间传输对象的序列化和反序列化。

* #### 易被攻击

* #### 序列化后的流太大

* #### 序列化性能太差



### 2.使用 Protocol Buffers



```xml
syntax = "proto2";

package tutorial;

option java_multiple_files = true;
option java_package = "com.example.tutorial.protos";
option java_outer_classname = "AddressBookProtos";

message Person {
  optional string name = 1;
  optional int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    optional string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phones = 4;
}

message AddressBook {
  repeated Person people = 1;
}
```



package 包声明



可选项:

option java_multiple_files = true;
option java_package = "com.example.tutorial.protos";  
option java_outer_classname = "AddressBookProtos";



*  java_multiple_files

  为每个生成的类生成单独的 .java文件

* java_package  

  指定生成的类在哪个包中，如果不声明，就匹配包声明（通常不是合适的java包名）

* java_outer_classname 

  根据下面的信息生成的类名称，如果不指定就将文件名转换为大写驼峰来命名

  例如:

  "my_proto.proto"    ->  MyProto.class



**消息定义**

消息只是包含一组类型字段的聚合

字段的类型和 java类型有相应的对应，而且在消息中可以嵌套其他的消息。

如 message Person 中嵌套了 message PhoneNumber



每个元素上的 “=1” “=2” 标记该字段在二进制编码中使用的唯一标签

1-15 相对于更高的标签数字少一个字节，所以对于常用和重复的元素用1-15标记，作为优化。

重复字段中的每个元素都需要重新编码标签编号，因此重复字段特别适合这种优化。



**字段修饰符**

```protobuf
  // 字段规则：required -> 字段只能也必须出现 1 次
  // 字段规则：optional -> 字段可出现 0 次或1次
  // 字段规则：repeated -> 字段可出现任意多次（包括 0）重复值的顺序将保留在协议缓冲区中
```

