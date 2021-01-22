spark-shell启动中默认scala的环境
```scala
import org.apache.spark.sq.types._
import org.apache.spark.sq.Row
import org.apache.spark.rdd.EmptyRDD
import org.apache.spark.sq.DataFrame
//需要加上隐式转换的包,不然转换类型非常麻烦,总是强制校验报错
import org.apache.spark.sql.SQLContext.implicits._
//创建dataframe
var datadf=sc.parallelize(Seq(("d1","u1",10),("d1","u2",3),("d1","u3",14),("d1","u4",10),("d1","u5",10))).toDF("date","u_ph","u_ph_cnt")
//选择列
var tempdf=datadf.select(col("u_ph"))
tempdf=datadf.select("u_ph")
//zipWithUniqueId()根据分区去操作,必zipWithId要性能更好,若果必须保证全局的顺序id就需要用zipWithId了
var tempdf1=tempdf.rdd.zipWithUniqueId() //注意是rdd
var tempdf1=tempdf.rdd.zipWithUniqueId.toDF("u_ph","id")

//rdd to DF 在scala中 感觉好麻烦,不如python灵活
tempdf.rdd.distinct.map(_.getAs[String](0)).zipWithUniqueId.map(_.swap).toDF("id","node").show()

var rdd1=tempdf.rdd
var datadf2=sc.parallelize(Seq(("d1","u1",10),("d1","u7",3),("d1","u8",14),("d1","u9",10),("d1","u5",10))).toDF("date","u_ph","u_ph_cnt")
// rdd union操作,源码不带去重的参数,要去冲,只能后面加上distinct
rdd1=rdd1.union(datadf2.select("u_ph").rdd)

//这样,当报错时,随时可以查看当前实例 有哪些方法;至于查看属性没找到方便的,因为是运行的实例的属性,需要反射去做;静态属性的应该可能有方法,有空补充
spark.getClass.getMethods.mkString("\n")

//rdd map算子
sc.parallelize(1 to 10).map(x => (Map(x  -> 0), 0)).toDF().show()

//使用 to 构造数据
var rdd1=sc.parallelize(1 to 10)

var rdd1 =sc.parallelize(Seq(1,2,3,4,5,6))
var rdd2 =sc.parallelize(Seq(11,21,31,41,51,61))
var rdd3=rdd1.union(rdd2)

// toDF后 rdd 变成了 partitionRDD 然后不能再toDF了,必须经过map等算子里面,进行对应有的序列化实现操作,才能再toDF,麻烦
rdd1.toDF.rdd.map(_.getAs[Integer](0)).toDF().show

```

```

```
