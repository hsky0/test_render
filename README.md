# 测试案例：将Django部署到render

## 创建一个django项目

## 添加数据库支持
1. 命令
2. 
```console
pip install dj-database-url psycopg2-binary
pip freeze > requirements.txt
```

1. 修改mysite/setting.py文件

```python
# Import the dj-database-url package at the beginning of the file
import dj_database_url

# Database documentation https://docs.djangoproject.com/en/5.0/ref/settings/#databases

DATABASES = {
    'default': dj_database_url.config(
        # Replace this value with your local database's connection string.
        default='postgresql://postgres:postgres@localhost:5432/mysite',
        conn_max_age=600
    )
}


```

3. 迁移数据库
命令： python manage.py migrate


python3 -m gunicorn mysite.asgi:application -k uvicorn.workers.UvicornWorker