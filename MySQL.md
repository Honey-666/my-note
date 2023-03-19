# MySQL

### 关于SQL语句的分类？

```txt

	SQL语句有很多，最好进行分门别类，这样更容易记忆。
		分为：
			DQL：
				数据查询语言（凡是带有select关键字的都是查询语句）
				select...

			DML：
				数据操作语言（凡是对表当中的数据进行增删改的都是DML）
				insert delete update
				insert 增
				delete 删
				update 改

				这个主要是操作表中的数据data。

			DDL：
				数据定义语言
				凡是带有create、drop、alter的都是DDL。
				DDL主要操作的是表的结构。不是表中的数据。
				create：新建，等同于增
				drop：删除
				alter：修改
				这个增删改和DML不同，这个主要是对表结构进行操作。

			TCL：
				不是王牌电视。
				是事务控制语言
				包括：
					事务提交：commit;
					事务回滚：rollback;

			DCL：
				是数据控制语言。
				例如：授权grant、撤销权限revoke....

```