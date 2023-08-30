# 1. 简介

​	本文主要讲述如何在Windows环境下使用visual studio配置grpc，并且实现grpc通信的例子。前文[windows配置和编译grpc](https://llfc.club/articlepage?id=2QSEHcC1he1RgiewYG93ilaAMiY)已经介绍了如何在windows环境配置grpc，接下来我们利用之前编译好的库配置到项目中，并实现grpc通信。



# 2. 项目配置

​	1.我们为了方便配置，将之前下载和编译好的项目放在D盘cppsoft文件夹下，路径为`D:\cppsoft\grpc`。

​	2.接下来我们创建一个项目，名字叫Grpc-Server,在项目的根目录下创建一个proto名字为`demo.proto`，文件中内容如下：

```
syntax = "proto3";

package hello;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

message HelloRequest {
  string message = 1;
}

message HelloReply {
  string message = 1;
}
```



​	3.接下来我们利用grpc编译后生成的protoc.exe生成proto的头文件和源文件

​	PS. 注意这里可以为protoc.exe配置环境变量，就可以省略D:\grpcCode\grpc\vsPro\third_party\protobuf\Debug\

```
D:\grpcCode\grpc\vsPro\third_party\protobuf\Debug\protoc.exe  -I="." --grpc_out="." --plugin=protoc-gen-grpc="D:\grpcCode\grpc\vsPro\Debug\grpc_cpp_plugin.exe" "demo.proto"
```

​	

​	4.**上述命令会生成demo.grpc.pb.h和demo.grpc.pb.cc文件**。

`D:\grpcCode\grpc\vsPro\third_party\protobuf\Debug\protoc.exe`表示protoc.exe所在的路径，你可以将其放在指定目录并配置环境变量，直接使用protoc.exe就行了。**此外，必须用grpc里面的protoc，不然可能会出现proto的版本不匹配的问题**

`-I="."` 指定 `demo.proto`所在的路径为当前路径。

`--grpc_out="."` 表示生成的pb.h和pb.cc文件的输出目录。

`--plugin=protoc-gen-grpc="D:\grpcCode\grpc\vsPro\Debug\grpc_cpp_plugin.exe"`表示要使用的插件为cpp插件，也就是生成cpp类的头文件和源文件。



​	5.接下来我们生成grpc类需要的pb文件，因为要序列化数据。[消息文件]

```
D:\grpcCode\grpc\vsPro\third_party\protobuf\Debug\protoc.exe --cpp_out=. "demo.proto"
```

**上述命令会生成demo.pb.h和demo.pb.cc文件。**

接下来我们在项目中配置grpc库的包含目录和库目录。
为了方便调试，我们配置debug版本



## 2.1 附加包含目录，处理的是从git上colon下来的文件路径

项目属性中选择C++下的常规选项，在附加包含目录中添加我们grpc生成debug库

```
D:\grpcCode\grpc\third_party\re2
D:\grpcCode\grpc\third_party\address_sorting\include
D:\grpcCode\grpc\third_party\abseil-cpp
D:\grpcCode\grpc\third_party\protobuf\src
D:\grpcCode\grpc\include
```



## 2.2 库目录，处理的是cmake并且编译后的项目路径，如build

接下来配置库路径, 在链接器常规选项下，点击附加库目录，添加我们需要的库目录

```
D:\grpcCode\grpc\vsPro\third_party\re2\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\types\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\synchronization\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\status\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\random\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\flags\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\debugging\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\container\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\hash\Debug
D:\grpcCode\grpc\vsPro\third_party\boringssl-with-bazel\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\numeric\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\time\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\base\Debug
D:\grpcCode\grpc\vsPro\third_party\abseil-cpp\absl\strings\Debug
D:\grpcCode\grpc\vsPro\third_party\protobuf\Debug
D:\grpcCode\grpc\vsPro\third_party\zlib\Debug
D:\grpcCode\grpc\vsPro\Debug
D:\grpcCode\grpc\vsPro\third_party\cares\cares\lib\Debug
```

## 2.3 附加依赖项，处理的是cmake并且编译后的项目lib路径，如build

```
libprotobufd.lib
gpr.lib
grpc.lib
grpc++.lib
grpc++_reflection.lib
address_sorting.lib
ws2_32.lib
cares.lib
zlibstaticd.lib
upb.lib
ssl.lib
crypto.lib
absl_bad_any_cast_impl.lib
absl_bad_optional_access.lib
absl_bad_variant_access.lib
absl_base.lib
absl_city.lib
absl_civil_time.lib
absl_cord.lib
absl_debugging_internal.lib
absl_demangle_internal.lib
absl_examine_stack.lib
absl_exponential_biased.lib
absl_failure_signal_handler.lib
absl_flags.lib
absl_flags_config.lib
absl_flags_internal.lib
absl_flags_marshalling.lib
absl_flags_parse.lib
absl_flags_program_name.lib
absl_flags_usage.lib
absl_flags_usage_internal.lib
absl_graphcycles_internal.lib
absl_hash.lib
absl_hashtablez_sampler.lib
absl_int128.lib
absl_leak_check.lib
absl_leak_check_disable.lib
absl_log_severity.lib
absl_malloc_internal.lib
absl_periodic_sampler.lib
absl_random_distributions.lib
absl_random_internal_distribution_test_util.lib
absl_random_internal_pool_urbg.lib
absl_random_internal_randen.lib
absl_random_internal_randen_hwaes.lib
absl_random_internal_randen_hwaes_impl.lib
absl_random_internal_randen_slow.lib
absl_random_internal_seed_material.lib
absl_random_seed_gen_exception.lib
absl_random_seed_sequences.lib
absl_raw_hash_set.lib
absl_raw_logging_internal.lib
absl_scoped_set_env.lib
absl_spinlock_wait.lib
absl_stacktrace.lib
absl_status.lib
absl_strings.lib
absl_strings_internal.lib
absl_str_format_internal.lib
absl_symbolize.lib
absl_synchronization.lib
absl_throw_delegate.lib
absl_time.lib
absl_time_zone.lib
absl_statusor.lib
re2.lib
```

## 2.4 grpc 服务器代码实现

`GreeterServiceImpl` 继承自 `Greeter::Service`，重写了`SayHello`函数，当收到客户端发送的`SayHello`请求后执行重写函数功能的逻辑。

```
#include <iostream>
#include <memory>
#include <string>
#include <grpcpp/grpcpp.h>
#include "demo.grpc.pb.h"

using grpc::Server;
using grpc::ServerBuilder;
using grpc::ServerContext;
using grpc::Status;
using hello::HelloRequest;
using hello::HelloReply;
using hello::Greeter;//Greeter 是demo.proto中定义的service

// Logic and data behind the server's behavior.
class GreeterServiceImpl final : public Greeter::Service {
    Status SayHello(ServerContext* context, const HelloRequest* request,
        HelloReply* reply) override {
        std::string prefix("llfc grpc server has received :  ");
        reply->set_message(prefix + request->message());
        return Status::OK;
    }
};

void RunServer() {
    std::string server_address("127.0.0.1:50051");
    GreeterServiceImpl service;
    ServerBuilder builder;
    
    // Listen on the given address without any authentication mechanism.
    // 监听给定的地址
    builder.AddListeningPort(server_address, grpc::InsecureServerCredentials());
    
    // Register "service" as the instance through which we'll communicate with
    // clients. In this case it corresponds to an *synchronous* service.
    builder.RegisterService(&service);
    
    // Finally assemble the server.
    std::unique_ptr<Server> server(builder.BuildAndStart());
    std::cout << "Server listening on " << server_address << std::endl;
    
    // Wait for the server to shutdown. Note that some other thread must be
    // responsible for shutting down the server for this call to ever return.
    server->Wait();
}

int main(int argc, char** argv) {
    RunServer();
    return 0;
}
```



## 2.5 grpc客户端代码实现

grpc客户端比较简单，直接调用发送接口发送数据即可

```
#include <string>
#include <iostream>
#include <memory>
#include <grpcpp/grpcpp.h>
#include "demo.grpc.pb.h"

using grpc::ClientContext;
using grpc::Channel;
using grpc::Status;
using hello::HelloReply;
using hello::HelloRequest;
using hello::Greeter;

// static method : Greeter::NewStub
class FCClient {
public:
    FCClient(std::shared_ptr<Channel> channel)
        :stub_(Greeter::NewStub(channel)) {
    }
    
    std::string SayHello(std::string name) {
        ClientContext context;
        HelloReply reply;
        HelloRequest request;
        request.set_message(name);
        
        Status status = stub_->SayHello(&context, request, &reply);
        if (status.ok()) {
            return reply.message();
        }
        else {
            return "failure " + status.error_message();
        }
    }
private:
    std::unique_ptr<Greeter::Stub> stub_;
};

int main(int argc, char* argv[]) {
    auto channel = grpc::CreateChannel("127.0.0.1:50051", grpc::InsecureChannelCredentials());
    FCClient client(channel);
    
    // block until get result from RPC server
    std::string result = client.SayHello("hello , llfc.club !");
    printf("get result [%s]\n", result.c_str());
    return 0;
}
```

客户端创建了一个channel，然后调用NewStub生成stub，接下来就可以发送数据了