# 完整性约束
```python
# 以学生表为例
class Student(db.Model):
    # 自增 ID
    Sno = db.Column(db.CHAR(8), primary_key=True, UNIQUE = True)
    Sname = db.Column(db.String(50), nullable=False)
    Ssex = db.Column(db.String(2),default='未知')
    Sbirth = db.Column(db.DATE)
    Sdept = db.Column(db.String(50))
    
# NOT NULL 也就是限制列取值为非空 -> nullable = False\True
# DEFAULT 也就是指定列的默认值 -> DEFAULT = "值"
# UNIQUE 指定列取值不能重复 -> unique = True\False
# PRIMARY KEY 指定本列为主码 -> primary_key=True
# FOREIGN KEY 定义本列
#为引用其他表的外码->foreign_key=True
```

^10d9d1

# [创建索引,联合索引，唯一性联合索引]()

```python
class Student(db.Model):
    # 自增 ID
    Sno = db.Column(db.CHAR(8), primary_key=True, UNIQUE = True, index=True)
    #创建索引只需要添加index=True即可
 __table_args__ = (
	db.Index('ix_user_post_user_id_insert_time', 'user_id', 'insert_time'),
    )#创建联合索引，需要在__table_args__对象里定义db.Index(),ix_user_post_user_id_insert_time是索引的名称
 __table_args__ = (
    db.UniqueConstraint('user_id', 'post_id', name='uix_user_post_user_id_post_id'),
        #唯一性联合索引需要添加UniqueConstraint对象，使得user_id和post_id是唯一的
	db.Index('ix_user_post_user_id_insert_time', 'user_id', 'insert_time'),
    )
```
# 测试用例

