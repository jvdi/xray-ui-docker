# Xray-UI docker Image

> English Translate of x-ui [vaxilu](https://github.com/vaxilu) in docker version

Supported Architecture :

|                      Script                                      | Tag    | amd64 |
| ---------------------------------------------------------- | ------ | ----- |
| [jvdi/xray-ui](https://github.com/jvidi/xray-ui)              | latest | ✅    |

### Why Should You Use Docker

- Consistent & Isolated Environment
- Rapid Application Deployment
- Ensures Scalability & Flexibility
- Better Portability
- Cost-Effective
- In-Built Version Control System
- Security
- .....

### For this project, if you use docker

- You don't need to concern yourself with operating systems, architectures and other issues.
- You will never ruin your Linux server. If you don't want to use it, you can stop or remove it from your environment exactly.
- Last but not least, you can easily deploy and upgrade

### Hot to use it

#### Prerequisite: Install docker

Use the official one-click script

```bash
curl -sSL https://get.docker.com/ | sh
```

#### Start you container

```
mkdir xray-ui && cd xray-ui
docker run -itd --network=host \
    -v $PWD/db/:/etc/xray-ui/ \
    -v $PWD/cert/:/root/cert/ \
    --name xray-ui --restart=unless-stopped \
    javidi/xray-ui
```

##### Or you could use docker compose to start it

```
mkdir xray-ui && cd xray-ui
wget https://raw.githubusercontent.com/jvdi/xray-ui-docker/main/docker-compose.yml
docker compose up -d
```

#### How to enable SSL to your Xray-UI Panel

This part describe how to enable SSL.

- Suppose your xray-ui port is `54321`
- Suppose your IP is `10.10.10.10`
- Suppose your domain is `xrayui.example.com` and you have set the A recode in cloudflare
- Suppose you are using Debian 10+ or Ubuntu 18+ system
- Suppose your email is `xxxx@example.com`

##### Steps as below

1. Install nginx and python3-certbot-nginx

```bash
sudo apt update
sudo apt install python3-certbot-nginx
```

2. Add new nging configurtion

```
touch /etc/nginx/conf.d/xrayui.conf
```

Add below to the file. Adjust appropriately to your own situation.

```nginx
server {
    listen 80;
    listen [::]:80;
    server_name xrayui.example.com;

    location / {
        proxy_redirect off;
        proxy_pass http://127.0.0.1:54321;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
    }

    # This part desribe how to reverse websockt proxy
     location /xray {
         proxy_redirect off;
         proxy_pass http://127.0.0.1:10001;
         proxy_http_version 1.1;
         proxy_set_header Upgrade $http_upgrade;
         proxy_set_header Connection "upgrade";
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header Host $http_host;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header Y-Real-IP $realip_remote_addr;
     }
}
```

3. Check your config is OK

```
nginx -t
```

4. Get cert

```
certbot --nginx --agree-tos --no-eff-email --email xxxxx@example.com
```

For more details, refer to [cerbot](https://certbot.eff.org/)

5. Reload nginx config

```
nginx -s reload
```

6. Test automatic renewal

```
sudo certbot renew --dry-run
```

Note: Default credentials

Username: `admin`

Password: `admin`

## 💞 Thanks
- [vaxilu/x-ui](https://github.com/vaxilu/x-ui) , Xray Proxy web-ui
- [Chasing66](https://github.com/Chasing66/x-ui) , Docker file
