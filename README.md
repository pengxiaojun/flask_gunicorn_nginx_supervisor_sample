#flask_gunicorn_nginx_supervisor_sample
使用 flask+gunicorn+nginx+supervisor 进行网站部署的示例
web的部署方面，大都是 nginx 做前端代理，中间 webservice 调用程序脚本。
大概方式：`nginx + webservice + script`


## 组件介绍
python运行环境使用virtualenv来管理, 包管理用 pip 来处理

    pip freeze > requirements.txt    # 导出依赖包
    pip install -r requirements.txt  # 安装依赖包

### flask
flask 是一个轻量级可扩展的python web框架，支持python2,python3, 源码遵循PEP8标准
    virtuanlenv venv
    source venv/bin/active
    pip install flask
    deactivate
应用示例
    from flask import Flask
    app = Flask(__name__);
    @app.route("/")
    def index():
        return "This is home page"
    def main():
        app.run(debug=True)
运行:
    http://127.0.0.1:5000 (5000是flask自带web服务器的默认端口)

### gunicorn
flask自带的web服务器不建议在生产环境下使用,gunicorn用作wsgi容器.当然还有一些比如tornado, gevent, twisted等.目前uwsgi也可用来进行部署
    pip install gunicorn

gunicorn 启动 flask，先去掉上面代码 app.run()(这将启动flask自带的web服务器), 现在需要用gunicorn来启动 app

     gunicron -w4 -b0.0.0.0:8000 main:app

侦听8000, -w4开启4个worker, -b绑定的ip地址(0.0.0.0表示INADDR_ANY)
 

### nginx
nginx一般用来作反正代理服务器. 这里的正向与反向是由于代理服务器的放的位置而决定的

    正向代理：{ client -> 代理服务器 } -> web server

    反向代理：client -> { 代理服务器 -> web server } 
    {} 表示局域网
nginx在这里主要用来转到请求到 gunicorn的8000端口, 最后转给python脚本程序来处理请求
    sudo apt-get install nginx

配置:
    server {
        listen 127.0.0.1:8888;

        location / {
            proxy_pass http://127.0.0.1:8000;
            proxy_redirect off;
            proxy_set_header Host $host:8080;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }


### supervisor
supervisor是进程管理工具, 这里主要用它来管理 gunicorn与nginx
    pip install supervisor
    sudo echo_supervisord_conf > /etc/supervisor.conf   # 生成默认配置文件
    sudo vim supervisor.conf                       

    [program:myapp]
    command=/var/www/myproject/venv/bin/gunicorn -w4 -b0.0.0.0:8000 main:app    ; supervisor启动命令
    directory=/var/www/myproject                                       ; 项目的文件夹路径
    startsecs=0                                                ; 启动时间
    stopwaitsecs=0                                             ; 终止等待时间
    autostart=false                                            ; 是否自动启动
    autorestart=false                                          ; 是否自动重启
    stdout_logfile=/var/log/gunicorn.log                       ; log 日志
    stderr_logfile=/var/log/gunicorn.err                       ; 错误日志

    [program:nginx]
    command=/usr/sbin/nginx
    startsecs=0
    stopwaitsecs=0
    autostart=false
    autorestart=false
    stdout_logfile=/var/log/nginx.log
    stderr_logfile=/var/log/nginx.err   


supervsior 启动 gunicorn
    `supervisord -c /etc/supervisor.conf `

http://127.0.0.1:8000访问首页

