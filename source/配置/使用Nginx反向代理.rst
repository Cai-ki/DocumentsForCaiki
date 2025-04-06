使用Nginx反向代理
============================================================================
安装
*************
::

    sudo apt update
    sudo apt install nginx

配置
*****************
::

    sudo nano /etc/nginx/sites-available/git-proxy

    server {
        listen 80;
        server_name git.example.com;

        location / {
            proxy_pass https://github.com;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }

启动
******************
::

    sudo systemctl start nginx
    sudo systemctl enable nginx

    curl http://localhost

    sudo ln -s /etc/nginx/sites-available/git-proxy /etc/nginx/sites-enabled/
    sudo nginx -t
    sudo systemctl reload nginx

**********************

::

    sudo apt update
    sudo apt install certbot python3-certbot-nginx

    sudo certbot --nginx -d test.caiki.top

::

    sudo ufw status
    sudo ufw allow 80/tcp
    sudo ufw allow 443/tcp
    sudo ufw reload