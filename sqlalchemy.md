## sqlalchemy的orm方式如何指定id自增还是hash
#### 设置id为自增
```python
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy.dialects.mysql import CHAR
from flask import Flask
import uuid


app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] ='mysql+pymysql://root:123456@localhost/todo'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)
# 以学生表为例
class student(db.Model):
    # 自增 ID
    id = db.Column(db.Integer,
                    primary_key=True,
                    autoincrement=True)

    name = db.Column(db.String(50), nullable=False)

    age = db.Column(db.Integer, nullable=False)

class student_hash(db.Model):
    # 使用哈希值作为主键
    id = db.Column(CHAR(32),
			primary_key=True,
			default=lambda: str(uuid.uuid4().hex))

    name = db.Column(db.String(50), nullable=False)

    age = db.Column(db.Integer, nullable=False)

with app.app_context():
    db.create_all()

```
# flask-sqlalchemy常用字段类型
![[Snipaste_2024-09-09_10-06-12.png](file:///C%3A%5CUsers%5C34159%5CPictures%5CSnipaste_2024-09-09_10-06-12.png)
![Snipaste_2024-09-09_10-18-25.png](file:///C%3A%5CUsers%5C34159%5CPictures%5CSnipaste_2024-09-09_10-18-25.png)
![[Pasted image 20240909101851.png]]
[[DataBase MYSQL#[创建索引,联合索引，唯一性联合索引]]]
# flask_sqlalchemy对数据库表记录的增删改查
以学生表为例
![[DataBase MYSQL#^10d9d1]]
```python
from models import db, Student, app, Course, StudentCourse
from flask_sqlalchemy import SQLAlchemy

# 增加
student = Student(Sno='12345678', Sname='小明', Ssex='男', Sbirth='1990-01-01', Sdept='Computer Science')# 设置表记录数据
db.session.add(student)# 插入记录
db.commit()# 提交

# 删除
# 方案1： 
goods = Goods.query.filter(Goods.name == "小明").first() # query查询 查询到名称为小明的第一个记录
db.session.delete(goods)# 删除查询到的记录
db.session.commit() # 提交
# 方案2： 
Goods.query.filter(Goods.name == "小丽").delete()
# 删除字段name值为小丽的记录
db.session.commit()# 提交

# 修改（更新）
# 方案1：先查询模型对象，修改对象属性，再提交到数据库
goods = Goods.query.filter(Goods.name == "小丽").first()
goods.Ssex = "男性" # 把小丽的性别改为男性
db.session.add(goods) # 可以省略这步 
db.session.commit() # 提交
# 方案2（推荐）： 
Goods.query.filter(Goods.name == "下午茶").update({"Ssex": "男性"})# 直接修改，更加简洁
db.session.commit() # 提交

# 查询
def query(): # 简单查询 
User.query.all() # 查询所有用户数据 
User.query.count() # 查询有多少个用户 
User.query.first() # 查询第1个用户 

# 根据id查询 返回模型对象/None 
User.query.get(5)
User.query.filter_by(id=5).all() 
User.query.filter(User.id == 5).first() 

# 查询名字以某个字符开始/结尾/包含的所有用户 
User.query.filter(User.name.endswith("g")).all() User.query.filter(User.name.startswith("w")).all() User.query.filter(User.name.contains("n")).all() User.query.filter(User.name.like("w%n%g")).all() # 模糊查询 

# 查询名字不等于wang的所有用户 
from sqlalchemy import not_, and_, or_ User.query.filter(not_(User.name == "wang")).all() User.query.filter(User.name != "wang").all() 

User.query.filter(User.id.in_([1, 5, 6])).all() 
# 查询id在某个范围的用户 

# 排序查询
User.query.order_by(User.age, User.id.desc()).limit(5).all() # 所有用户先按年龄从小到大, 再按id从大到小排序, 取前5个 

# 分页查询 
pn = User.query.paginate(3, 3)
# pn.pages 总页数 pn.page 当前页码 pn.items 当前页的数据 pn.total 总条数

# 去重 
db.query(distinct(User.name)).all() db.query(User.name,User.age).distinct(User.name, User.age).filter(xxx).all() 

# 聚合查询 
# 查询每个年龄的人数 select age, count(name) from t_user group by age 分组聚合 
from sqlalchemy import func 
data = db.session.query(User.age, func.count(User.id).label("count")).group_by(User.age).all() 
for item in data:
# print(item[0], item[1]) 
print(item.age, item.count)

# 只查询所有人的姓名和邮箱 优化查询 User.query.all() # 相当于select * 
from sqlalchemy.orm import load_only 
data = User.query.options(load_only(User.name, User.email)).all() 

#另一种写法： data =session.query(User).with_entities(User.id).filter(xxx).all() 

for item in data:
	print(item.name, item.email) 

data=db.session.query(User.name,User.email).all() 

for item in data:
	print(item.name, item.email)
```
