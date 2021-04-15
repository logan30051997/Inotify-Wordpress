# Cài Inotify-tool trên CentOS7 theo dõi thay đổi files trong Wordpress

## 1. Cài đặt Wordpress

[Cài wordpres LEMP stack](https://github.com/logan30051997/Install-webserver-CentOS7/tree/main/LEMP/wordpress)

## 2. Cài Inotify-tools

[Web Inotify-tools](https://github.com/inotify-tools/inotify-tools/wiki)

`yum install -y epel-release && yum update`

`yum install inotify-tools`

## 3. Scripts tối ưu chạy Inotify theo dõi

### Scrípts theo dõi inotify

`vim /home/inotify/inotify.sh`

```
#!bin/bash
inotifywait -m /home/www/wordpress /home/www/wordpress/wp-admin -e modify,delete,create,move |
    while read path action file euid; do
        if [[ "$file" =~ .*php$ ]]; then
{
messs="$action file '$file' in directory '$path' by '$EUID' "
CHATID="-401349290"
KEY="1782130751:AAFRzdmz-sEBCvOp2lrFeHeT9V4m4kJSd0k"

/usr/bin/curl -k -s --max-time 10 -d "chat_id=$CHATID&disable_web_page_preview=1&parse_mode=html&text=$messs" https://api.telegram.org/bot$KEY/sendMessage
}
        fi
    done
```
### Chạy Inotify dưới daemon

- Tạo service inotify trong 

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
- Chạy service 

`systemctl daemon-reload`

`systemctl start inotify`

`systemctl enable inotify`

### 4. Tài liệu

http://manpages.ubuntu.com/manpages/bionic/man1/inotifywait.1.html

https://www.systutorials.com/docs/linux/man/1-inotifywatch/



