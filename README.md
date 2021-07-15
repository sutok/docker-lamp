## docker-compose

1. docker-compose build --no-cache
2. docker-compose up -d

## web server

- ubuntu 20.4
- php7.4
- apache2.4
- DocumentRoot: /var/www/html

## database server

- MySQL8.x
- CentOS8(arm版が見つからなかった為)
- server情報適宜変更(docker-compose)
```
  db:
    container_name: app_db #web側コンテナからのホスト名
    build: ./docker/db
    environment:
      MYSQL_ROOT_PASSWORD: password1
      MYSQL_DATABASE: app_db
      MYSQL_USER: db_user
      MYSQL_PASSWORD: db_password
      TZ: 'Asia/Tokyo'
```

## Certificate

- 自己証明書(docker/app/ssl/)

    内包のserver.crt, server.keyでにて見れなければ自作する必要あり
```
ADD ssl/server.crt /etc/ssl/certs/
ADD ssl/server.key /etc/ssl/private/

# Composerのインストール
RUN cd /usr/bin && curl -s http://getcomposer.org/installer | php && ln -s /usr/bin/composer.phar /usr/bin/composer \
&& sed -i  's/^\;date\.timezone\ =/date.timezone = Asia\/Tokyo/'  /etc/php/7.4/apache2/php.ini \
&& chmod 400 /etc/ssl/certs/server.crt \
&& chmod 400 /etc/ssl/private/server.key 

```

## php source code root directory

- 以下、`git clone`例
```
cd src
git clone https://github/xxxx/yyy projects
```
- docker-compose.yaml
```
    container_name: web_app
    volumes:
      - ./src/projects:/var/www/html
```