# Các bước install supervísor trên Ubuntu
  + Cài đặt service: sudo apt-get install supervisor
  + Tạo folder conf.d trong thư mực /etc/supervisor nếu chưa có
  + Tạo file config cần chạy tương ứng trong thư mục conf.d theo cấu trúc:
  ```sh
    [program:test-backend_queue]
    command=sudo -u root php artisan queue:work --daemon --env=dev --tries=1 --queue=default,onboarding
    directory=/usr/share/nginx/www/test-lumen
    stdout_logfile=/usr/share/nginx/www/test-lumen/app/test-backend_supervisord.log
    redirect_stderr=true
    numprocs=10
    process_name=%(program_name)s_%(process_num)02d
    autostart=true
    ;user=abc
    autorestart=true
  ```
  + Chỉnh sửa file config /etc/supervisord.conf theo config sau:
  
```sh
        [supervisord]
    logfile=/tmp/supervisord.log ; (main log file;default $CWD/supervisord.log)
    logfile_maxbytes=50MB        ; (max main logfile bytes b4 rotation;default 50MB)
    logfile_backups=10           ; (num of main logfile rotation backups;default 10)
    loglevel=info                ; (log level;default info; others: debug,warn,trace)
    pidfile=/tmp/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
    nodaemon=false               ; (start in foreground if true;default false)
    minfds=1024                  ; (min. avail startup file descriptors;default 1024)
    minprocs=200                 ; (min. avail process descriptors;default 200)
    ;umask=022                   ; (process file creation umask;default 022)
    ;user=abc                 ; (default is current user, required if root)
    ;identifier=supervisor       ; (supervisord identifier, default is 'supervisor')
    directory=/tmp              ; (default is not to cd during start)
    ;nocleanup=true              ; (don't clean up tempfiles at start;default false)
    ;childlogdir=/tmp            ; ('AUTO' child log dir, default $TEMP)
    ;environment=KEY="value"     ; (key value pairs to add to environment)
    ;strip_ansi=false            ; (strip ansi escape codes in logs; def. false)


    [rpcinterface:supervisor]
    supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface


    [unix_http_server]
    file=/tmp/supervisor.sock
    chmod=0755 ; sockef file mode (default 0700)

    [inet_http_server]
    port=127.0.0.1:9005
    ;username=abc

    [supervisorctl]
    ;srevreurl=http://127.0.0.1:9001
    serverurl=unix:///tmp/supervisor.sock
    ;username=abc
    ;serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket

    [include]
    files = /etc/supervisord.d/*.conf
  ```
  + Tạo service cho supervísor trong systemd để service tự động chạy khi khởi động hệ thống(Có thể không cần):
  ```sh
    touch /etc/systemd/system/supervisord.service
  ```
  + Cấu hình service theo config:
  ```sh
    [Unit]
    Description=Supervisor daemon
    Documentation=http://supervisord.org
    After=network.target

    [Service]
    ExecStart=supervisord -n -c
    /etc/supervisor/supervisord.conf
    ExecStop=upervisorctl $OPTIONS
    shutdown
    ExecReload=supervisorctl $OPTIONS
    reload
    KillMode=process
    Restart=on-failure
    RestartSec=42s

    [Install]
    WantedBy=multi-user.target
    Alias=supervisord.service
  ```
  + Start supervísor service :
  ```sh
    systemctl start supervisord.service
  ```
  + Tiếp theo chạy 2 câu lệnh sau để đọc config và áp dụng thay đổi khi có thêm config cho worker mới:
  ```sh
    sudo supervisorctl reread
    sudo supervisorctl update
  ```
  + Để xem các worker đang chạy gõ lệnh:
  ```sh
    sudo supervisorctl reread
  ```
