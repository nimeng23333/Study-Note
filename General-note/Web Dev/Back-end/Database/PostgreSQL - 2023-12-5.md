
```SQL
CREAT TABLE xxx (
id SERIAL PRIMARY KEY,  //SERIAL 表示序列化，不需要指定id，可以自动增长
name VARCHAR(50),  //最长50个字符，存储空间会根据实际大小分配，CHAR(50)则会固定分配50个字符的空间,TEXT可以根据实际情况匹配大小但不可以限制最大字符数
age INT
)
```

数据类型汇总
[PostgreSQL: Documentation: 16: Chapter 8. Data Types](https://www.postgresql.org/docs/current/datatype.html)

.csv 后缀  comma seperated values 逗号分割的值

在服务器端连接数据库服务器
```JS
import pg from "pg";

const db = new pg.Client({
	user: "postgres",
	host: "localhost",
	database: "world", //database的名字
	password: "1006rebecca",
	port: 5432,
});

db.connect();
db.query("SELECT * FROM capitals", (err, res) =>{
	if (err){
		console.error("Error executing query", err.stack);
	}else{
		quiz = res.rows;
	}
	db.end(); //注意！！这个要写在query里面
})
```

![[Pasted image 20231206172921.png]]

在js代码中pg提供$n作为占位符
```js
await db.query("INSERT INTO visited_countries(country_code) VALUES ($1, $2, $3)" ,[value1, value2, value3]);
```