## web server

- ubuntu 20.4
- php7.4
- apache2.4
- DocumentRoot: 
```
    volumes:
      - ./src/projects:/var/www/html   # localhost用のDocumentRoot
      - ./src/renewal:/var/www/renewal # renewal用のDocumentRoot
```
- VirtualHosts: docker-compose.yamlへlocalhost, renewalというServerNameを設定)
```
    environment:
      - VIRTUAL_Host=localhost,renewal
```

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
- 自己証明書作成ツール（mkcert）
```
cd docker/app/ssl
mkcert localdomain.local
mkcert renewal
```
- 作成した証明書を配置
```
ADD ssl/localdomain.local.crt /etc/ssl/certs/
ADD ssl/localdomain.local.key /etc/ssl/private/
ADD ssl/renewal.crt /etc/ssl/certs/
ADD ssl/renewal.key /etc/ssl/private/

# Composerのインストール
RUN cd /usr/bin && curl -s http://getcomposer.org/installer | php && ln -s /usr/bin/composer.phar /usr/bin/composer \
&& sed -i  's/^\;date\.timezone\ =/date.timezone = Asia\/Tokyo/'  /etc/php/7.4/apache2/php.ini \
&& chmod 400 /etc/ssl/certs/localdomain.crt \
&& chmod 400 /etc/ssl/private/localdomain.local.key \
&& chmod 400 /etc/ssl/certs/renewal.crt \
&& chmod 400 /etc/ssl/private/renewal.key 

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

## 作業端末のhostsへ追加

```
127.0.0.1 renewal
```

## docker-compose　にて起動

1. docker-compose build --no-cache
2. docker-compose up -d


## web access
- https://loclahost/
- https://renewal/
