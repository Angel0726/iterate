---
title: Flask 库 Migrate
toc: true
date: 2018-06-22 21:59:05
---
## 可以补充进来的
- 对于这个 Flask 的 Migrate 还是有一些问题的。比如 列的名称的变更，这个就做不到


# ORIGIN
真正的使用了数据库之后，才感觉数据库的维护的确不是那么简单的，就比如数据库的升级，表中的列添加了；数据的迁移，从 sqlite 迁移到 Mysql；数据的备份，将 sqlite 或 mysql 的数据进行备份，备份到本地或者备份到 dropbox ；还有数据的防护 等等。


```Python
from app import app, db
from flask_script import Manager, Shell
from flask_migrate import Migrate, MigrateCommand  # 载入 migrate 扩展
from app.models import User# 如果缺少这句，manage.py 可能检测不到 models

manager = Manager(app)
migrate = Migrate(app, db)  # 注册 migrate 到 flask

manager.add_command('db', MigrateCommand)  # 在终端环境下添加一个 db 命令

if __name__ == '__main__':
    manager.run()
```

需要使用的指令：

- Python manager.py db init
- Python manager.py db migrate -m "initial migration"
- Python manager.py db upgrade


输入 Python manager.py db init 来创建迁移仓库,
输入 Python manager.py db migrate -m "initial migration"来创建迁移脚本, 在数据库结构有变动后创建迁移脚本
输入 Python manager.py db upgrade 来更新数据库




# 相关
  1. [Flask学习记录之 Flask-Migrate](https://www.cnblogs.com/agmcs/p/4448094.html)
  2. [Flask-SQLAlchemy 和 Flask-Migrate 使用](https://liuliqiang.info/post/flask-sqlalchemy-and-migrate/%0A)
  3. [Flask-Migrate的使用](https://wing324.github.io/2017/02/26/Flask-Migrate%E7%9A%84%E4%BD%BF%E7%94%A8/)
