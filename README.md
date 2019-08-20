### iOS Swift Common Architecture

>meta-programming(元编程-code write code)

```
可以从两个层面上来理解：

在运行时通过反射之类的技术来动态修改程序自身的结构。比如说我们都非常熟悉的Objective-C Runtime。

通过DSL来生成特定的代码，这通常发生在编译期预处理阶段。
```

#### Sourcery
>sourcery read config from .sourcery.yml 

- pod 'Sourcery'
- Shell Config (Under Check Pods Manifest.lock)

```
if [ "$CONFIGURATION" == "Debug" ] ; then
$PODS_ROOT/Sourcery/bin/sourcery
fi
```

- Config File

```
- sources: source file
- templates: stencil file
- output: generated file
```

- 命令行实现

```
sourcery \
   --sources ./YourProject \
   --templates ./YourTemplates \
   --output ./YourProject/AutoGenerated.swift
```

- yml配置文件实现

```
.sourcery.yml

sources:
  - SourceryT/Class
templates:
  - Templates/AutoEquatable.stencil
  #- Templates/Colors.stencil
  #- Templates/ModelConvert.stencil
  #- Templates/Router.stencil
  #...
output:
  Generated

```

```
AutoEquatable.stencil

{# AutoEquatable Generator Template #}
{{"//autoequatable"|uppercase}}
{% for type in types.implementing.AutoEquatable %}
//MARK: - {{type.name}}
extension {{type.name}}: Equatable {
    static func == (lhs:{{type.name}}, rhs: {{type.name}}) -> Bool {
        {% for variable in type.storedVariables %}
        guard lhs.{{variable.name}} == rhs.{{variable.name}} else {
            return false
        }
        {% endfor %}
        return true
    }
}
{% endfor %}

```

##### Sourcery Annotation

> Sourcery自定义 规则

- [Documents](https://littlebitesofcocoa.com/295-building-an-api-client-with-sourcery-key-value-annotations)

```
e.g.
/// sourcery: key = value
/// sourcery: anotherKey = someOtherValue
also
/// sourcery: key = value, anotherKey = someOtherValue
contain some basic types

/// sourcery: maxSpeed = 1500, hasHyperdrive = true
/// sourcery: codename = "blacksaber"

```

- Exp

```
enum SpaceshipsAPI {
    /// sourcery: method = "GET", path = "/spaceships"
    /// sourcery: timeout = 5
    case ships
    ...
}

//SpaceshipsAPI+Properties.stencil
extension SpaceshipsAPI {
  public var path: String? {
    switch self {
      {% for c in type.SpaceshipsAPI.cases %}
      {% if c.annotations.path %}
      case .{{ c.name }}: return "{{c.annotations.path}}"
      {% endif %}
    {% endfor %}
    default: return nil
    }
  }

  // ... etc
}
```


##### Stencil

```
Stencil是一门用Swift实现的专门为Swift设计的模板语言
```

- [GitHub](https://github.com/stencilproject/Stencil)

- {{ ... }}
>变量语法，将中间的部分作为变量（或变量的表达式）来解析，解析后的值会作为结果插入到模板中的相应位置上。

- {% ... %}
>标签语法（Tag），标签用来表示一些具有特殊功能的语法，比如用来实现判断的if和循环的for。

- {# ... #}
>注释语法，不会出现在解析后的结果中


>除此之外还有一个名为Filter的概念，它的语法是这样的：{{ "stencil"|uppercase }}。符号|左边是输入的变量，右边就是一个Filter，这里输出了字符串的大写形式.
>[String Filter](https://github.com/SwiftGen/StencilSwiftKit/blob/master/Documentation/filters-strings.md)




##### StencilSwiftKit

- [Documentation](https://github.com/SwiftGen/StencilSwiftKit/tree/master/Documentation)

##### Projects that use Stencil

- [Soucery](https://github.com/krzysztofzablocki/Sourcery)
- [SwiftGen](https://github.com/SwiftGen/SwiftGen)
- [Weaver](https://github.com/scribd/Weaver)


#### VSCode - Stencil/YAML 插件

##### YAML

>YAML: YAML Ain't Markup Language

- [ORG](https://yaml.org)
- [1.2](https://yaml.org/spec/1.2/spec.html)

##### YamlSwift

> 解析一串YAML文档( 或者JSON文档) 并返回一个表示该字符串的Yaml enum 值

- [GitHub](https://github.com/behrang/YamlSwift)


#### Apple/SwiftSyntax

>It allows for Swift tools to parse, inspect, generate, and transform Swift source code.

- [SwiftSyntax](https://github.com/apple/swift-syntax)

#### SwiftGen/StencilSwiftKit

- [Documents](https://github.com/SwiftGen/StencilSwiftKit)

### Others

- SwiftGen
- SwiftLint
- URLNavigator
- XCConfig
- SwiftMonkey(Paws) (Test)
- DoraemonKit
- SwifterSwift(more than 500 native Swift extensions)