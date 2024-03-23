# 测试案例：将Django部署到render

## 创建一个新的django项目
### 安装django并创建项目
```console
pip install django
pip freeze > requirements.txt
django-admin startproject mysite .

```

### 创建一个登录页面
1. 创建一个新的应用程序
```console
python3 manage.py startapp homepage
```

2. 在mysite/setting.py文件中添加该应用程序
```python
INSTALLED_APPS = [
    'homepage.apps.HomepageConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    ...
]
```

3. 编写视图
遇到问题：django.template.exceptions.TemplateSyntaxError: Invalid block tag on line 18: 'static'. Did you forget to register or load this tag?
在index.html文件头部加上{% load static %}

### 添加基本安全：略过

### 添加PostgreSQL支持
1. 添加依赖包
```console
pip install dj-database-url psycopg2-binary
pip freeze > requirements.txt
```
2. 修改mysite/sestting.py文件
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

3. 遇到问题：Is the server running on that host and accepting TCP/IP connections?
   

### 建立静态文件服务
- 需要用到whitenoise[brotil]
- 安装命令：pip install 'whitenoise[brotli]'
- 打开mysite/setting.py文件，添加如下内容
  ```python
    MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
    ]

    # This production code might break development mode, so we check whether we're in DEBUG mode
    if not DEBUG:
        # Tell Django to copy static assets into a path called `staticfiles` (this is specific to Render)
        STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
        # Enable the WhiteNoise storage backend, which compresses static files to reduce disk use
        # and renames the files with unique names for each version to support long-term caching
        STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
  ```   


```console
pip install django
pip freeze > requirements.txt
django-admin startproject mysite .
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
注意：This connection string assumes that you have PostgreSQL running on localhost, on port **5432**, with a **database named mysite** and a **user named postgres** with the **password postgres**

3. 迁移数据库
命令： python manage.py migrate


python3 -m gunicorn mysite.asgi:application -k uvicorn.workers.UvicornWorker


apturl==0.5.2ubuntu22

dj-database-url==2.1.0
Django==5.0.3
gunicorn==21.2.0
psycopg2-binary==2.9.9
sqlparse==0.4.4
asgiref==3.8.0


#### 依赖项问题
- 如果直接用命令：pip freeze > requirements.txt
- 这样做会导致很多依赖项的产生，有可能会导致部署失败
- 解决办法是手动添加依赖项，将部署需要用到的包及其版本号填入requirements.txt文件
- 如下所示
  ```txt
    dj-database-url==2.1.0
    Django==5.0.3
    gunicorn==21.2.0
    psycopg2-binary==2.9.9
    sqlparse==0.4.4
    asgiref==3.8.0
    uvicorn==0.29.0
  ```





