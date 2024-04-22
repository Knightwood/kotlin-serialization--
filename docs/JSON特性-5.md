# JSON features JSON 功能

[](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#json-features)

This is the fifth chapter of the [Kotlin Serialization Guide](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serialization-guide.md). In this chapter, we'll walk through features of [JSON](https://www.json.org/json-en.html) serialization available in the [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) class.  
这是《Kotlin 序列化指南》的第五章。在本章中，我们将演练 Json 类中提供的 JSON 序列化功能。

**Table of contents 目录**

- [Json configuration Json 配置](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#json-configuration)
    - [Pretty printing 漂亮的印刷](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#pretty-printing)
    - [Lenient parsing 宽松解析](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#lenient-parsing)
    - [Ignoring unknown keys 忽略未知键](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#ignoring-unknown-keys)
    - [Alternative Json names 备用 Json 名称](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#alternative-json-names)
    - [Coercing input values 强制输入值](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#coercing-input-values)
    - [Encoding defaults 编码默认值](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#encoding-defaults)
    - [Explicit nulls 显式 null](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#explicit-nulls)
    - [Allowing structured map keys  
        允许结构化映射键](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#allowing-structured-map-keys)
    - [Allowing special floating-point values  
        允许特殊的浮点值](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#allowing-special-floating-point-values)
    - [Class discriminator for polymorphism  
        多态性的类鉴别器](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#class-discriminator-for-polymorphism)
    - [Class discriminator output mode  
        类鉴别器输出方式](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#class-discriminator-output-mode)
    - [Decoding enums in a case-insensitive manner  
        以不区分大小写的方式解码枚举](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#decoding-enums-in-a-case-insensitive-manner)
    - [Global naming strategy 全局命名策略](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#global-naming-strategy)
- [Json elements Json 元素](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#json-elements)
    - [Parsing to Json element  
        解析为 Json 元素](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#parsing-to-json-element)
    - [Types of Json elements  
        Json 元素的类型](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#types-of-json-elements)
    - [Json element builders Json 元素生成器](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#json-element-builders)
    - [Decoding Json elements 解码 Json 元素](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#decoding-json-elements)
    - [Encoding literal Json content (experimental)  
        对文字 Json 内容进行编码（实验性）](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#encoding-literal-json-content-experimental)
        - [Serializing large decimal numbers  
            序列化大十进制数](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#serializing-large-decimal-numbers)
        - [Using `JsonUnquotedLiteral` to create a literal unquoted value of `null` is forbidden  
            禁止用于 `JsonUnquotedLiteral` 创建 的 `null` 文字未加引号的值](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#using-jsonunquotedliteral-to-create-a-literal-unquoted-value-of-null-is-forbidden)
- [Json transformations Json 转换](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#json-transformations)
    - [Array wrapping 数组包装](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#array-wrapping)
    - [Array unwrapping 数组解包](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#array-unwrapping)
    - [Manipulating default values  
        操作默认值](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#manipulating-default-values)
    - [Content-based polymorphic deserialization  
        基于内容的多态反序列化](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#content-based-polymorphic-deserialization)
    - [Under the hood (experimental)  
        引擎盖下（实验性）](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#under-the-hood-experimental)
    - [Maintaining custom JSON attributes  
        维护自定义 JSON 属性](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#maintaining-custom-json-attributes)

## Json configuration Json 配置


The default [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) implementation is quite strict with respect to invalid inputs. It enforces Kotlin type safety and restricts Kotlin values that can be serialized so that the resulting JSON representations are standard. Many non-standard JSON features are supported by creating a custom instance of a JSON _format_.  
默认的 Json 实现对于无效输入非常严格。它强制实施 Kotlin 类型安全性，并限制可序列化的 Kotlin 值，以便生成的 JSON 表示形式是标准的。通过创建 JSON 格式的自定义实例来支持许多非标准 JSON 功能。

To use a custom JSON format configuration, create your own [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) class instance from an existing instance, such as a default `Json` object, using the [Json()](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json.html) builder function. Specify parameter values in the parentheses via the [JsonBuilder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/index.html) DSL. The resulting `Json` format instance is immutable and thread-safe; it can be simply stored in a top-level property.  
要使用自定义 JSON 格式配置，请使用 Json（） 构建器函数从现有实例（如默认 `Json` 对象）创建自己的 Json 类实例。通过 JsonBuilder DSL 在括号中指定参数值。生成 `Json` 的格式实例是不可变的，并且是线程安全的;它可以简单地存储在顶级属性中。

> We recommend that you store and reuse custom instances of formats for performance reasons because format implementations may cache format-specific additional information about the classes they serialize.  
> 出于性能原因，我们建议您存储和重用格式的自定义实例，因为格式实现可能会缓存有关其序列化的类的特定于格式的附加信息。

This chapter shows configuration features that [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) supports.  
本章介绍 Json 支持的配置功能。

### Pretty printing 漂亮的印刷


By default, the [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) output is a single line. You can configure it to pretty print the output (that is, add indentations and line breaks for better readability) by setting the [prettyPrint](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/pretty-print.html) property to `true`:  
默认情况下，Json 输出为单行。您可以通过将 prettyPrint 属性设置为 `true` ：

```kotlin
val format = Json { prettyPrint = true }

@Serializable
data class Project(val name: String, val language: String)

fun main() {
    val data = Project("kotlinx.serialization", "Kotlin")
    println(format.encodeToString(data))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-01.kt).  
> 您可以在此处获取完整代码。

It gives the following nice result:  
它给出了以下不错的结果：

```
{
    "name": "kotlinx.serialization",
    "language": "Kotlin"
}
```

### Lenient parsing 宽松解析

By default, [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) parser enforces various JSON restrictions to be as specification-compliant as possible (see [RFC-4627](https://www.ietf.org/rfc/rfc4627.txt)). Particularly, keys and string literals must be quoted. Those restrictions can be relaxed with the [isLenient](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/is-lenient.html) property. With `isLenient = true`, you can parse quite freely-formatted data:  
默认情况下，Json 解析器强制执行各种 JSON 限制，以尽可能符合规范（请参阅 RFC-4627）。特别是，键和字符串文字必须加引号。这些限制可以通过 isLenient 属性放宽。使用 `isLenient = true` ，您可以解析格式非常自由的数据：

```kotlin
val format = Json { isLenient = true }

enum class Status { SUPPORTED }

@Serializable
data class Project(val name: String, val status: Status, val votes: Int)

fun main() {
    val data = format.decodeFromString<Project>("""
        {
            name   : kotlinx.serialization,
            status : SUPPORTED,
            votes  : "9000"
        }
    """)
    println(data)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-02.kt).  
> 您可以在此处获取完整代码。

You get the object, even though all keys of the source JSON, string, and enum values are unquoted, while an integer is quoted:  
即使源 JSON、字符串和枚举值的所有键都不带引号，而整数则用引号引出，您也会得到该对象：

```
Project(name=kotlinx.serialization, status=SUPPORTED, votes=9000)
```

### Ignoring unknown keys 忽略未知键


JSON format is often used to read the output of third-party services or in other dynamic environments where new properties can be added during the API evolution. By default, unknown keys encountered during deserialization produce an error. You can avoid this and just ignore such keys by setting the [ignoreUnknownKeys](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/ignore-unknown-keys.html) property to `true`:  
JSON 格式通常用于读取第三方服务的输出，或者用于在 API 演进过程中可以添加新属性的其他动态环境中。默认情况下，在反序列化过程中遇到的未知键会生成错误。您可以通过将 ignoreUnknownKeys 属性设置为 `true` ：

```kotlin
val format = Json { ignoreUnknownKeys = true }

@Serializable
data class Project(val name: String)

fun main() {
    val data = format.decodeFromString<Project>("""
        {"name":"kotlinx.serialization","language":"Kotlin"}
    """)
    println(data)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-03.kt).  
> 您可以在此处获取完整代码。

It decodes the object despite the fact that the `Project` class doesn't have the `language` property:  
它对对象进行解码，尽管 `Project` 该类没有以下 `language` 属性：

```
Project(name=kotlinx.serialization)
```

### Alternative Json names 备用 Json 名称


It's not a rare case when JSON fields are renamed due to a schema version change. You can use the [`@SerialName` annotation](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md#serial-field-names) to change the name of a JSON field, but such renaming blocks the ability to decode data with the old name. To support multiple JSON names for the one Kotlin property, there is the [JsonNames](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-names/index.html) annotation:  
由于架构版本更改而重命名 JSON 字段的情况并不少见。您可以使用 `@SerialName` 注解更改 JSON 字段的名称，但此类重命名会阻止使用旧名称解码数据。为了支持一个 Kotlin 属性的多个 JSON 名称，可以使用 JsonNames 注解：

```kotlin
@Serializable
data class Project(@JsonNames("title") val name: String)

fun main() {
  val project = Json.decodeFromString<Project>("""{"name":"kotlinx.serialization"}""")
  println(project)
  val oldProject = Json.decodeFromString<Project>("""{"title":"kotlinx.coroutines"}""")
  println(oldProject)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-04.kt).  
> 您可以在此处获取完整代码。

As you can see, both `name` and `title` Json fields correspond to `name` property:  
如您所见，这两个 `name` 字段和 `title` Json 字段都对应于 `name` property：

```
Project(name=kotlinx.serialization)
Project(name=kotlinx.coroutines)
```

Support for [JsonNames](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-names/index.html) annotation is controlled by the [JsonBuilder.useAlternativeNames](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/use-alternative-names.html) flag. Unlike most of the configuration flags, this one is enabled by default and does not need attention unless you want to do some fine-tuning.  
对 JsonNames 注释的支持由 JsonBuilder.useAlternativeNames 标志控制。与大多数配置标志不同，默认情况下，此标志处于启用状态，除非您想进行一些微调，否则不需要注意。

### Coercing input values 强制输入值

JSON formats that from third parties can evolve, sometimes changing the field types. This can lead to exceptions during decoding when the actual values do not match the expected values. The default [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) implementation is strict with respect to input types as was demonstrated in the [Type safety is enforced](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md#type-safety-is-enforced) section. You can relax this restriction using the [coerceInputValues](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/coerce-input-values.html) property.  
来自第三方的 JSON 格式可以演变，有时会更改字段类型。当实际值与预期值不匹配时，这可能会导致解码过程中出现异常。默认的 Json 实现对输入类型是严格的，如强制类型安全部分所示。可以使用 coerceInputValues 属性放宽此限制。

This property only affects decoding. It treats a limited subset of invalid input values as if the corresponding property was missing and uses the default value of the corresponding property instead. The current list of supported invalid values is:  
此属性仅影响解码。它将无效输入值的有限子集视为缺少相应的属性，并改用相应属性的默认值。当前支持的无效值列表为：

- `null` inputs for non-nullable types  
    `null` 不可为 null 类型的输入
- unknown values for enums  
    枚举的未知值

> This list may be expanded in the future, so that [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) instance configured with this property becomes even more permissive to invalid value in the input, replacing them with defaults.  
> 将来可能会扩展此列表，以便使用此属性配置的 Json 实例对输入中的无效值更加宽容，将其替换为默认值。

See the example from the [Type safety is enforced](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md#type-safety-is-enforced) section:  
请参阅强制执行类型安全部分中的示例：

```kotlin
val format = Json { coerceInputValues = true }

@Serializable
data class Project(val name: String, val language: String = "Kotlin")

fun main() {
    val data = format.decodeFromString<Project>("""
        {"name":"kotlinx.serialization","language":null}
    """)
    println(data)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-05.kt).  
> 您可以在此处获取完整代码。

The invalid `null` value for the `language` property was coerced into the default value:  
`language` 该属性的无效 `null` 值被强制为默认值：

```
Project(name=kotlinx.serialization, language=Kotlin)
```

### Encoding defaults 编码默认值


Default values of properties are not encoded by default because they will be assigned to missing fields during decoding anyway. See the [Defaults are not encoded](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md#defaults-are-not-encoded-by-default) section for details and an example. This is especially useful for nullable properties with null defaults and avoids writing the corresponding null values. The default behavior can be changed by setting the [encodeDefaults](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/encode-defaults.html) property to `true`:  
默认情况下，属性的默认值不会编码，因为它们无论如何都会在解码过程中分配给缺失的字段。有关详细信息和示例，请参阅默认值未编码部分。这对于默认值为 null 的可为 null 属性特别有用，并避免写入相应的 null 值。可以通过将 encodeDefaults 属性设置为 `true` 以下值来更改默认行为：

```kotlin
val format = Json { encodeDefaults = true }

@Serializable
class Project(
    val name: String,
    val language: String = "Kotlin",
    val website: String? = null
)

fun main() {
    val data = Project("kotlinx.serialization")
    println(format.encodeToString(data))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-06.kt).  
> 您可以在此处获取完整代码。

It produces the following output which encodes all the property values including the default ones:  
它生成以下输出，该输出对所有属性值（包括默认值）进行编码：

```
{"name":"kotlinx.serialization","language":"Kotlin","website":null}
```

### Explicit nulls 显式 null


By default, all `null` values are encoded into JSON strings, but in some cases you may want to omit them. The encoding of `null` values can be controlled with the [explicitNulls](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/explicit-nulls.html) property.  
默认情况下，所有 `null` 值都编码为 JSON 字符串，但在某些情况下，您可能希望省略它们。可以使用 explicitNulls 属性控制值的 `null` 编码。

If you set property to `false`, fields with `null` values are not encoded into JSON even if the property does not have a default `null` value. When decoding such JSON, the absence of a property value is treated as `null` for nullable properties without a default value.  
如果将 property 设置为 `false` ，则即使属性没有默认 `null` 值，也不会将具有 `null` 值的字段编码为 JSON。解码此类 JSON 时，对于没有默认值的可为 null 的属性，将缺少属性值视为 `null` 缺少属性值。

```kotlin
val format = Json { explicitNulls = false }

@Serializable
data class Project(
    val name: String,
    val language: String,
    val version: String? = "1.2.2",
    val website: String?,
    val description: String? = null
)

fun main() {
    val data = Project("kotlinx.serialization", "Kotlin", null, null, null)
    val json = format.encodeToString(data)
    println(json)
    println(format.decodeFromString<Project>(json))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-07.kt).  
> 您可以在此处获取完整代码。

As you can see, `version`, `website` and `description` fields are not present in output JSON on the first line. After decoding, the missing nullable property `website` without a default values has received a `null` value, while nullable properties `version` and `description` are filled with their default values:  
如您所见， `version` 第一行的输出 JSON 中不存在 、 `website` 和 `description` 字段。解码后，缺少的没有默认值的可为 null 属性 `website` 已收到一个 `null` 值，而可为 null 的属性 `version` 则 `description` 用其默认值填充：

```
{"name":"kotlinx.serialization","language":"Kotlin"}
Project(name=kotlinx.serialization, language=Kotlin, version=1.2.2, website=null, description=null)
```

`explicitNulls` is `true` by default as it is the default behavior across different versions of the library.  
`explicitNulls` 默认情况下， `true` 因为它是库不同版本的默认行为。

### Allowing structured map keys  允许结构化映射键

JSON format does not natively support the concept of a map with structured keys. Keys in JSON objects are strings and can be used to represent only primitives or enums by default. You can enable non-standard support for structured keys with the [allowStructuredMapKeys](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/allow-structured-map-keys.html) property.  
JSON 格式本身不支持具有结构化键的映射的概念。JSON 对象中的键是字符串，默认情况下只能用于表示基元或枚举。可以使用 allowStructuredMapKeys 属性启用对结构化键的非标准支持。

This is how you can serialize a map with keys of a user-defined class:  
以下是使用用户定义类的键序列化映射的方法：

```kotlin
val format = Json { allowStructuredMapKeys = true }

@Serializable
data class Project(val name: String)

fun main() {
    val map = mapOf(
        Project("kotlinx.serialization") to "Serialization",
        Project("kotlinx.coroutines") to "Coroutines"
    )
    println(format.encodeToString(map))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-08.kt).  
> 您可以在此处获取完整代码。

The map with structured keys gets represented as JSON array with the following items: `[key1, value1, key2, value2,...]`.  
具有结构化键的映射表示为包含以下项的 JSON 数组： `[key1, value1, key2, value2,...]` .

```
[{"name":"kotlinx.serialization"},"Serialization",{"name":"kotlinx.coroutines"},"Coroutines"]
```

### Allowing special floating-point values  允许特殊的浮点值

By default, special floating-point values like [Double.NaN](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-double/-na-n.html) and infinities are not supported in JSON because the JSON specification prohibits it. You can enable their encoding using the [allowSpecialFloatingPointValues](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/allow-special-floating-point-values.html) property:  
默认情况下，JSON 不支持 Double.NaN 和无穷大等特殊浮点值，因为 JSON 规范禁止这样做。可以使用 allowSpecialFloatingPointValues 属性启用其编码：

```kotlin
val format = Json { allowSpecialFloatingPointValues = true }

@Serializable
class Data(
    val value: Double
)

fun main() {
    val data = Data(Double.NaN)
    println(format.encodeToString(data))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-09.kt).  
> 您可以在此处获取完整代码。

This example produces the following non-stardard JSON output, yet it is a widely used encoding for special values in JVM world:  
此示例生成以下非 stardard JSON 输出，但它是 JVM 世界中广泛使用的特殊值的编码：

```
{"value":NaN}
```

### Class discriminator for polymorphism  多态性的类鉴别器


A key name that specifies a type when you have a polymorphic data can be specified in the [classDiscriminator](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/class-discriminator.html) property:  
可以在 classDiscriminator 属性中指定具有多态数据时指定类型的键名称：

```kotlin
val format = Json { classDiscriminator = "#class" }

@Serializable
sealed class Project {
    abstract val name: String
}

@Serializable
@SerialName("owned")
class OwnedProject(override val name: String, val owner: String) : Project()

fun main() {
    val data: Project = OwnedProject("kotlinx.coroutines", "kotlin")
    println(format.encodeToString(data))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-10.kt).  
> 您可以在此处获取完整代码。

In combination with an explicitly specified [SerialName](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization/-serial-name/index.html) of the class it provides full control over the resulting JSON object:  
结合显式指定的类的 SerialName，它提供对生成的 JSON 对象的完全控制：

```
{"#class":"owned","name":"kotlinx.coroutines","owner":"kotlin"}
```

It is also possible to specify different class discriminators for different hierarchies. Instead of Json instance property, use [JsonClassDiscriminator](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-class-discriminator/index.html) annotation directly on base serializable class:  
也可以为不同的层次结构指定不同的类鉴别器。直接在基可序列化类上使用 JsonClassDiscriminator 注释，而不是 Json 实例属性：

```kotlin
@Serializable
@JsonClassDiscriminator("message_type")
sealed class Base
```

This annotation is _inheritable_, so all subclasses of `Base` will have the same discriminator:  
这个注解是可继承的，所以 的所有 `Base` 子类都具有相同的鉴别器：

```kotlin
@Serializable // Class discriminator is inherited from Base
sealed class ErrorClass: Base()
```

> To learn more about inheritable serial annotations, see documentation for [InheritableSerialInfo](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization/-inheritable-serial-info/index.html).  
> 若要了解有关可继承串行注解的详细信息，请参阅 InheritableSerialInfo 的文档。

Note that it is not possible to explicitly specify different class discriminators in subclasses of `Base`. Only hierarchies with empty intersections can have different discriminators.  
请注意，不可能在 的 `Base` 子类中显式指定不同的类鉴别器。只有具有空交集的层次结构才能具有不同的鉴别器。

Discriminator specified in the annotation has priority over discriminator in Json configuration:  
在 Json 配置中，注解中指定的鉴别器优先于鉴别器：

```kotlin
val format = Json { classDiscriminator = "#class" }

fun main() {
    val data = Message(BaseMessage("not found"), GenericError(404))
    println(format.encodeToString(data))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-11.kt).  
> 您可以在此处获取完整代码。

As you can see, discriminator from the `Base` class is used:  
正如你所看到的，使用了类中的 `Base` 鉴别器：

```
{"message":{"message_type":"my.app.BaseMessage","message":"not found"},"error":{"message_type":"my.app.GenericError","error_code":404}}
```

### Class discriminator output mode  类鉴别器输出方式

Class discriminator provides information for serializing and deserializing [polymorphic class hierarchies](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/polymorphism.md#sealed-classes). As shown above, it is only added for polymorphic classes by default. In case you want to encode more or less information for various third party APIs about types in the output, it is possible to control addition of the class discriminator with the [JsonBuilder.classDiscriminatorMode](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/class-discriminator-mode.html) property.  
类鉴别器提供用于序列化和反序列化多态类层次结构的信息。如上所示，默认情况下仅为多态类添加它。如果要对有关输出中类型的各种第三方 API 的更多或更少信息进行编码，则可以使用 JsonBuilder.classDiscriminatorMode 属性控制类鉴别器的添加。

For example, [ClassDiscriminatorMode.NONE](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-class-discriminator-mode/-n-o-n-e/index.html) does not add class discriminator at all, in case the receiving party is not interested in Kotlin types:  
例如，ClassDiscriminatorMode.NONE 根本不添加类鉴别器，以防接收方对 Kotlin 类型不感兴趣：

```kotlin
val format = Json { classDiscriminatorMode = ClassDiscriminatorMode.NONE }

@Serializable
sealed class Project {
    abstract val name: String
}

@Serializable
class OwnedProject(override val name: String, val owner: String) : Project()

fun main() {
    val data: Project = OwnedProject("kotlinx.coroutines", "kotlin")
    println(format.encodeToString(data))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-12.kt).  
> 您可以在此处获取完整代码。

Note that it would be impossible to deserialize this output back with kotlinx.serialization.  
请注意，不可能使用 kotlinx.serialization 反序列化此输出。

```
{"name":"kotlinx.coroutines","owner":"kotlin"}
```

Two other available values are [ClassDiscriminatorMode.POLYMORPHIC](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-class-discriminator-mode/-p-o-l-y-m-o-r-p-h-i-c/index.html) (default behavior) and [ClassDiscriminatorMode.ALL_JSON_OBJECTS](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-class-discriminator-mode/-a-l-l_-j-s-o-n_-o-b-j-e-c-t-s/index.html) (adds discriminator whenever possible). Consult their documentation for details.  
另外两个可用值是 ClassDiscriminatorMode.POLYMORPHIC（默认行为）和 ClassDiscriminatorMode.ALL_JSON_OBJECTS（尽可能添加鉴别器）。有关详细信息，请参阅其文档。

### Decoding enums in a case-insensitive manner  以不区分大小写的方式解码枚举

[Kotlin's naming policy recommends](https://kotlinlang.org/docs/coding-conventions.html#property-names) naming enum values using either uppercase underscore-separated names or upper camel case names. [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) uses exact Kotlin enum values names for decoding by default. However, sometimes third-party JSONs have such values named in lowercase or some mixed case. In this case, it is possible to decode enum values in a case-insensitive manner using [JsonBuilder.decodeEnumsCaseInsensitive](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/decode-enums-case-insensitive.html) property:  
Kotlin 的命名策略建议使用大写下划线分隔的名称或大写的驼峰大小写名称来命名枚举值。默认情况下，Json 使用精确的 Kotlin 枚举值名称进行解码。但是，有时第三方 JSON 具有以小写或混合大小写命名的此类值。在这种情况下，可以使用 JsonBuilder.decodeEnumsCaseInsensitive 属性以不区分大小写的方式解码枚举值：

```kotlin
val format = Json { decodeEnumsCaseInsensitive = true }

enum class Cases { VALUE_A, @JsonNames("Alternative") VALUE_B }

@Serializable
data class CasesList(val cases: List<Cases>)

fun main() {
  println(format.decodeFromString<CasesList>("""{"cases":["value_A", "alternative"]}""")) 
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-13.kt).  
> 您可以在此处获取完整代码。

It affects serial names as well as alternative names specified with [JsonNames](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-names/index.html) annotation, so both values are successfully decoded:  
它会影响序列化名称以及使用 JsonNames 注释指定的备用名称，因此这两个值都已成功解码：

```
CasesList(cases=[VALUE_A, VALUE_B])
```

This property does not affect encoding in any way.  
此属性不会以任何方式影响编码。

### Global naming strategy 全局命名策略

If properties' names in Json input are different from Kotlin ones, it is recommended to specify the name for each property explicitly using [`@SerialName` annotation](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md#serial-field-names). However, there are certain situations where transformation should be applied to every serial name — such as migration from other frameworks or legacy codebase. For that cases, it is possible to specify a [namingStrategy](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-builder/naming-strategy.html) for a [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) instance. `kotlinx.serialization` provides one strategy implementation out of the box, the [JsonNamingStrategy.SnakeCase](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-naming-strategy/-builtins/-snake-case.html):  
如果 Json 输入中的属性名称与 Kotlin 属性名称不同，建议使用 `@SerialName` 注释显式指定每个属性的名称。但是，在某些情况下，应将转换应用于每个序列化名称，例如从其他框架或旧代码库迁移。对于这种情况，可以为 Json 实例指定 namingStrategy。 `kotlinx.serialization` 提供了一个开箱即用的策略实现，即 JsonNamingStrategy.SnakeCase：

```kotlin
@Serializable
data class Project(val projectName: String, val projectOwner: String)

val format = Json { namingStrategy = JsonNamingStrategy.SnakeCase }

fun main() {
    val project = format.decodeFromString<Project>("""{"project_name":"kotlinx.coroutines", "project_owner":"Kotlin"}""")
    println(format.encodeToString(project.copy(projectName = "kotlinx.serialization")))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-14.kt).  
> 您可以在此处获取完整代码。

As you can see, both serialization and deserialization work as if all serial names are transformed from camel case to snake case:  
正如你所看到的，序列化和反序列化的工作方式就好像所有序列化名称都从骆驼大小写转换为蛇大小写一样：

```
{"project_name":"kotlinx.serialization","project_owner":"Kotlin"}
```

There are some caveats one should remember while dealing with a [JsonNamingStrategy](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-naming-strategy/index.html):  
在处理 JsonNamingStrategy 时，应该记住一些注意事项：

- Due to the nature of the `kotlinx.serialization` framework, naming strategy transformation is applied to all properties regardless of whether their serial name was taken from the property name or provided by [SerialName](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization/-serial-name/index.html) annotation. Effectively, it means one cannot avoid transformation by explicitly specifying the serial name. To be able to deserialize non-transformed names, [JsonNames](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-names/index.html) annotation can be used instead.  
    由于 `kotlinx.serialization` 框架的性质，命名策略转换将应用于所有属性，无论其序列化名称是从属性名称中获取还是由 SerialName 注解提供。实际上，这意味着无法通过显式指定序列化名称来避免转换。为了能够反序列化未转换的名称，可以改用 JsonNames 注释。
    
- Collision of the transformed name with any other (transformed) properties serial names or any alternative names specified with [JsonNames](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-names/index.html) will lead to a deserialization exception.  
    转换后的名称与任何其他（转换后的）属性、序列化名称或使用 JsonNames 指定的任何备用名称发生冲突将导致反序列化异常。
    
- Global naming strategies are very implicit: by looking only at the definition of the class, it is impossible to determine which names it will have in the serialized form. As a consequence, naming strategies are not friendly to actions like Find Usages/Rename in IDE, full-text search by grep, etc. For them, the original name and the transformed are two different things; changing one without the other may introduce bugs in many unexpected ways and lead to greater maintenance efforts for code with global naming strategies.  
    全局命名策略是非常隐含的：仅通过查看类的定义，无法确定它将在序列化形式中具有哪些名称。因此，命名策略对 IDE 中的 Find Usages/Rename、grep 全文搜索等操作不友好。对他们来说，原名和改造后是两回事;更改一个而不更改另一个可能会以许多意想不到的方式引入 bug，并导致对具有全局命名策略的代码进行更大的维护工作。
    

Therefore, one should carefully weigh the pros and cons before considering adding global naming strategies to an application.  
因此，在考虑向应用程序添加全局命名策略之前，应仔细权衡利弊。

## Json elements Json 元素


Aside from direct conversions between strings and JSON objects, Kotlin serialization offers APIs that allow other ways of working with JSON in the code. For example, you might need to tweak the data before it can parse or otherwise work with such an unstructured data that it does not readily fit into the typesafe world of Kotlin serialization.  
除了字符串和 JSON 对象之间的直接转换外，Kotlin 序列化还提供了允许在代码中以其他方式处理 JSON 的 API。例如，您可能需要先调整数据，然后才能解析数据或以其他方式处理此类非结构化数据，使其不容易适应 Kotlin 序列化的类型安全世界。

The main concept in this part of the library is [JsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-element/index.html). Read on to learn what you can do with it.  
库的这一部分的主要概念是 JsonElement。请继续阅读，了解您可以用它做什么。

### Parsing to Json element 解析为 Json 元素


A string can be _parsed_ into an instance of [JsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-element/index.html) with the [Json.parseToJsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/parse-to-json-element.html) function. It is called neither decoding nor deserialization because none of that happens in the process. It just parses a JSON and forms an object representing it:  
可以使用 Json.parseToJsonElement 函数将字符串分析为 JsonElement 的实例。它既不被称为解码也不称为反序列化，因为在此过程中不会发生任何情况。它只是解析一个 JSON 并形成一个表示它的对象：

```kotlin
fun main() {
    val element = Json.parseToJsonElement("""
        {"name":"kotlinx.serialization","language":"Kotlin"}
    """)
    println(element)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-15.kt).  
> 您可以在此处获取完整代码。

A `JsonElement` prints itself as a valid JSON:  
A `JsonElement` 将自身打印为有效的 JSON：

```
{"name":"kotlinx.serialization","language":"Kotlin"}
```

### Types of Json elements Json 元素的类型

A [JsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-element/index.html) class has three direct subtypes, closely following JSON grammar:  
JsonElement 类有三个直接子类型，严格遵循 JSON 语法：

- [JsonPrimitive](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-primitive/index.html) represents primitive JSON elements, such as string, number, boolean, and null. Each primitive has a simple string [content](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-primitive/content.html). There is also a [JsonPrimitive()](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-primitive.html) constructor function overloaded to accept various primitive Kotlin types and to convert them to `JsonPrimitive`.  
    JsonPrimitive 表示基元 JSON 元素，例如字符串、数字、布尔值和 null。每个基元都有一个简单的字符串内容。还有一个 JsonPrimitive（） 构造函数重载，用于接受各种原始 Kotlin 类型并将它们转换为 `JsonPrimitive` .
    
- [JsonArray](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-array/index.html) represents a JSON `[...]` array. It is a Kotlin [List](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/) of `JsonElement` items.  
    JsonArray 表示 JSON `[...]` 数组。它是 Kotlin 项目列表 `JsonElement` 。
    
- [JsonObject](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-object/index.html) represents a JSON `{...}` object. It is a Kotlin [Map](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-map/) from `String` keys to `JsonElement` values.  
    JsonObject 表示 JSON `{...}` 对象。它是从 `String` 键到 `JsonElement` 值的 Kotlin 映射。
    

The `JsonElement` class has extensions that cast it to its corresponding subtypes: [jsonPrimitive](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/json-primitive.html), [jsonArray](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/json-array.html), [jsonObject](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/json-object.html). The `JsonPrimitive` class, in turn, provides converters to Kotlin primitive types: [int](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/int.html), [intOrNull](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/int-or-null.html), [long](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/long.html), [longOrNull](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/long-or-null.html), and similar ones for other types. This is how you can use them for processing JSON whose structure you know:  
该 `JsonElement` 类具有将其转换为其相应子类型的扩展：jsonPrimitive、jsonArray、jsonObject。反过来，该 `JsonPrimitive` 类提供 Kotlin 基元类型的转换器：int、intOrNull、long、longOrNull 和其他类型的类似转换器。以下是如何使用它们来处理您知道其结构的 JSON：

```kotlin
fun main() {
    val element = Json.parseToJsonElement("""
        {
            "name": "kotlinx.serialization",
            "forks": [{"votes": 42}, {"votes": 9000}, {}]
        }
    """)
    val sum = element
        .jsonObject["forks"]!!
        .jsonArray.sumOf { it.jsonObject["votes"]?.jsonPrimitive?.int ?: 0 }
    println(sum)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-16.kt).  
> 您可以在此处获取完整代码。

The above example sums `votes` in all objects in the `forks` array, ignoring the objects that have no `votes`:  
上面的例子对 `votes` `forks` 数组中的所有对象求和，忽略没有 `votes` 的对象：

```
9042
```

Note that the execution will fail if the structure of the data is otherwise different.  
请注意，如果数据结构在其他方面不同，则执行将失败。

### Json element builders Json 元素生成器

You can construct instances of specific [JsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-element/index.html) subtypes using the respective builder functions [buildJsonArray](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/build-json-array.html) and [buildJsonObject](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/build-json-object.html). They provide a DSL to define the resulting JSON structure. It is similar to Kotlin standard library collection builders, but with a JSON-specific convenience of more type-specific overloads and inner builder functions. The following example shows all the key features:  
您可以使用相应的构建器函数 buildJsonArray 和 buildJsonObject 构造特定 JsonElement 子类型的实例。它们提供了一个 DSL 来定义生成的 JSON 结构。它类似于 Kotlin 标准库集合构建器，但具有特定于 JSON 的便利性，包括更多特定于类型的重载和内部构建器函数。以下示例显示了所有关键功能：

```kotlin
fun main() {
    val element = buildJsonObject {
        put("name", "kotlinx.serialization")
        putJsonObject("owner") {
            put("name", "kotlin")
        }
        putJsonArray("forks") {
            addJsonObject {
                put("votes", 42)
            }
            addJsonObject {
                put("votes", 9000)
            }
        }
    }
    println(element)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-17.kt).  
> 您可以在此处获取完整代码。

As a result, you get a proper JSON string:  
因此，你会得到一个正确的 JSON 字符串：

```
{"name":"kotlinx.serialization","owner":{"name":"kotlin"},"forks":[{"votes":42},{"votes":9000}]}
```

### Decoding Json elements 解码 Json 元素

An instance of the [JsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-element/index.html) class can be decoded into a serializable object using the [Json.decodeFromJsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/decode-from-json-element.html) function:  
可以使用 Json.decodeFromJsonElement 函数将 JsonElement 类的实例解码为可序列化对象：

```kotlin
@Serializable
data class Project(val name: String, val language: String)

fun main() {
    val element = buildJsonObject {
        put("name", "kotlinx.serialization")
        put("language", "Kotlin")
    }
    val data = Json.decodeFromJsonElement<Project>(element)
    println(data)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-18.kt).  
> 您可以在此处获取完整代码。

The result is exactly what you would expect:  
结果正是您所期望的：

```
Project(name=kotlinx.serialization, language=Kotlin)
```

### Encoding literal Json content (experimental)  
对文字 Json 内容进行编码（实验性）

> This functionality is experimental and requires opting-in to [the experimental Kotlinx Serialization API](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/compatibility.md#experimental-api).  
> 此功能是实验性的，需要选择加入实验性的 Kotlinx 序列化 API。

In some cases it might be necessary to encode an arbitrary unquoted value. This can be achieved with [JsonUnquotedLiteral](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-unquoted-literal.html).  
在某些情况下，可能需要对任意不带引号的值进行编码。这可以通过 JsonUnquotedLiteral 实现。

#### Serializing large decimal numbers  序列化large decimal类型数字


The JSON specification does not restrict the size or precision of numbers, however it is not possible to serialize numbers of arbitrary size or precision using [JsonPrimitive()](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-primitive.html).  
JSON 规范不限制数字的大小或精度，但是无法使用 JsonPrimitive（） 序列化任意大小或精度的数字。

If [Double](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-double/) is used, then the numbers are limited in precision, meaning that large numbers are truncated. When using Kotlin/JVM [BigDecimal](https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html) can be used instead, but [JsonPrimitive()](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-primitive.html) will encode the value as a string, not a number.  
如果使用 Double，则数字的精度有限，这意味着大数字会被截断。使用 Kotlin/JVM 时，可以改用 BigClimal，但 JsonPrimitive（） 会将值编码为字符串，而不是数字。

```kotlin
import java.math.BigDecimal

val format = Json { prettyPrint = true }

fun main() {
    val pi = BigDecimal("3.141592653589793238462643383279")
    
    val piJsonDouble = JsonPrimitive(pi.toDouble())
    val piJsonString = JsonPrimitive(pi.toString())
  
    val piObject = buildJsonObject {
        put("pi_double", piJsonDouble)
        put("pi_string", piJsonString)
    }

    println(format.encodeToString(piObject))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-19.kt).  
> 您可以在此处获取完整代码。

Even though `pi` was defined as a number with 30 decimal places, the resulting JSON does not reflect this. The [Double](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-double/) value is truncated to 15 decimal places, and the String is wrapped in quotes - which is not a JSON number.  
尽管 `pi` 被定义为小数点后 30 位的数字，但生成的 JSON 并未反映这一点。Double 值被截断到小数点后 15 位，String 用引号括起来 - 这不是 JSON 数字。

```
{
    "pi_double": 3.141592653589793,
    "pi_string": "3.141592653589793238462643383279"
}
```

To avoid precision loss, the string value of `pi` can be encoded using [JsonUnquotedLiteral](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-unquoted-literal.html).  
为了避免精度损失，可以使用 JsonUnquotedLiteral 对 的 `pi` 字符串值进行编码。

```kotlin
import java.math.BigDecimal

val format = Json { prettyPrint = true }

fun main() {
    val pi = BigDecimal("3.141592653589793238462643383279")

    // use JsonUnquotedLiteral to encode raw JSON content
    val piJsonLiteral = JsonUnquotedLiteral(pi.toString())

    val piJsonDouble = JsonPrimitive(pi.toDouble())
    val piJsonString = JsonPrimitive(pi.toString())
  
    val piObject = buildJsonObject {
        put("pi_literal", piJsonLiteral)
        put("pi_double", piJsonDouble)
        put("pi_string", piJsonString)
    }

    println(format.encodeToString(piObject))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-20.kt).  
> 您可以在此处获取完整代码。

`pi_literal` now accurately matches the value defined.  
`pi_literal` 现在与定义的值准确匹配。

```
{
    "pi_literal": 3.141592653589793238462643383279,
    "pi_double": 3.141592653589793,
    "pi_string": "3.141592653589793238462643383279"
}
```

To decode `pi` back to a [BigDecimal](https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html), the string content of the [JsonPrimitive](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-primitive/index.html) can be used.  
若要解码 `pi` 回 BigDecimal，可以使用 JsonPrimitive 的字符串内容。

(This demonstration uses a [JsonPrimitive](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-primitive/index.html) for simplicity. For a more re-usable method of handling serialization, see [Json Transformations](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/json.md#json-transformations) below.)  
（为简单起见，此演示使用 JsonPrimitive。有关处理序列化的更可重用的方法，请参阅下面的 Json 转换。

```kotlin
import java.math.BigDecimal

fun main() {
    val piObjectJson = """
          {
              "pi_literal": 3.141592653589793238462643383279
          }
      """.trimIndent()
    
    val piObject: JsonObject = Json.decodeFromString(piObjectJson)
    
    val piJsonLiteral = piObject["pi_literal"]!!.jsonPrimitive.content
    
    val pi = BigDecimal(piJsonLiteral)
    
    println(pi)
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-21.kt).  
> 您可以在此处获取完整代码。

The exact value of `pi` is decoded, with all 30 decimal places of precision that were in the source JSON.  
对 的 `pi` 确切值进行解码，并具有源 JSON 中的所有 30 位小数精度。

```
3.141592653589793238462643383279
```

#### Using `JsonUnquotedLiteral` to create a literal unquoted value of `null` is forbidden  禁止用于 `JsonUnquotedLiteral` 创建 的 `null` 文字未加引号的值

To avoid creating an inconsistent state, encoding a String equal to `"null"` is forbidden. Use [JsonNull](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-null/index.html) or [JsonPrimitive](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-primitive/index.html) instead.  
为避免创建不一致的状态，禁止对等于 的 `"null"` 字符串进行编码。请改用 JsonNull 或 JsonPrimitive。

```kotlin
fun main() {
    // caution: creating null with JsonUnquotedLiteral will cause an exception! 
    JsonUnquotedLiteral("null")
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-22.kt).  
> 您可以在此处获取完整代码。

```
Exception in thread "main" kotlinx.serialization.json.internal.JsonEncodingException: Creating a literal unquoted value of 'null' is forbidden. If you want to create JSON null literal, use JsonNull object, otherwise, use JsonPrimitive
```

## Json transformations Json 转换

To affect the shape and contents of JSON output after serialization, or adapt input to deserialization, it is possible to write a [custom serializer](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md). However, it may be inconvenient to carefully follow [Encoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-encoder/index.html) and [Decoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-decoder/index.html) calling conventions, especially for relatively small and easy tasks. For that purpose, Kotlin serialization provides an API that can reduce the burden of implementing a custom serializer to a problem of manipulating a Json elements tree.  
若要在序列化后影响 JSON 输出的形状和内容，或使输入适应反序列化，可以编写自定义序列化程序。但是，仔细遵循编码器和解码器调用约定可能会带来不便，尤其是对于相对较小的简单任务。为此，Kotlin 序列化提供了一个 API，可以将实现自定义序列化程序的负担减少到操作 Json 元素树的问题。

We recommend that you get familiar with the [Serializers](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md) chapter: among other things, it explains how custom serializers are bound to classes.  
我们建议您熟悉序列化程序一章：除其他内容外，它还解释了自定义序列化程序如何绑定到类。

Transformation capabilities are provided by the abstract [JsonTransformingSerializer](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-transforming-serializer/index.html) class which implements [KSerializer](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization/-k-serializer/index.html). Instead of direct interaction with `Encoder` or `Decoder`, this class asks you to supply transformations for JSON tree represented by the [JsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-element/index.html) class using the`transformSerialize` and `transformDeserialize` methods. Let's take a look at the examples.  
转换功能由实现 KSerializer 的抽象 JsonTransformingSerializer 类提供。此类不直接与 `Encoder` 或 `Decoder` 交互，而是要求您使用 `transformSerialize` and `transformDeserialize` 方法为 JsonElement 类表示的 JSON 树提供转换。让我们看一下这些例子。

### Array wrapping 数组包装


The first example is an implementation of JSON array wrapping for lists.  
第一个示例是列表的 JSON 数组包装的实现。

Consider a REST API that returns a JSON array of `User` objects, or a single object (not wrapped into an array) if there is only one element in the result.  
考虑返回 `User` 对象的 JSON 数组的 REST API，如果结果中只有一个元素，则返回单个对象（未包装到数组中）。

In the data model, use the [`@Serializable`](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization/-serializable/index.html) annotation to specify a custom serializer for a `users: List<User>` property.  
在数据模型中，使用 `@Serializable` 注解为 `users: List<User>` 属性指定自定义序列化程序。

```kotlin
@Serializable
data class Project(
    val name: String,
    @Serializable(with = UserListSerializer::class)
    val users: List<User>
)

@Serializable
data class User(val name: String)
```

Since this example covers only the deserialization case, you can implement `UserListSerializer` and override only the `transformDeserialize` function. The `JsonTransformingSerializer` constructor takes an original serializer as parameter (this approach is shown in the section [Constructing collection serializers](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md#constructing-collection-serializers)):  
由于此示例仅涵盖反序列化情况，因此只能实现 `UserListSerializer` 和重写该 `transformDeserialize` 函数。 `JsonTransformingSerializer` 构造函数采用原始序列化程序作为参数（此方法在构造集合序列化程序一节中显示）：

```kotlin
object UserListSerializer : JsonTransformingSerializer<List<User>>(ListSerializer(User.serializer())) {
    // If response is not an array, then it is a single object that should be wrapped into the array
    override fun transformDeserialize(element: JsonElement): JsonElement =
        if (element !is JsonArray) JsonArray(listOf(element)) else element
}
```

Now you can test the code with a JSON array or a single JSON object as inputs.  
现在，您可以使用 JSON 数组或单个 JSON 对象作为输入来测试代码。

```kotlin
fun main() {
    println(Json.decodeFromString<Project>("""
        {"name":"kotlinx.serialization","users":{"name":"kotlin"}}
    """))
    println(Json.decodeFromString<Project>("""
        {"name":"kotlinx.serialization","users":[{"name":"kotlin"},{"name":"jetbrains"}]}
    """))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-23.kt).  
> 您可以在此处获取完整代码。

The output shows that both cases are correctly deserialized into a Kotlin [List](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/).  
输出显示，这两种情况都已正确反序列化为 Kotlin List。

```
Project(name=kotlinx.serialization, users=[User(name=kotlin)])
Project(name=kotlinx.serialization, users=[User(name=kotlin), User(name=jetbrains)])
```

### Array unwrapping 数组解包


You can also implement the `transformSerialize` function to unwrap a single-element list into a single JSON object during serialization:  
您还可以实现在序列化期间将单个元素列表解包为单个 JSON 对象的 `transformSerialize` 函数：

```kotlin
    override fun transformSerialize(element: JsonElement): JsonElement {
        require(element is JsonArray) // this serializer is used only with lists
        return element.singleOrNull() ?: element
    }
```

Now, if you serialize a single-element list of objects from Kotlin:  
现在，如果您从 Kotlin 序列化对象的单元素列表：

```kotlin
fun main() {
    val data = Project("kotlinx.serialization", listOf(User("kotlin")))
    println(Json.encodeToString(data))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-24.kt).  
> 您可以在此处获取完整代码。

You end up with a single JSON object, not an array with one element:  
你最终会得到一个 JSON 对象，而不是一个包含一个元素的数组：

```
{"name":"kotlinx.serialization","users":{"name":"kotlin"}}
```

### Manipulating default values  操作默认值


Another kind of useful transformation is omitting specific values from the output JSON, for example, if it is used as default when missing or for other reasons.  
另一种有用的转换是从输出 JSON 中省略特定值，例如，如果缺少该值或出于其他原因将其用作默认值。

Imagine that you cannot specify a default value for the `language` property in the `Project` data model for some reason, but you need it omitted from the JSON when it is equal to `Kotlin` (we can all agree that Kotlin should be default anyway). You can fix it by writing the special `ProjectSerializer` based on the [Plugin-generated serializer](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md#plugin-generated-serializer) for the `Project` class.  
想象一下，由于某种原因，您无法为 `Project` 数据模型中的 `language` 属性指定默认值，但是当它等于时 `Kotlin` ，您需要从 JSON 中省略它（我们都同意 Kotlin 无论如何都应该是默认值）。您可以通过根据 Plugin 为 `Project` 类生成的序列化程序编写特殊 `ProjectSerializer` 代码来修复它。

```kotlin
@Serializable
class Project(val name: String, val language: String)

object ProjectSerializer : JsonTransformingSerializer<Project>(Project.serializer()) {
    override fun transformSerialize(element: JsonElement): JsonElement =
        // Filter out top-level key value pair with the key "language" and the value "Kotlin"
        JsonObject(element.jsonObject.filterNot {
            (k, v) -> k == "language" && v.jsonPrimitive.content == "Kotlin"
        })
}
```

In the example below, we are serializing the `Project` class at the top-level, so we explicitly pass the above `ProjectSerializer` to [Json.encodeToString](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/encode-to-string.html) function as was shown in the [Passing a serializer manually](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md#passing-a-serializer-manually) section:  
在下面的示例中，我们在顶层序列化 `Project` 该类，因此我们将上述 `ProjectSerializer` 内容显式传递给 Json.encodeToString 函数，如手动传递序列化程序部分所示：

```kotlin
fun main() {
    val data = Project("kotlinx.serialization", "Kotlin")
    println(Json.encodeToString(data)) // using plugin-generated serializer
    println(Json.encodeToString(ProjectSerializer, data)) // using custom serializer
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-25.kt).  
> 您可以在此处获取完整代码。

See the effect of the custom serializer:  
查看自定义序列化程序的效果：

```
{"name":"kotlinx.serialization","language":"Kotlin"}
{"name":"kotlinx.serialization"}
```

### Content-based polymorphic deserialization  基于内容的多态反序列化


Typically, [polymorphic serialization](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/polymorphism.md) requires a dedicated `"type"` key (also known as _class discriminator_) in the incoming JSON object to determine the actual serializer which should be used to deserialize Kotlin class.  
通常，多态序列化需要在传入的 JSON 对象中使用专用 `"type"` 键（也称为类鉴别器）来确定应用于反序列化 Kotlin 类的实际序列化程序。

However, sometimes the `type` property may not be present in the input. In this case, you need to guess the actual type by the shape of JSON, for example by the presence of a specific key.  
但是，有时该 `type` 属性可能不存在于输入中。在这种情况下，您需要通过 JSON 的形状来猜测实际类型，例如通过特定键的存在来猜测。

[JsonContentPolymorphicSerializer](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-content-polymorphic-serializer/index.html) provides a skeleton implementation for such a strategy. To use it, override its `selectDeserializer` method. Let's start with the following class hierarchy.  
JsonContentPolymorphicSerializer 为此类策略提供了框架实现。若要使用它，请重写其 `selectDeserializer` 方法。让我们从以下类层次结构开始。

> Note that is does not have to be `sealed` as recommended in the [Sealed classes](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/polymorphism.md#sealed-classes) section, because we are not going to take advantage of the plugin-generated code that automatically selects the appropriate subclass, but are going to implement this code manually.  
> 请注意，这不必 `sealed` 按照 Sealed classes 部分的建议进行，因为我们不会利用插件生成的代码来自动选择适当的子类，而是将手动实现此代码。

```kotlin
@Serializable
abstract class Project {
    abstract val name: String
}

@Serializable
data class BasicProject(override val name: String): Project()


@Serializable
data class OwnedProject(override val name: String, val owner: String) : Project()
```

You can distinguish the `BasicProject` and `OwnedProject` subclasses by the presence of the `owner` key in the JSON object.  
您可以通过 JSON 对象中 `owner` 是否存在键来区分 `BasicProject` 和 `OwnedProject` 子类。

```kotlin
object ProjectSerializer : JsonContentPolymorphicSerializer<Project>(Project::class) {
    override fun selectDeserializer(element: JsonElement) = when {
        "owner" in element.jsonObject -> OwnedProject.serializer()
        else -> BasicProject.serializer()
    }
}
```

When you use this serializer to serialize data, either [registered](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/polymorphism.md#registered-subclasses) or the default serializer is selected for the actual type at runtime:  
使用此序列化程序序列化数据时，将在运行时为实际类型选择已注册或默认序列化程序：

```kotlin
fun main() {
    val data = listOf(
        OwnedProject("kotlinx.serialization", "kotlin"),
        BasicProject("example")
    )
    val string = Json.encodeToString(ListSerializer(ProjectSerializer), data)
    println(string)
    println(Json.decodeFromString(ListSerializer(ProjectSerializer), string))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-26.kt).  
> 您可以在此处获取完整代码。

No class discriminator is added in the JSON output:  
JSON 输出中未添加类鉴别器：

```
[{"name":"kotlinx.serialization","owner":"kotlin"},{"name":"example"}]
[OwnedProject(name=kotlinx.serialization, owner=kotlin), BasicProject(name=example)]
```

### Under the hood (experimental)  （实验性）


Although abstract serializers mentioned above can cover most of the cases, it is possible to implement similar machinery manually, using only the [KSerializer](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization/-k-serializer/index.html) class. If tweaking the abstract methods `transformSerialize`/`transformDeserialize`/`selectDeserializer` is not enough, then altering `serialize`/`deserialize` is a way to go.  
尽管上面提到的抽象序列化程序可以涵盖大多数情况，但可以仅使用 KSerializer 类手动实现类似的机制。如果调整抽象方法 `transformSerialize` / `transformDeserialize` / `selectDeserializer` 还不够，那么改变 `serialize` / `deserialize` 是一条路。

Here are some useful things about custom serializers with [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html):  
以下是有关使用 Json 的自定义序列化程序的一些有用信息：

- [Encoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-encoder/index.html) can be cast to [JsonEncoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-encoder/index.html), and [Decoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-decoder/index.html) to [JsonDecoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-decoder/index.html), if the current format is [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html).  
    如果当前格式为 Json，则可以将 Encoder 转换为 JsonEncoder，将 Decoder 转换为 JsonDecoder。
- `JsonDecoder` has the [decodeJsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-decoder/decode-json-element.html) method and `JsonEncoder` has the [encodeJsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-encoder/encode-json-element.html) method, which basically retrieve an element from and insert an element to a current position in the stream.  
    `JsonDecoder` 具有 decodeJsonElement 方法和 `JsonEncoder` encodeJsonElement 方法，该方法基本上从流中检索元素并将元素插入到流中的当前位置。
- Both [`JsonDecoder`](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-decoder/json.html) and [`JsonEncoder`](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json-encoder/json.html) have the `json` property, which returns [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) instance with all settings that are currently in use.  
    两者都 `JsonDecoder` `JsonEncoder` 具有属性 `json` ，该属性返回包含当前正在使用的所有设置的 Json 实例。
- [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) has the [encodeToJsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/encode-to-json-element.html) and [decodeFromJsonElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/decode-from-json-element.html) methods.  
    Json 具有 encodeToJsonElement 和 decodeFromJsonElement 方法。

Given all that, it is possible to implement two-stage conversion `Decoder -> JsonElement -> value` or `value -> JsonElement -> Encoder`. For example, you can implement a fully custom serializer for the following `Response` class so that its `Ok` subclass is represented directly, but the `Error` subclass is represented by an object with the error message:  
鉴于所有这些，可以实现两级转换 `Decoder -> JsonElement -> value` 或 `value -> JsonElement -> Encoder` .例如，您可以为以下 `Response` 类实现完全自定义的序列化程序，以便直接表示其 `Ok` 子类，但该 `Error` 子类由带有错误消息的对象表示：

```kotlin
@Serializable(with = ResponseSerializer::class)
sealed class Response<out T> {
    data class Ok<out T>(val data: T) : Response<T>()
    data class Error(val message: String) : Response<Nothing>()
}

class ResponseSerializer<T>(private val dataSerializer: KSerializer<T>) : KSerializer<Response<T>> {
    override val descriptor: SerialDescriptor = buildSerialDescriptor("Response", PolymorphicKind.SEALED) {
        element("Ok", dataSerializer.descriptor)
        element("Error", buildClassSerialDescriptor("Error") {
          element<String>("message")
        })
    }

    override fun deserialize(decoder: Decoder): Response<T> {
        // Decoder -> JsonDecoder
        require(decoder is JsonDecoder) // this class can be decoded only by Json
        // JsonDecoder -> JsonElement
        val element = decoder.decodeJsonElement()
        // JsonElement -> value
        if (element is JsonObject && "error" in element)
            return Response.Error(element["error"]!!.jsonPrimitive.content)
        return Response.Ok(decoder.json.decodeFromJsonElement(dataSerializer, element))
    }

    override fun serialize(encoder: Encoder, value: Response<T>) {
        // Encoder -> JsonEncoder
        require(encoder is JsonEncoder) // This class can be encoded only by Json
        // value -> JsonElement
        val element = when (value) {
            is Response.Ok -> encoder.json.encodeToJsonElement(dataSerializer, value.data)
            is Response.Error -> buildJsonObject { put("error", value.message) }
        }
        // JsonElement -> JsonEncoder
        encoder.encodeJsonElement(element)
    }
}
```

Having this serializable `Response` implementation, you can take any serializable payload for its data and serialize or deserialize the corresponding responses:  
有了这个可 `Response` 序列化的实现，你可以为其数据获取任何可序列化的有效负载，并序列化或反序列化相应的响应：

```kotlin
@Serializable
data class Project(val name: String)

fun main() {
    val responses = listOf(
        Response.Ok(Project("kotlinx.serialization")),
        Response.Error("Not found")
    )
    val string = Json.encodeToString(responses)
    println(string)
    println(Json.decodeFromString<List<Response<Project>>>(string))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-27.kt).  
> 您可以在此处获取完整代码。

This gives you fine-grained control on the representation of the `Response` class in the JSON output:  
这使您可以对 JSON 输出中 `Response` 类的表示形式进行细粒度控制：

```
[{"name":"kotlinx.serialization"},{"error":"Not found"}]
[Ok(data=Project(name=kotlinx.serialization)), Error(message=Not found)]
```

### Maintaining custom JSON attributes  维护自定义 JSON 属性


A good example of custom JSON-specific serializer would be a deserializer that packs all unknown JSON properties into a dedicated field of `JsonObject` type.  
特定于 JSON 的自定义序列化程序的一个很好的示例是反序列化程序，它将所有未知的 JSON 属性打包到一个专用的 `JsonObject` 类型字段中。

Let's add `UnknownProject` – a class with the `name` property and arbitrary details flattened into the same object:  
让我们添加 `UnknownProject` 一个类， `name` 其中属性和任意细节被平展到同一个对象中：

```kotlin
data class UnknownProject(val name: String, val details: JsonObject)
```

However, the default plugin-generated serializer requires details to be a separate JSON object and that's not what we want.  
但是，默认插件生成的序列化程序要求详细信息是单独的 JSON 对象，这不是我们想要的。

To mitigate that, write an own serializer that uses the fact that it works only with the `Json` format:  
为了缓解这种情况，请编写一个自己的序列化程序，该序列化程序使用它仅适用于以下 `Json` 格式的事实：

```kotlin
object UnknownProjectSerializer : KSerializer<UnknownProject> {
    override val descriptor: SerialDescriptor = buildClassSerialDescriptor("UnknownProject") {
        element<String>("name")
        element<JsonElement>("details")
    }

    override fun deserialize(decoder: Decoder): UnknownProject {
        // Cast to JSON-specific interface
        val jsonInput = decoder as? JsonDecoder ?: error("Can be deserialized only by JSON")
        // Read the whole content as JSON
        val json = jsonInput.decodeJsonElement().jsonObject
        // Extract and remove name property
        val name = json.getValue("name").jsonPrimitive.content
        val details = json.toMutableMap()
        details.remove("name")
        return UnknownProject(name, JsonObject(details))
    }

    override fun serialize(encoder: Encoder, value: UnknownProject) {
        error("Serialization is not supported")
    }
}
```

Now it can be used to read flattened JSON details as `UnknownProject`:  
现在，它可以用来读取扁平化的JSON详细信息 `UnknownProject` ：

```kotlin
fun main() {
    println(Json.decodeFromString(UnknownProjectSerializer, """{"type":"unknown","name":"example","maintainer":"Unknown","license":"Apache 2.0"}"""))
}
```

> You can get the full code [here](https://github.com/Kotlin/kotlinx.serialization/blob/master/guide/example/example-json-28.kt).  
> 您可以在此处获取完整代码。

```
UnknownProject(name=example, details={"type":"unknown","maintainer":"Unknown","license":"Apache 2.0"})
```

---

The next chapter covers [Alternative and custom formats (experimental)](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/formats.md).  
下一章将介绍替代格式和自定义格式（实验性）。