---
layout: post
category : v8
title : "v8 Embedder的指南"
tagline: "学习笔记"
tags : [v8, Debug, javascript, v8 Embedder, 调试v8]
excerpt: 如果你读过的[入门指南](https://github.com/v8/v8/wiki/Getting%20Started%20with%20Embedding)，你就已经熟悉V8的一些关键概念（如句柄`handles`，作用域`scopes`和上下文`contexts`）并把V8当做一个独立的虚拟机使用。本文档将进一步讨论这些概念，并介绍其他在你自己的C++应用程序中嵌入V8的关键。
---
{% include JB/setup %}

## Embedder的指南

如果你读过的[入门指南](https://github.com/v8/v8/wiki/Getting%20Started%20with%20Embedding)，你就已经熟悉V8的一些关键概念（如句柄`handles`，作用域`scopes`和上下文`contexts`）并把V8当做一个独立的虚拟机使用。本文档将进一步讨论这些概念，并介绍其他在你自己的C++应用程序中嵌入V8的关键。

V8 API提供了编译和执行脚本，访问C++方法和数据结构，处理错误和启用安全检查的功能。你的应用程序可以像任何其他C++库一样使用V8。你的C++代码通过包含V8的头文件来v8的API`#include "include/v8.h"`。

[V8 Design Elements](https://github.com/v8/v8/wiki/Design%20Elements)文件提供了背景，在优化你的V8应用程序时可能会用到。

### 读者

本文档面向想要在C++应用程序中嵌入V8 JavaScript引擎的C++程序员。它将帮助你使自己的应用程序的C++对象和方法可用于JavaScript，并使JavaScript对象和函数可用于你的C++应用程序。

### 句柄`Handles`和垃圾回收`Garbage Collection`

句柄提供对JavaScript对象在堆中的位置的引用。V8垃圾回收器回收不能再被访问的对象使用的内存。在垃圾回收过程中，垃圾回收器经常会将对象移动到堆中的不同位置（优化整理内存块）。当垃圾回收器移动对象时，垃圾回收器也会更新引用了位置更新的对象的所有句柄。

如果一个对象无法通过JavaScript访问，并且没有引用它的句柄，则该对象被认为是垃圾。垃圾回收器会不时地删除所有被认为是垃圾的对象。V8的垃圾回收机制是V8性能的关键。要了解更多相关内容请看[V8 Design Elements](https://github.com/v8/v8/wiki/Design%20Elements)。

**几种类型的句柄：**

1. 局部句柄`Local Handles`保存在堆栈中，并在调用相应的析构函数时被删除。这些句柄的生命周期由句柄域`handle scope`决定，句柄域`handle scope`通常在函数调用开始时创建。当删除句柄域`handle scope`时，垃圾回收器可以释放之前由句柄域`handle scope`中的句柄引用的那些对象，前提是它们不再可以通过JavaScript或其他句柄访问。这种类型的句柄在示例中[Getting Started](https://github.com/v8/v8/wiki/Getting%20Started%20with%20Embedding)。

局部句柄类`Local<SomeType>`

>注意：句柄堆栈不是C++调用堆栈的一部分，但句柄域`handle scope`嵌入在C++堆栈中。句柄域`handle scope`只能是堆栈分配，不能用`new`分配。

2. 持久句柄`Persistent Handles`提供对堆分配的JavaScript对象的引用，就像局部句柄一样。有两种风格:不同在于他们处理引用的生命周期管理不同。当需要保留对一个对象的引用以进行多个函数调用时，或者当句柄生存期与C++作用域不对应时使用持久句柄。例如，Google Chrome使用持久句柄来引用文档对象模型`DOM`节点。持久句柄可以使用`PersistentBase::SetWeak`变成弱持久句柄。当一个对象唯一的引用来自一个弱句柄时，垃圾回收器触发一个回调。

a. 一个`UniquePersistent<SomeType>`句柄依赖于C++的构造函数和析构函数来管理基本对象的生命周期。

b. 一个`Persistent<SomeType>`可以用它的构造函数构造，但必须有明确用`Persistent::Reset`清除。

3. 还有其他类型的句柄很少使用，我们在这里只会简单提及

a. `Eternal`是预期永远不会被删除的JavaScript对象的持久句柄。它使用起来更轻耗，因为它减轻了垃圾回收器确定该对象的活性。

b. `Persistent`和`UniquePersistent`都不能被复制，这使得它们不适合作为带有`pre-C++ 11`标准库容器的值。`PersistentValueMap`和`PersistentValueVector`为持久值提供了有着`Map`和类似容器的语义的容器类。`C++ 11`嵌入器不需要这些，因为`C++ 11`移动语义`classA(classA &&)`解决了基本的问题。

当然，每次创建一个对象时创建一个局部句柄都会导致很多句柄！这是句柄域`handle scope`非常有用。你可以将句柄域作为一个容纳大量句柄的容器。当调用句柄域`handle scope`的析构函数时，在该作用域内创建的所有句柄将从堆栈中删除。正如你所期望的，这会导致句柄指向的对象符合被垃圾回收器从堆中删除的条件。

回到我们很简单的例子，如[Getting Started](https://github.com/v8/v8/wiki/Getting%20Started%20with%20Embedding)中描述，下图中可以看到句柄栈和堆分配的对象。请注意，`Context::New()`返回一个局部句柄，在他的基础上我们创建一个持久句柄来展示持久性句柄的使用。

![Alt text](./1481710140568.png)


当析构函数`HandleScope::~HandleScope`被调用，句柄域`handle scope`将被删除。如果没有其他引用，则已删除句柄域`handle scope`内的句柄引用的对象符合在下一个垃圾回收中移除的条件。垃圾回收器也可以删除`source_obj`，并且堆里面的`script_obj`对象不再被任何句柄引用，或者不再被以其他来自Javascript方式访问。由于上下文句柄`context handle`是一个持久句柄，当退出句柄域`handle scope`时，它不会被删除。删除上下文句柄`context handle`的唯一方法是显式调用`Reset`就可以了。

>注意：在本文档中，术语handle是指一个局部句柄，当讨论一个持久句柄时，该术语是使用完整的`Local Handle`。

了解这个模型中的一个常见缺陷是重要的：你不能从在一个句柄域`handle scope`声明的函数中直接返回一个局部句柄。如果你这样做，你试图返回的局部句柄最终将在函数返回之前被这个句柄域`handle scope`的析构函数销毁。正确返回局部句柄的方法是构建一个`EscapableHandleScope`代替`HandleScope`，并调用句柄域上的`Escape`方法，传入要返回其值的句柄。下面是一个在实践中如何工作的例子：

```cpp
// This function returns a new array with three elements, x, y, and z.
Local<Array> NewPointArray(int x, int y, int z) {
  v8::Isolate* isolate = v8::Isolate::GetCurrent();

  // We will be creating temporary handles so we use a handle scope.
  EscapableHandleScope handle_scope(isolate);

  // Create a new empty array.
  Local<Array> array = Array::New(isolate, 3);

  // Return an empty result if there was an error creating the array.
  if (array.IsEmpty())
    return Local<Array>();

  // Fill out the values
  array->Set(0, Integer::New(isolate, x));
  array->Set(1, Integer::New(isolate, y));
  array->Set(2, Integer::New(isolate, z));

  // Return the value through Escape.
  return handle_scope.Escape(array);
}
```
该`Escape`方法复制它的参数到封闭作用域内的值，删除其所有的局部句柄，然后还给新句柄副本可以安全返回。

### 上下文`Contexts`

在V8中，上下文是一个允许分离的，不相关的JavaScript应用程序在V8的单个实例中运行执行环境。你必须显式指定任何要运行的JavaScript代码的上下文。

为什么这是必要的？因为JavaScript内置了一套实用的可以通过JavaScript代码更改的函数和对象。例如，如果两个完全不相关的JavaScript函数以相同的方式改变了全局对象，那么很可能发生意外的结果。

在CPU时间和内存方面，创建新的执行上下文并构建给定数量的内置对象可能看起来是一个昂贵的操作。然而，V8的大量的缓存确保虽然你创建的第一个上下文有点昂贵，但后续的上下文要廉价得多。这是因为第一个上下文需要创内置对象并解析内置的JavaScript代码，而后续上下文只需为其上下文创建内置对象。随着V8快照功能（带构建选项激活`snapshot=yes`将作为一个快照被高度优化花在创建第一个方面的时候，包括其中包含了内置的JavaScript代码已编译的代码序列化堆，这是默认的）。随着垃圾回收，V8广泛的缓存也是关键，V8的表现，更多信息请参考设计元素。

创建上下文时，可以任意的进入和退出。当你在上下文A中时，你也可以进入不同的上下文B，这意味着你用当前上下文B替换A。当你退出B时，A将恢复为当前上下文。如下图所示：

![Alt text](./1481710181646.png)

>注意，每个上下文内置的通用函数和对象保持分离。在创建上下文时，你可以选择性的设置一个安全令牌。请参阅安全模型[Security Model](https://github.com/v8/v8/wiki/Embedder's%20Guide#security-model)的详细信息部分。

在V8中使用上下文的动机是使浏览器中的每个窗口和`iframe`都有自己新的JavaScript环境。

### 模板`Templates`

模板是上下文中JavaScript函数和对象的蓝图。你可以使用模板来封装C++函数和数据结构到JavaScript对象中，以便它们可以由JavaScript脚本处理。例如，Google Chrome使用模板将`C++ DOM`节点封装为JavaScript对象，并在全局命名空间中安装这些功能。你可以创建一组模板，然后对每个新的上下文使用同一个模板。你可以拥有任意所需数量的模板。但是任何给定的上下文中只能有任意一个模板的一个实例。

在JavaScript中，函数和对象之间存在强烈的二元性。当我们在Java或C++创建一个新类型的对象时通常会定义一个新类。而在JavaScript中我们创建一个新的函数来代替，并使用该函数作为构造函数来创建实例。JavaScript对象的设计和功能与构建它的函数密切相关。这反映在V8两种模板的工作方式上。

1. 功能模板`Function templates`

函数模板是单个函数的蓝图。可以在你希望实例化这个JavaScript函数的上下文环境中通过调用模板的`GetFunction`方法来创建模板的一个JavaScript实例，你还可以将C++回调关联到一个JavaScript函数实例被调用时触发的函数模板。

2. 对象模板`Object templates`

每个函数模板都有一个关联的对象模板。这用于将使用此函数创建的对象配置为其构造函数。你可以将两种类型的C++回调与对象模板关联：

  - 当脚本访问对象特定的属性时，调用访问器回调

  - 当脚本访问对象任何的属性时，调用拦截器回调。存取器和拦截器将在本文后面讨论。

以下代码提供了为全局对象创建模板并设置内置全局函数的示例。

```cpp
// Create a template for the global object and set the
// built-in global functions.
Local<ObjectTemplate> global = ObjectTemplate::New(isolate);
global->Set(String::NewFromUtf8(isolate, "log"), FunctionTemplate::New(isolate, LogCallback));

// Each processor gets its own context so different processors
// do not affect each other.
Persistent<Context> context = Context::New(isolate, NULL, global);
```

这个示例代码取自`process.cc`示例里的`JsHttpProcessor::Initializer`方法中。

### 访问器

访问器是一个C++回调，当对象属性被JavaScript脚本访问时，计算并返回一个值。访问器通过一个对象模板构造，使用`SetAccessor`方法。这个方法使用与其相关联的属性名称，以及当脚本尝试读取或写入属性时要运行的两个回调作为参数。

访问器的复杂性取决于你操作的数据类型：

- 访问静态全局变量[Accessing Static Global Variables](https://github.com/v8/v8/wiki/Embedder's%20Guide#accessing-static-global-variables)

- 访问动态变量[Accessing Dynamic Variables](https://github.com/v8/v8/wiki/Embedder's%20Guide#accessing-dynamic-variables)

**访问静态全局变量**

比方说，有两个C++整数变量x和y，他们要被提供给JavaScript作为作用域内的全局变量。为此，你需要在脚本读取或写入这些变量时调用C++访问器函数。这些访问器函数使用`Integer::New`把一个C++整数转换成一个JavaScript整数，以及使用`Int32Value`将一个JavaScript整数转换为一个C++整数。如下例所示：

```cpp
void XGetter(Local<String> property,
              const PropertyCallbackInfo<Value>& info) {
  info.GetReturnValue().Set(x);
}

void XSetter(Local<String> property, Local<Value> value,
             const PropertyCallbackInfo<Value>& info) {
  x = value->Int32Value();
}

// YGetter/YSetter are so similar they are omitted for brevity

Local<ObjectTemplate> global_templ = ObjectTemplate::New(isolate);
global_templ->SetAccessor(String::NewFromUtf8(isolate, "x"), XGetter, XSetter);
global_templ->SetAccessor(String::NewFromUtf8(isolate, "y"), YGetter, YSetter);
Persistent<Context> context = Context::New(isolate, NULL, global_templ);
```

### 访问动态变量

在前面的例子中，变量是静态的和全局的。如果被操作的数据是动态的，如在浏览器中的DOM树的真实情况呢？让我们想象一下x，和y都是C++`Point类`的对象字段属性：

```cpp
class Point {
public:
  Point(int x, int y) : x_(x), y_(y) { }
  int x_, y_;
}
```

为了使任意数量的C++的`Point类`实例可用于JavaScript，我们需要为每个C++`Point类` 创建一个JavaScript对象，并连接JavaScript对象和C++的实例。这是通过外部值和内部对象字段属性完成的。

首先为Point包装对象创建一个对象模板：

```cpp
Local<ObjectTemplate> point_templ = ObjectTemplate::New(isolate);
```

每个JavaScript的point对象维持一个对C++对象的引用，这就是一个内部字段属性的包装。这些字段属性被如此命名是因为它们不能从JavaScript中访问，它们只能从C++代码访问。对象可以具有任意数量的内部字段属性，内部字段属性的数量在对象模板上设置如下：

```cpp
point_templ->SetInternalFieldCount(1);
```

这里内部字段计数设置为1，意味着该对象具有一个内部属性字段、一个索引0, 并指向一个C++的对象。

**x和y访问器添加到模板：**

```cpp
point_templ.SetAccessor(String::NewFromUtf8(isolate, "x"), GetPointX, SetPointX);
point_templ.SetAccessor(String::NewFromUtf8(isolate, "y"), GetPointY, SetPointY);
```

接下来，通过创建模板的新实例来包装C++ Point类对象，然后设置`point p`的外部包的内部字段为0。

```cpp
Point* p = ...;
Local<Object> obj = point_templ->NewInstance();
obj->SetInternalField(0, External::New(isolate, p));
```

外部对象只是void*的包装。外部对象只能用于在内部字段中存储引用值。JavaScript对象不能直接引用C++对象，所以外部值用作从JavaScript到C++的“桥梁”。在这个意义上，外部值是句柄的反例，因为句柄允许C++引用JavaScript对象。

这里定义了x的get和set访问器，y的访问器定义是相同的，只是y替换x：

```cpp
void GetPointX(Local<String> property,
               const PropertyCallbackInfo<Value>& info) {
  Local<Object> self = info.Holder();
  Local<External> wrap = Local<External>::Cast(self->GetInternalField(0));
  void* ptr = wrap->Value();
  int value = static_cast<Point*>(ptr)->x_;
  info.GetReturnValue().Set(value);
}

void SetPointX(Local<String> property, Local<Value> value,
               const PropertyCallbackInfo<Value>& info) {
  Local<Object> self = info.Holder();
  Local<External> wrap = Local<External>::Cast(self->GetInternalField(0));
  void* ptr = wrap->Value();
  static_cast<Point*>(ptr)->x_ = value->Int32Value();
}
```

访问器提取point对象的参考，是由JavaScript对象包裹，然后读取和写入相关的领域对象。这样，这些通用访问器可以用于任意数量的包装过的point对象。

### 拦截器

你还可以为脚本访问任何对象属性时指定回调。这些被称为拦截器。为了效率，有两种类型的拦截器：

1. **命名属性拦截器** -以字符串名称访问属性时调用。在浏览器环境中`document.theFormName.elementName`

2. **索引属性拦截器** -以索引访问属性时调用。在浏览器的环境中`document.forms.elements[0]`

v8源码中配套的示例代码process.cc包含了使用拦截器的一个例子。在下面的代码片段`SetNamedPropertyHandler`指定`MapGet`和`MapSet`拦截器：

```cpp
Local<ObjectTemplate> result = ObjectTemplate::New(isolate);
result->SetNamedPropertyHandler(MapGet, MapSet);
```

`MapGet`拦截器如下：

```cpp
void JsHttpRequestProcessor::MapGet(Local<String> name,
                                    const PropertyCallbackInfo<Value>& info) {
  // Fetch the map wrapped by this object.
  map<string, string> *obj = UnwrapMap(info.Holder());

  // Convert the JavaScript string to a std::string.
  string key = ObjectToString(name);

  // Look up the value if it exists using the standard STL idiom.
  map<string, string>::iterator iter = obj->find(key);

  // If the key is not present return an empty handle as signal.
  if (iter == obj->end()) return;

  // Otherwise fetch the value and wrap it in a JavaScript string.
  const string &value = (*iter).second;
  info.GetReturnValue().Set(String::NewFromUtf8(value.c_str(), String::kNormalString, value.length()));
}

```

与访问器一样，只要访问属性，就会调用指定的回调。访问器和拦截器之间的区别是拦截器处理所有属性，而访问器与一个特定属性相关联。

### 安全模型

“同源策略”（首先由Netscape Navigator 2.0引入）防止从一个“源”加载的文档或脚本从不同的“源”获取或设置文档的属性。定义为域名（www.example.com），协议（http或https）和端口（例如，www.example.com:81是不一样的www.example.com）必须完全匹配才能将两个网页视为具有相同的来源。没有这种保护，恶意网页可能损害另一网页的完整性。

在V8中，`origin`被定义为上下文。默认情况下不允许访问除你正在调用的任何上下文之外的任何上下文。要访问除了正在调用的上下文之外的上下文，你需要使用安全性令牌或安全回调。安全令牌可以是任何值，但通常是一个符号，一个不存在于其他地方的规范字符串。你还可以指定一个安全令牌SetSecurityToken，当你建立了一个上下文。如果不指定安全性令牌，V8将自动为你正在创建的上下文生成一个。

当尝试访问全局变量时，V8安全系统首先根据尝试访问全局对象的代码的安全性令牌检查正在访问的全局对象的安全性令牌。如果授予令牌匹配访问权限。如果令牌不匹配V8执行回调以检查是否应允许访问。你可以通过对对象模板使用SetAccessCheckCallbacks方法来指定是否应允许对对象的访问。然后V8安全系统可以获取正在访问的对象的安全回调，并调用它来询问是否允许另一个上下文访问它。这个回调给出被访问的对象，被访问的属性名，访问类型（例如读，写或删除），并返回是否允许访问。

该机制在谷歌浏览器实现的，因此，如果安全令牌不匹配，一个特殊的回调用于只允许以下访问：window.focus()，window.blur()，window.close()，window.location，window.open()，history.forward()，history.back()，和history.go()。

### 异常

如果发生错误，V8将抛出异常 - 例如，当脚本或函数尝试读取不存在的属性，或者调用的函数不是函数时。

如果操作未成功，V8将返回空句柄。因此，重要的是，在继续执行之前，你的代码检查返回值不是空句柄。检查与空句柄Local类的公共成员函数IsEmpty()。

你能使用TryCatch捕获异常，例如：

```cpp
TryCatch trycatch(isolate);
Local<Value> v = script->Run();
if (v.IsEmpty()) {
  Local<Value> exception = trycatch.Exception();
  String::Utf8Value exception_str(exception);
  printf("Exception: %s\n", *exception_str);
  // ...
}
```

如果返回值是一个空的句柄，而你没有TryCatch到位，你的代码必定会退出。如果你有异常被TryCatch捕获那你的代码允许继续执行。

### 继承

每个FunctionTemplate都有PrototypeTemplate给出函数原型的模板方法。您可以在PrototypeTemplate上设置属性，并将C ++函数与这些属性相关联，然后它将出现在相应FunctionTemplate的所有实例上。例如：

```cpp
Local<FunctionTemplate> childTemplate = FunctionTemplate::New(isolate);
childTemplate->PrototypeTemplate().Set(
    String::NewFromUtf8(isolate, "propertyA"),
    FunctionTemplate::New(isolate, MyWheelsMethodCallback)->GetFunction();
)
```

这将导致所有childTemplate实例的原型链中有一个属性propertyA，属性propertyA被调用的时候，C++回调函数MyWheelsMethodCallback也会被调用。

V8的FunctionTemplate类提供公共成员函数Inherit()，当想要一个函数模板从另一个函数模板继承时调用，如下所示：

```cpp
void Inherit(Local<FunctionTemplate> parent);
```