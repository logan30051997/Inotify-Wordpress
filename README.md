# Cài Inotify-tool trên CentOS7 theo dõi thay đổi files trong Wordpress

## 1. Cài đặt Wordpress

[Cài wordpres LEMP stack](https://github.com/logan30051997/Install-webserver-CentOS7/tree/main/LEMP/wordpress)

## 2. Cài Inotify-tools

[Web Inotify-tools](https://github.com/inotify-tools/inotify-tools/wiki)

`yum install -y epel-release && yum update`

`yum install inotify-tools`

## 3. Scripts chạy Inotify theo dõi

### Scripts theo dõi inotify

- Theo dõi folder Wordpress

`vim /home/inotify/inotify.sh`

```
#!bin/bash
#Check folder 
inotifywait -m /home/www/wordpress /home/www/wordpress/wp-admin -e modify,delete,create,move |
    while read path action file euid; do
        if [[ "${file}" =~ .*php$ ]]; then
{
messs="|$action| -- File '${file}' in directory $path"
CHATID="-401349290"
KEY="1782130751:AAFRzdmz-sEBCvOp2lrFeHeT9V4m4kJSd0k"

/usr/bin/curl -k -s --max-time 10 -d "chat_id=$CHATID&disable_web_page_preview=1&parse_mode=html&text=$messs" https://api.telegram.org/bot$KEY/sendMessage
}
#Check file with user "nobody" and move to /tmp/danger/
 find /home/www/wordpress/ -user nobody -iname "*.php" | xargs -I '{}' mv '{}' /tmp/danger/
        fi
    done
```

- Theo dõi files không tin cậy ở folder /tmp/danger

`vim /home/inotify/danger.sh`

```
#!bin/bash

inotifywait -m /tmp/danger -e move |
        while read path action file euid; do
        if [[ "${file}" =~ .*php$ ]]; then
{
messwarning="[Warning]: |$action| -- File '${file}' in directory $path by nobody "
CHATID="-401349290"
KEY="1782130751:AAFRzdmz-sEBCvOp2lrFeHeT9V4m4kJSd0k"

/usr/bin/curl -k -s --max-time 10 -d "chat_id=$CHATID&disable_web_page_preview=1&parse_mode=html&text=$messwarning" https://api.telegram.org/bot$KEY/sendMessage
}
        fi
done

```
### Chạy Inotify dưới daemon

- Tạo service inotify.

`vim /etc/systemd/system/inotify.service`

```
[Unit]
Description=Inotify

[Service]
ExecStart=/home/inotify/inotify.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
- Tạo service danger

`vim /etc/systemd/system/danger.service`

```
[Unit]
Description=Inotify

[Service]
ExecStart=/home/inotify/danger.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

- Chạy service 

`systemctl daemon-reload`

`systemctl start inotify`

`systemctl start danger`

`systemctl enable inotify`

`systemctl enable danger`


### 4. Hoạt động 

**Bước 1: khởi động service theo dõi folder**

**Bước 2: Nếu có files đuôi .php được tạo trong folder được theo dõi sẽ gửi thông báo đến telegram cho quản trị biết**

**Bước 3: Sau khi thông báo, tiếp tục kiểm tra files đúng định dạng và user Trusted (tin tưởng), nếu file thuộc user "nobody" sẽ move sang foler /tmp/danger và lưu ở đó và đồng thời thông báo về telegram cho quản trị là có file không đúng yêu cầu**

*Sẽ có thông báo tạo files thỏa mãn yêu cầu và thông báo tạo files nguy hiểm (có thể kiểm tra trong /tmp/danger*
### 5. Tài liệu

http://manpages.ubuntu.com/manpages/bionic/man1/inotifywait.1.html

https://www.systutorials.com/docs/linux/man/1-inotifywatch/



