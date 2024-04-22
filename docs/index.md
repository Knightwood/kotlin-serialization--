
## 基本用法

 1.定义可序列化类，只要类中的字段具有setter和getter，就可以序列化。例如：

```kotlin
@Serializable  
data class Test(  
	@SerialName("userName")//此注解会指定序列化/反序列化时字段的名称
	val data:String="",
	@Transient //此注解使得在序列化和反序列化时忽略此字段
	val time:String=""
)
```

!!! note
	@Transient   (不要与[kotlin.jvm.Transient]混淆)  
	使得在序列化和反序列化时忽略此字段  
	但是需要给与默认值，否则报错  
	如果json中包含了被忽略的字段，需要配置coerceInputValues为true，否则会报错
	
2.配置序列化器（可选）
```kotlin
object HttpSerializersModule {
	//指定多态结构
	 private val pagerEntityModule = SerializersModule {  
	 //定义PagerEntity有两个子类：APagerEntity和APagerEntity
		 polymorphic(PagerEntity::class) {  
			 subclass(APagerEntity::class)  
			 subclass(APagerEntity2::class)
			}  
	}
	fun get() = responseModule
}

var jsonUtil: Json = Json {  
	ignoreUnknownKeys = true  //忽略未知字段
	coerceInputValues = true  //强制默认值
	serializersModule = HttpSerializersModule.get()  //（可选）如果需要指定多态结构
}
```
3.使用上面定义的jsonUtil序列化和反序列化
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
## 泛型
在类上使用泛型，这个泛型代表的类型也需要支持可序列化  
比如写一个通用的http请求response返回类
如下示例，data class，class，都是可以的，不论在构造函数中还是类体中都行。泛型为`List<SomeEntity>`之类的，只要SomeEntity可序列化就没问题，List之类的kotlin是有默认支持的。

```
@Serializable  
data class BaseResponse3<out T>(  
    val data: T? = null,  
    var code: Int = 200,  
    var msg: String = "",  
){
	var name:String =""
}

@Serializable   
class BaseResponse3<T> (var data: T? = null， var code: Int = 200 ){  
    var msg: String = ""  
}

```

例如：
```kotlin
//序列化
val a1 = BaseResponse3(data = UserInfo(), code = 633, msg = "test msg")  
val a2 = BaseResponse3(  
    data = ApkEntity(appName = "app name1", apkUrl = "url......", appVersion = "1.0.236"),  
    code = 999,  
    msg = "test msg11111"  
)  
//打印json，结果正常
println(" a1:${a1.toJson()} \n a2: ${a2.toJson()}")  


//定义一下josn数据
val a2s =  
    "{\"data\":{\"apkName\":\"app name1\",\"apkDownloadUrl\":\"url......\",\"apkVersionName\":\"1.0.236\"},\"code\":999,\"msg\":\"test msg11111\"}"  
val a1s =  
    "{\"data\":{\"smsCode\":\"2564\",\"userName\":\"op\",\"appVersion\":\"1.56\",\"headPic\":\"kkkkkkkkkkkkkkkkkkkkk\",\"tel\":\"158496875632\",\"nickName\":\"tomjjj\",\"sex\":1},\"code\":633,\"msg\":\"test msg\"} "  

//反序列化，不会报错
jsonUtil.decodeFromString<BaseResponse3<SysUserEntity>>(a1s)  
jsonUtil.decodeFromString<BaseResponse3<ApkEntity>>(a2s)
```

## 多态和泛型
如果我有一个抽象类或是密封类，他们都有不同的实现，想令这些实现也都支持序列化和反序列化：
### 如果是父类是抽象类，夹杂着泛型和多种子类

- 序列化可以处理任意的 “开放 “类或 “抽象 “类。但是，由于这种多态性是开放的，因此有可能在源代码的任何地方，甚至在其他模块中定义了子类，因此序列化的子类列表无法在编译时确定，必须在运行时显式注册。

例如：我们有如下几个类
```
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
val responseModule2 = SerializersModule {  
    //指定OkResponse1和OkResponse2是Response的子类，且泛型为Any，如此，泛型可传任意可序列化和反序列化的类型了
    polymorphic(Response::class) {  
        subclass(OkResponse1.serializer(PolymorphicSerializer(Any::class)))  
        subclass(OkResponse2.serializer(PolymorphicSerializer(Any::class)))  
    }  
}
var jsonUtil: Json = Json {  
    ignoreUnknownKeys = true  
    coerceInputValues = true  
    serializersModule =responseModule+ responseModule2  //合并多个SerializersModule
}
```


* 测试1
```kotlin
//1. string类型  
val data = OkResponse1<String>("oooo")  
data.toJson()  
  
//2. list类型  
val s = mutableListOf<SysUserEntity>()  
s.add(SysUserEntity())  
val data2 = OkResponse1<List<SysUserEntity>>(s)  
data2.toJson()  
  
//3. 普通类型  
val data23 = OkResponse1<SysUserEntity>()  
data23.data = SysUserEntity() 
data23.toJson()  
  
println("Page: ${data.toJson()} \n ${data2.toJson()} \n ${data23.toJson()}")  
  
  
val a1 = "{\"data\":\"oooo\"}"  
val a2 =  
    "{\"data\":[{\"smsCode\":\"2564\",\"userName\":\"op\",\"appVersion\":\"1.56\",\"headPic\":\"kkkkkkkkkkkkkkkkkkkkk\",\"tel\":\"158496875632\",\"nickName\":\"tomjjj\",\"sex\":1}]}"  
val a3 =  
    "{\"data\":{\"smsCode\":\"2564\",\"userName\":\"op\",\"appVersion\":\"1.56\",\"headPic\":\"kkkkkkkkkkkkkkkkkkkkk\",\"tel\":\"158496875632\",\"nickName\":\"tomjjj\",\"sex\":1}}"  
a1.cast<OkResponse1<String>>()  
a2.cast<OkResponse1<List<SysUserEntity>>>()  
a3.cast<OkResponse1<SysUserEntity>>()
```
* 测试2：
```kotlin
val data = OkResponse2("DDD")  
data.toJson()  
val s = mutableListOf<SysUserEntity>()  
s.add(LoginPrefs.loginUserInfo)  
val data2 = OkResponse2(s)  
data2.toJson()  
val data23 = OkResponse2(SysUserEntity())  
data23.toJson()  
  
println("Page: ${data.toJson()} \n ${data2.toJson()} \n ${data23.toJson()}")
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