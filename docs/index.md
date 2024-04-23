
当前翻译版本，英文文档最后一次改动：[5b28d33](https://github.com/Kotlin/kotlinx.serialization/commit/5b28d33ab2d2e930db007a76b7ad91a2344c1445)

| kotlinx-serialization-json版本 | 默认使用kotlin版本  |
| ---------------------------- | ------------- |
| 1.4.1                        | 1.7.20        |
| 1.5.0                        | 1.8.10        |
| 1.5.1                        | 1.8.21        |
| 1.6.0                        | 1.9.0，1.9.10  |
| 1.6.1                        | 1.9.20，1.9.21 |
| 1.6.3                        | 1.9.22        |



## 基本用法


### 0.引入依赖
```kotlin
plugins {  
    kotlin("plugin.serialization") version "1.9.0"//根据你的kotlin版本引入插件
}

dependencies {   
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.6.0")  
}
```
如果你使用toml，可以如此引入插件  
toml 文件  
```
[versions]  
kotlin_version = "1.9.0"
[plugins]
kotlin-serialization = { id = "org.jetbrains.kotlin.plugin.serialization", version.ref = "kotlin_version" }
```
引入插件  
```
plugins {  
	alias(libs.plugins.kotlin.serialization)
}
```



### 1.定义可序列化类
要求：
	属性具有setter和getter。例如var类型，构造函数中的val等，委托属性不行
	`@Serializable` 注解要求类的主构造函数的所有参数都是属性

```kotlin
@Serializable  
class class Test(  
	@SerialName("userName")//此注解会指定序列化/反序列化时字段的名称
	val data:String="", //可被序列化
	@SerialName("code")
	var code:Int=0,//可被序列化
	@Transient //此注解使得在序列化和反序列化时忽略此字段
	val time:String=""
){
	@SerialName("code2")
	var code2:Int=0,//可被序列化
    
    // 只有getter，没有setter，不能被序列化
    val path: String 
        get() = "kotlin/$name"

    // 委托属性 --不可被序列化
    var id by ::data 
}
```

!!! note
	@Transient   (不要与[kotlin.jvm.Transient]混淆)  
	使得在序列化和反序列化时忽略此字段  
	但是需要给与默认值，否则报错  
	如果json中包含了被忽略的字段，需要配置coerceInputValues为true，否则会报错
	
### 2.配置序列化器（可选）

更多配置内容查看“配置Json序列化器功能” 这一章

```kotlin
object HttpSerializersModule {
	var jsonUtil: Json = Json {  
		ignoreUnknownKeys = true  //忽略未知字段
		coerceInputValues = true  //强制默认值
	}
}
```
### 3.使用上面定义的jsonUtil序列化和反序列化

如果不定用jsonUtil,可以直接使用`Json`这个默认默认对象

```
//反序列化
jsonUtil.decodeFromString<T>("")
//序列化
jsonUtil.encodeToString(data)
//生成json对象
jsonUtil.encodeToJsonElement(data)
//手动构建json对象
val jsonBody = buildJsonObject {  
	put("id", 1)  
	put("name", "hello")  
	put("remark", "remark")  
}


//例如使用默认的Json进行序列化和反序列化
Json.decodeFromString<T>("")
```

---
定义一下下面示例中会用到的快捷方法
```kotlin
inline fun <reified T> T.toJsonElement(json: Json = HttpSerializersModule.jsonUtil): JsonElement {  
    return json.encodeToJsonElement(this)  
}  
  
inline fun <reified T> T.toJson(json: Json = HttpSerializersModule.jsonUtil): String {  
    return json.encodeToString(this)  
}  
  
inline fun <reified T> String.cast(json: Json = HttpSerializersModule.jsonUtil): T {  
    return json.decodeFromString<T>(this)  
}
```

## 泛型
在类上使用泛型，这个泛型代表的类型也需要支持可序列化  
泛型为`List<SomeEntity>`之类的，只要SomeEntity可序列化就没问题，List之类的kotlin是有默认支持的（内置类这一章介绍了一些默认支持的可序列化类）。

比如写一个通用的http请求response返回类  
示例：

```
@Serializable  
data class BaseResponse3<out T>(  
    val data: T? = null,    
    var msg: String = "",  
){
	var name:String =""
}

```

测试代码：
```kotlin
val a1 = BaseResponse3(data = testUserInfo, msg = "test msg")  
val a2 = BaseResponse3(  
    data = BookInfo(bookName = "hello world", url = "url......", authorName = "张三"),  
    msg = "test msg11111"  
)
//转为为json字符串并进行打印
println(" a1:${a1.toJson()} \n a2: ${a2.toJson()}")  

//定义json字符串
val a1s ="{\"data\":{\"userName\":\"op\",\"id\":1,\"tel\":\"158496875632\",\"nickName\":\"tomjjj\",\"sex\":1},\"msg\":\"test msg\"}"  
val a2s="{\"data\":{\"bookName\":\"hello world\",\"url\":\"url......\",\"authorName\":\"张三\"},\"msg\":\"test msg11111\"}"  //反序列化
a1s.cast<BaseResponse3<UserInfo>>()  
a2s.cast<BaseResponse3<BookInfo>>()
```

## 多态和泛型
如果我有一个抽象类或是密封类，他们都有不同的实现，想令这些实现也都支持序列化和反序列化：
### 如果是父类是抽象类，夹杂着泛型和多种子类

- **序列化可以处理任意的`open class`或 `abstract class`。但是，由于这种多态性是开放的，有可能被放在源代码的任何地方，甚至在其他模块中定义子类，因此序列化的子类列表无法在编译时确定，必须在运行时显式注册。**

例如：我们有如下几个类
```kotlin
@Serializable  
abstract class Response<out T>  
  
@Serializable  
@SerialName("OkResponse1")  
open class OkResponse1<T>(var data: T? = null) : Response<T>(){  
    var code: Int = 200  
    var rtncode: Int = 200  
    var msg: String = ""  
}
  
@Serializable  
@SerialName("OkResponse2")  
data class OkResponse2<out T>(  
    val data: T? = null,  
    var rtncode: Int = 200,  
    var msg: String = "",  
) : Response<T>()  
  
```
!!! note
	OkResponse1和OkResponse2都是Response的子类，而且都含有泛型。  
	OkResponse1是个open class，构造函数包含一个字段，其余在函数体中  
	OkResponse2则是一个data class，字段都在构造函数中  

OkResponse1和OkResponse2都是Response的子类，需要在序列化模块中注册：

多个SerializersModule可以直接用`+`合并

```kotlin
//可选，如果需要
object HttpSerializersModule {
	//指定多态结构
	private val pagerEntityModule = SerializersModule {  
		//定义PagerEntity有两个子类：APagerEntity和APagerEntity
		polymorphic(PagerEntity::class) {  
			subclass(APagerEntity::class)  
			subclass(APagerEntity2::class)
		}
	}
	
	private val responseModule2 = SerializersModule {  
		//指定OkResponse1和OkResponse2是Response的子类，且泛型为Any，如此，泛型可传任意可序列化和反序列化的类型了
	    polymorphic(Response::class) {  
			subclass(OkResponse1.serializer(PolymorphicSerializer(Any::class)))  
	        subclass(OkResponse2.serializer(PolymorphicSerializer(Any::class)))  
	    }  
	}

	fun get() = responseModule+pagerEntityModule //合并多个SerializersModule
}

var jsonUtil: Json = Json {  
    ignoreUnknownKeys = true  
    coerceInputValues = true  
    serializersModule =  HttpSerializersModule.get()
}
```


* 测试OkResponse1
```kotlin
//1. string类型  
val data = OkResponse1<String>("oooo")  
  
//2. list类型  
val s = mutableListOf<UserInfo>()  
s.add(testUserInfo)  
val data2 = OkResponse1<List<UserInfo>>(s)  
  
//3. 普通类型  
val data3 = OkResponse1<UserInfo>()  
data3.data = testUserInfo  
  
println("JSON: ${data.toJson()} \n ${data2.toJson()} \n ${data3.toJson()}")  
  
  
val a1 = "{\"data\":\"oooo\"}"  
val a2 ="{\"data\":[{\"userName\":\"op\",\"id\":1,\"tel\":\"158496875632\",\"nickName\":\"tomjjj\",\"sex\":1}]}"  
val a3 ="{\"data\":{\"userName\":\"op\",\"id\":1,\"tel\":\"158496875632\",\"nickName\":\"tomjjj\",\"sex\":1}}" 


a1.cast<OkResponse1<String>>()  
a2.cast<OkResponse1<List<UserInfo>>>()  
a3.cast<OkResponse1<UserInfo>>()
```
* 测试OkResponse2：
```kotlin
val data = OkResponse2("DDD")  
  
val s = mutableListOf<UserInfo>()  
s.add(testUserInfo)  
val data2 = OkResponse2(s)  
  
val data3 = OkResponse2(testUserInfo)  
  
println("Page: ${data.toJson()} \n ${data2.toJson()} \n ${data3.toJson()}")
```

### 如果是密封类
密封类的所有子类都必须显式标记为 `@Serializable` 。

```kotlin
@Serializable
sealed class Project {
    abstract val name: String,
    var status = "open",
}

@Serializable
class OwnedProject(override val name: String, val owner: String) : Project()

fun main() {
    val data: Project = OwnedProject("kotlinx.coroutines", "kotlin")
    println(Json.encodeToString(data)) // Serializing data of compile-time type Project
}
```

## 序列化第三方类

如果可序列化类中包含无法添加@Serializable的类的属性  
例如Date这个第三方类，
有两种方式让它支持序列化和反序列化
### 首先定义这个类如何序列化和反序列化
例如：使用如下代码，告知序列化器，将date类的实例序列化时转换为Long，反序列化时，如何生成Date实例
```
object DateAsLongSerializer : KSerializer<Date> {
    override val descriptor: SerialDescriptor = PrimitiveSerialDescriptor("Date", PrimitiveKind.LONG)
    override fun serialize(encoder: Encoder, value: Date) = encoder.encodeLong(value.time)
    override fun deserialize(decoder: Decoder): Date = Date(decoder.decodeLong())
}
```
### 方式1：手动传递

使用encodeToString序列化时，第一个参数将上面定义的DateAsLongSerializer传入即可

```kotlin hl_lines="5"
fun main() {                                              
    val kotlin10ReleaseDate = SimpleDateFormat("yyyy-MM-ddX").parse("2016-02-15+00") 
    println(
	    Json.encodeToString(
			    DateAsLongSerializer,
			    kotlin10ReleaseDate
			    )
		)    
}
```

### 方式2：在属性上指定序列化程序

如果这个date是某个可序列化类的属性，可以使用下面代码，将DateAsLongSerializer指定给date字段

```kotlin hl_lines="4"
@Serializable          
class ProgrammingLanguage(
    val name: String,
    @Serializable(with = DateAsLongSerializer::class)
    val stableReleaseDate: Date
)

fun main() {
    val data = ProgrammingLanguage("Kotlin", SimpleDateFormat("yyyy-MM-ddX").parse("2016-02-15+00"))
    println(Json.encodeToString(data))
}
```

##  备用 Json 名称

上面说到`@SerialName` 注解可以更改 JSON 字段的名称，但它会阻止使用旧名称解码数据。
为了支持一个 Kotlin 属性的多个 JSON 名称，可以使用 JsonNames 注解
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
这两个 `name` 字段和 `title` Json 字段都对应于 `name` property：
```kotlin
Project(name=kotlinx.serialization)
Project(name=kotlinx.coroutines)
```