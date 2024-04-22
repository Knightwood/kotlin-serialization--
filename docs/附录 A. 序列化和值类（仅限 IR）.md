# Serialization and value classes (IR-specific)  
序列化和值类（特定于 IR）

[](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/value-classes.md#serialization-and-value-classes-ir-specific)

This appendix describes how value classes are handled by kotlinx.serialization.  
本附录描述了 kotlinx.serialization 如何处理值类。

> Features described are available on JVM/IR (enabled by default), JS/IR and Native backends.  
> 所描述的功能在 JVM/IR（默认启用）、JS/IR 和本机后端上可用。

**Table of contents 目录**

- [Serializable value classes  
    可序列化值类](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/value-classes.md#serializable-value-classes)
- [Unsigned types support (JSON only)  
    无符号类型支持（仅限 JSON）](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/value-classes.md#unsigned-types-support-json-only)
- [Using value classes in your custom serializers  
    在自定义序列化程序中使用值类](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/value-classes.md#using-value-classes-in-your-custom-serializers)

## Serializable value classes  
可序列化值类

[](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/value-classes.md#serializable-value-classes)

We can mark value class as serializable:  
我们可以将值类标记为可序列化：

```kotlin
@Serializable
@JvmInline
value class Color(val rgb: Int)
```

Value class in Kotlin is stored as its underlying type when possible (i.e. no boxing is required). Serialization framework does not impose any additional restrictions and uses the underlying type where possible as well.  
在可能的情况下，Kotlin 中的值类存储为其基础类型（即不需要装箱）。序列化框架不施加任何额外的限制，并且尽可能使用基础类型。

```kotlin
@Serializable
data class NamedColor(val color: Color, val name: String)

fun main() {
  println(Json.encodeToString(NamedColor(Color(0), "black")))
}
```

In this example, `NamedColor` is serialized as two primitives: `color: Int` and `name: String` without an allocation of `Color` class. When we run the example, encoding data with JSON format, we get the following output:  
在此示例中， `NamedColor` 被序列化为两个基元： `color: Int` 并且 `name: String` 没有 `Color` 类的分配。当我们运行示例时，使用 JSON 格式对数据进行编码，我们得到以下输出：

```
{"color": 0, "name": "black"}
```

As we see, `Color` class is not included during the encoding, only its underlying data. This invariant holds even if the actual value class is [allocated](https://kotlinlang.org/docs/reference/inline-classes.html#representation) — for example, when value class is used as a generic type argument:  
正如我们所看到的， `Color` 在编码过程中不包括类，只包含其基础数据。即使分配了实际的值类，此不变量也成立，例如，当值类用作泛型类型参数时：

```kotlin
@Serializable
class Palette(val colors: List<Color>)

fun main() {
  println(Json.encodeToString(Palette(listOf(Color(0), Color(255), Color(128)))))
}
```

The snippet produces the following output:  
代码段生成以下输出：

```
{"colors":[0, 255, 128]}
```

## Unsigned types support (JSON only)  
无符号类型支持（仅限 JSON）

[](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/value-classes.md#unsigned-types-support-json-only)

Kotlin standard library provides ready-to-use unsigned arithmetics, leveraging value classes to represent unsigned types: `UByte`, `UShort`, `UInt` and `ULong`. [Json](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-json/kotlinx.serialization.json/-json/index.html) format has built-in support for them: these types are serialized as theirs string representations in unsigned form. These types are handled as regular serializable types by the compiler plugin and can be freely used in serializable classes:  
Kotlin 标准库提供现成的无符号算术，利用值类来表示无符号类型： `UByte` 、 `UShort` 和 `UInt` `ULong` 。Json 格式对它们有内置的支持：这些类型以无符号形式序列化为它们的字符串表示形式。这些类型由编译器插件作为常规的可序列化类型进行处理，并且可以在可序列化类中自由使用：

```kotlin
@Serializable
class Counter(val counted: UByte, val description: String)

fun main() {
    val counted = 239.toUByte()
    println(Json.encodeToString(Counter(counted, "tries")))
}
```

The output is following:  
输出如下：

```
{"counted":239,"description":"tries"}
```

> Unsigned types are currently supported only in JSON format. Other formats such as ProtoBuf and CBOR use built-in serializers that use an underlying signed representation for unsigned types.  
> 目前仅支持 JSON 格式的无符号类型。其他格式（如 ProtoBuf 和 CBOR）使用内置序列化程序，这些序列化程序对无符号类型使用基础有符号表示形式。

## Using value classes in your custom serializers  
在自定义序列化程序中使用值类

[](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/value-classes.md#using-value-classes-in-your-custom-serializers)

Let's return to our `NamedColor` example and try to write a custom serializer for it. Normally, as shown in [Hand-written composite serializer](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md#hand-written-composite-serializer), we would write the following code in `serialize` method:  
让我们回到我们的 `NamedColor` 示例，并尝试为它编写一个自定义序列化程序。通常，如手写复合序列化器中所示，我们会在方法中 `serialize` 编写以下代码：

```kotlin
override fun serialize(encoder: Encoder, value: NamedColor) {
  encoder.encodeStructure(descriptor) {
    encodeSerializableElement(descriptor, 0, Color.serializer(), value.color)
    encodeStringElement(descriptor, 1, value.name)
  }
}
```

However, since `Color` is used as a type argument in [encodeSerializableElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-composite-encoder/encode-serializable-element.html) function, `value.color` will be boxed to `Color` wrapper before passing it to the function, preventing the value class optimization. To avoid this, we can use special [encodeInlineElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-composite-encoder/encode-inline-element.html) function instead. It uses [serial descriptor](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.descriptors/-serial-descriptor/index.html) of `Color` ([retrieved](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.descriptors/-serial-descriptor/get-element-descriptor.html) from serial descriptor of `NamedColor`) instead of [KSerializer](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization/-k-serializer/index.html), does not have type parameters and does not accept any values. Instead, it returns [Encoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-encoder/index.html). Using it, we can encode unboxed value:  
但是，由于 `Color` 在 encodeSerializableElement 函数中用作类型参数， `value.color` 因此在将其传递给函数之前将被装箱到 `Color` 包装器，从而阻止值类优化。为了避免这种情况，我们可以改用特殊的 encodeInlineElement 函数。它使用 （ `Color` 从 `NamedColor` 的 的 串行描述符 检索 ）而不是 KSerializer 的串行描述符，没有类型参数，也不接受任何值。相反，它返回 Encoder。使用它，我们可以对未装箱的值进行编码：

```kotlin
override fun serialize(encoder: Encoder, value: NamedColor) {
  encoder.encodeStructure(descriptor) {
    encodeInlineElement(descriptor, 0).encodeInt(value.color)
    encodeStringElement(descriptor, 1, value.name)
  }
}
```

The same principle goes also with [CompositeDecoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-composite-decoder/index.html): it has [decodeInlineElement](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-composite-decoder/decode-inline-element.html) function that returns [Decoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-decoder/index.html).  
CompositeDecoder 也有同样的原理：它具有返回 Decoder 的 decodeInlineElement 函数。

If your class should be represented as a primitive (as shown in [Primitive serializer](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md#primitive-serializer) section), and you cannot use [encodeStructure](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/encode-structure.html) function, there is a complementary function in [Encoder](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-encoder/index.html) called [encodeInline](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-encoder/encode-inline.html). We will use it to show an example how one can represent a class as an unsigned integer.  
如果类应表示为基元（如基元序列化程序部分所示），并且不能使用 encodeStructure 函数，则 Encoder 中有一个名为 encodeInline 的补充函数。我们将用它来展示一个示例，如何将类表示为无符号整数。

Let's start with a UID class:  
让我们从一个 UID 类开始：

```kotlin
@Serializable(UIDSerializer::class)
class UID(val uid: Int)
```

`uid` type is `Int`, but suppose we want it to be an unsigned integer in JSON. We can start writing the following custom serializer:  
`uid` type 是 `Int` ，但假设我们希望它是 JSON 中的无符号整数。我们可以开始编写以下自定义序列化程序：

```kotlin
object UIDSerializer: KSerializer<UID> {
  override val descriptor = UInt.serializer().descriptor
}
```

Note that we are using here descriptor from `UInt.serializer()` — it means that the class' representation looks like a UInt's one.  
请注意，我们在这里使用的描述符 from `UInt.serializer()` — 这意味着类的表示形式看起来像 UInt 的表示形式。

Then the `serialize` method:  
然后方法 `serialize` ：

```kotlin
override fun serialize(encoder: Encoder, value: UID) {
  encoder.encodeInline(descriptor).encodeInt(value.uid)
}
```

That's where the magic happens — despite we called a regular [encodeInt](https://kotlinlang.org/api/kotlinx.serialization/kotlinx-serialization-core/kotlinx.serialization.encoding/-encoder/encode-int.html) with a `uid: Int` argument, the output will contain an unsigned int because of the special encoder from `encodeInline` function. Since JSON format supports unsigned integers, it recognizes theirs descriptors when they're passed into `encodeInline` and handles consecutive calls as for unsigned integers.  
这就是神奇的地方——尽管我们用 `uid: Int` 参数调用了常规 encodeInt，但由于 `encodeInline` 函数的特殊编码器，输出将包含一个无符号的 int。由于 JSON 格式支持无符号整数，因此当它们被传入 `encodeInline` 时，它会识别它们的描述符，并像处理无符号整数一样处理连续调用。

The `deserialize` method looks symmetrically:  
该 `deserialize` 方法看起来对称：

```kotlin
override fun deserialize(decoder: Decoder): UID {
  return UID(decoder.decodeInline(descriptor).decodeInt())
}
```

> Disclaimer: You can also write such a serializer for value class itself (imagine UID being the value class — there's no need to change anything in the serializer). However, do not use anything in custom serializers for value classes besides `encodeInline`. As we discussed, calls to value class serializer may be optimized and replaced with a `encodeInlineElement` calls. `encodeInline` and `encodeInlineElement` calls with the same descriptor are considered equivalent and can be replaced with each other — formats should return the same `Encoder`. If you embed custom logic in custom value class serializer, you may get different results depending on whether this serializer was called at all (and this, in turn, depends on whether value class was boxed or not).  
> 免责声明：您也可以为值类本身编写这样的序列化程序（假设 UID 是值类 - 无需更改序列化程序中的任何内容）。但是，除了 `encodeInline` 之外，不要在自定义序列化程序中对值类使用任何内容。正如我们所讨论的，对值类序列化程序的调用可能会被优化并替换为调用 `encodeInlineElement` 。 `encodeInline` 具有相同描述符的 `encodeInlineElement` 调用被认为是等效的，可以相互替换 — 格式应返回相同的 `Encoder` 。如果在自定义值类序列化程序中嵌入自定义逻辑，则可能会得到不同的结果，具体取决于是否调用了此序列化程序（而这反过来又取决于值类是否装箱）。****