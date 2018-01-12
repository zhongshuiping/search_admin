# 如何使用虚拟环境

Python3.3以上通过venv模块原生支持虚拟环境，命令为`pyvenv`。

##  0. 创建一个名字为`venv`的虚拟环境

`pyvenv venv` 或者 `python3 -m venv /path/to/new/virtual/environment`, 推荐后者

## 1. 激活名字为`venv`的虚拟环境

`source venv/bin/activate`

## 2. 激活虚拟环境命令会修改命令行提示符，显示如下：

`(venv) $`

## 3. 退出当前虚拟环境

`deactivate`

## 4. 安装工具

`pip install gunicorn`

`pip install supervisor` # 只能用python2.7安装

## 5. 在supervisor中配置gunicorn

```
[program:search]
command=/home/venv_search/bin/gunicorn -w 49 -b 127.0.0.1:5050 wsgi:application
directory=/home/tony/search
startsecs=1
stopwaitsecs=1
autostart=true
autorestart=true
startretries=10
stdout_logfile=/var/log/search.stdout.log
stdout_logfile_maxbytes=100MB
stdout_logfile_backups=2
stderr_logfile=/var/log/search.stderr.log
stderr_logfile_maxbytes=100MB
stderr_logfile_backups=2
```

## 6. 启动supervisor进程

`supervisord`

## 7. 进入supervisor的控制台

`supervisorctl`

## 8. 在nginx中配置gunicorn反向代理

```
server {
        listen 80;
        server_name  localhost;
        location / {
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Scheme $scheme;
                proxy_redirect off;
                proxy_pass http://127.0.0.1:5050
        }
}

```

<br/>

## 数据库迁移步骤

`python manage.py db init`

`python manage.py db migrate -m "inition migrate"`

`python manage.py db upgrade`

`python manage.py deploy`


## 集成celery

`cd search && celery worker -A celery_runner --loglevel=info`