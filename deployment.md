

### start a project

    mkdir myproject

### create virtualenv

    cd myproject
    virtualenv venv
    source venv/bin/active

### install web framework

    pip install flask

### add requirements.txt
    pip freeze > requirements.txt

### start a webapp

    vim myapp.py    
    
    myapp.py
    
    from flask import Flask
    app = Flask(__name__)

    @app.route('/')
    def index():
        return 'hello flask'


    if __name__ == '__main__':
        app.debug = True
        app.run()

### test the webapp
    python myapp.py
    
browser the url  http://127.0.0.1:5000

### deploy by gunicorn
    pip install gunicorn
    gunicron -w4 -b0.0.0.0:8000 myapp:app
browser the url http://127.0.0.1:8000
    pkill gunicorn

### install supervisor
    pip install supervisor
    echo_supervisord_conf > supervisor.conf
    vim supervisor.conf

append the myapp config
    [program:myapp]
    command=/home/rsj217/rsj217/myproject/venv/bin/gunicorn -w4 -b0.0.0.0:8000 myapp:app
    directory=/home/rsj217/rsj217/myproject
    startsecs=0
    stopwaitsecs=0
    autostart=false
    autorestart=false
    stdout_logfile=/home/rsj217/rsj217/myproject/log/gunicorn.log
    stderr_logfile=/home/rsj217/rsj217/myproject/log/gunicorn.err
    
start by supervisor
    supervisord -c supervisor.conf   
    supervisorctl -c supervisor.conf status
    supervisorctl -c supervisor.conf reload
    supervisorctl -c supervisor.conf start [all]|[appname]
    supervisorctl -c supervisor.conf stop [all]|[appname]

active the supervisor web admin
    [inet_http_server]         ; inet (TCP) server disabled by default
    port=127.0.0.1:9001        ; (ip_address:port specifier, *:port for all iface)
    username=user              ; (default is no username (open server))
    password=123               ; (default is no password (open server))

    [supervisorctl]
    serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
    serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
    username=user              ; should be same as http_username if set
    password=123                ; should be same as http_password if set
    ;prompt=mysupervisor         ; cmd line prompt (default "supervisor")
    ;history_file=~/.sc_history  ; use readline history if available

append the nginx
    [program:nginx]
    command=/usr/sbin/nginx
    startsecs=0
    stopwaitsecs=0
    autostart=false
    autorestart=false
    stdout_logfile=/home/rsj217/rsj217/myproject/log/nginx.log
    stderr_logfile=/home/rsj217/rsj217/myproject/log/nginx.err   






  
        


MobileMainPageP2P.objects.filter(item__status=u'正在招标').order_by('hot_score')





