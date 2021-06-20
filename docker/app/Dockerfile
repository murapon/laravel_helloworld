FROM amazonlinux:2 as php-runtime

# 作業ディレクトリ
WORKDIR /var/www/html

# タイムゾーン設定
RUN ln -sf /usr/share/zoneinfo/Japan /etc/localtime
RUN echo ZONE="Asia/Tokyo" > /etc/sysconfig/clock
RUN echo UTC=False >> /etc/sysconfig/clock

# パッケージ更新
RUN yum update -y
# epelのPHP7.4を入れる
RUN amazon-linux-extras list
#RUN amazon-linux-extras install epel -y
RUN amazon-linux-extras install php8.0 -y
# fpmは使わないので削除
RUN yum remove php-fpm -y
# httpdとphpの追加パッケージを入れる
RUN yum install \
    git \
    unzip \
    httpd \
    mod_php \
    php-mbstring \
    php-pdo \
    php-opcache \
    php-json \
    php-mysqlnd \
    php-process \
    php-redis \
    php-soap \
    php-xml \
    php-zip \
    -y

# composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# 開発環境のみ
FROM php-runtime as dev
RUN amazon-linux-extras install vim -y

# phpdbgをインストール
# RUN yum --enablerepo=epel install php-dbg -y

# httpdの設定
COPY ./docker/app/apache2/httpd.conf /etc/httpd/conf/httpd.conf
COPY ./docker/app/apache2/conf.d/app.conf /etc/httpd/conf.d/app.conf
RUN mkdir -p /var/www/html/public
# httpdの設定ファイルの文法チェック
RUN httpd -t

EXPOSE 80
CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
