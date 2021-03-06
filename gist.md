# gist 记录一些代码片段


## 连接mysql批量插入、查询

```python
import pymysql
import time
def db():
    conn = pymysql.connect(user='root',passwd='123456',host='localhost',port=3306,db='dbname',charset='utf8',init_command="set NAMES utf8mb4", use_unicode=True)
    conn.encoding = "utf8"
    return conn
conn=db()
cur = conn.cursor()
# 批量插入 将list转为一条sql语句执行insert
sql = "insert into TABLENAME values"
for item in ...:
    sql += "('"+"','".join([pymysql.escape_string(str(i)) for i in item])+"'),"
sql = sql[:-1]
try:
    cur.execute(sql)
except:
    print("Error SQL: "+sql)
    time.sleep(1) # 显示出错的SQL语句后稍作等待，也方便Ctrl+C
conn.commit()

# 查一条，返回一个dict
id = ... #id为主键 只会有一条记录
cur.execute("select * from tablename where id="+str(id))
return dict(zip(("id","flag","更多的列名"),list(cur)[0]))
```

## flask设置一堆静态目录

```python
from flask import Flask, render_template, Blueprint, request, redirect, Markup
app = Flask(__name__)
for path in ['pic', 'skin', 'images', '更多静态目录']:
    blueprint = Blueprint(path, __name__, static_url_path='/'+path, static_folder=path)
    app.register_blueprint(blueprint)
```

## 大小写不敏感字典

```python
from requests.structures import CaseInsensitiveDict
mydict = CaseInsensitiveDict(mydict)
```

## print的时候顺带带上时间

使用这种方式的好处不仅是显示时间，而且可以很方便地将往屏幕输出改为写文件；更优雅的方式是用logging

```python
import time
myprint = lambda s: print("[{showtime}] {s}".format(showtime=time.strftime("%Y-%m-%d %H:%M:%S"), s=s))

myprint("aha myprint")
```

## mpms多线程下每个线程单独变量

自己写的类不是线程安全的，所以在多线程下要做到每个线程自己一个变量互不干扰

mpms下使用EasyLogin完整示例代码模板， 先要使用我fork的版本 加上了len支持：

```bash
wget https://d.py3.io/mpms.py
```

```python
from mpms import MPMS
from EasyLogin import EasyLogin
import threading
thread_data = threading.local()

import time
myprint = lambda s: print("[{showtime}] {s}".format(showtime=time.strftime("%Y-%m-%d %H:%M:%S"), s=s))

def worker(id):
    global thread_data
    a = thread_data.__dict__.get("a")
    if not a:
        a = EasyLogin()
        thread_data.__dict__["a"] = a
    pass # do the stuff, like a.get
    return result

def handler(meta, result):
    # meta["fp"].write ...
    pass # do the stuff

if __name__ == "__main__":
    meta = {"fp": open("result.txt","w",encoding="utf-8")}
    m = MPMS(worker, handler, 2, 2, meta=meta)
    m.start()
    for i in range(...):
        m.put(i)
    while len(m)>10:
        myprint("Remaning "+str(len(m)))
        time.sleep(2)
    m.join()
    myprint("Done!")
```