# gorm



连接后返回指针 `*gorm.DB `

```go
type DB struct {
    *Config
    Error        error 		//是否报错
    RowsAffected int64    //影响的条数
    Statement    *Statement
    clone        int
}
```

放入切片指针或者结构体指针,将会返回搜索道德结果：

```
user := make([]User, 5, 5)
dbs := db.Find(&user, "password = ?", "123")
```

