```scala
import com.google.gson._
val gson = new Gson

//from_json
case class NewClass(...)
val result = gson.fromJson(jsonStr, classOf[NewClass])

//to_json
import scala.collection.JavaConverters._
val jsonStr = gson.toJson(SomeMap.asJava)

```

- toJson中，需要将Map容器转化为java的类型，否则原map中的key和value在新生成的json字符串都作为value
- [[scala混用java容器]]

