![alt text](https://raw.githubusercontent.com/Pterify/FlexaCP/main/FlexaCP.png)

# Flexa V1.0

> [!NOTE]
> Flexa is a [Heliactyl 14](https://github.com/heliactyloss/heliactyl) but a Flexa Style, We just changed the design to the modern way.

Flexa is a high-performance client area for the Pterodactyl Panel. It allows your users to create, edit and delete servers, and also earn coins which can be used to upgrade their servers.

All features:
- Coins (AFK Page Earning)
- Servers (create, view, edit servers)
- Login Queue (prevent overload)
- User System (auth, regen password)
- Store (buy resources with coins)
- Dashboard (view resources)
- API (for bots & other things)


## Install Guide

  Ubuntu 22.04 - 20.04 ✅ <br>
  Debian 10 - 11 - 12 ✅
  - Install Nodejs 16, it's recommended to install it with nvm `sudo apt install curl && curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash`
  - reopen a new ssh session (eg restart putty)
  - `nvm install 16`
  - check the node version with `node -v`
  - Clone the repo: `git clone https://github.com/Pterify/flexacp.git /var/www/flexa`
  - `cd /var/www/flexa && npm i`
  - To run Flexa, use `node .`
  - To Running in background, use `npm install pm2 -g` after that `cd /var/www/flexa && pm2 start node .`
  
  Windows ✅
  - Download Nodejs: [Download x64](https://nodejs.org/dist/v18.20.2/node-v18.20.2-x64.msi)
  - Download repo: [Download](https://github.com/Pterify/flexacp/archive/refs/heads/main.zip)
  - `npm install`
  - To run Flexa, use `node .`

## NGINX reverse proxy

 - Run `nano /etc/nginx/sites-enabled/default`
 - Remove Everything and
 - Paste the configuration at the bottom of this and replace with the IP of the pterodactyl server including the port and with the domain you want your dashboard to be hosted on

```nginx
server {
    listen 80;
    server_name <domain>;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;

    location /ws {
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      proxy_pass "http://localhost:<port>/ws";
    }

    server_name <domain>;

    ssl_certificate /etc/letsencrypt/live/<domain>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<domain>/privkey.pem;
    ssl_session_cache shared:SSL:10m;
    ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    location / {
      proxy_pass http://localhost:<port>/;
      proxy_buffering off;
      proxy_set_header X-Real-IP $remote_addr;
    }
}
```
### Create SSL certificates

 - Run `sudo apt update`
 - Run `apt install nginx && apt install certbot -y`
 - Run `ufw allow 80 && ufw allow 443`
 - Run `certbot certonly -d <Your Flexa Domain>`
 - Select `1`


 - Run `systemctl restart nginx` and try open your domain.

#### Enter the directory and configure the `settings.json` file - most are optional except the Pterodactyl and OAuth2 settings which **must** be configured

```nginx
{
  "name": "Flexa",
  "pterodactyl": {
    "domain": "https://panel.example.com",
    "key": "Your Pterodactyl API key with all permissions"
  },
  "timezone": "Europe/London",
  "version": "1.0",
  "website": {
    "port": 3000,
    "secret": "Default Secret (Change this to any string you want)"
  },
  "database": "sqlite://database.sqlite",
  "api": {
    "client": {
      "accountSwitcher": true,
      "api": {
        "enabled": true,
        "code": "Default API Key (Change this to any string you want)"
      },
      "j4r": {
        "enabled": false,
        "ads": [
        ]
      },
      "bot": {
        "token": "Your Discord bot token",
        "joinguild": {
          "_comment": "The Discord bot must be in these servers and have invite permissions. Automatic guild joining will not work unless role packages are configured correctly. You can always just set it to a random role & package so that only this works.",
          "enabled": false,
          "guildid": [
            "000000000000000000"
          ]
        },
        "giverole": {
          "_comment": "Auto-join must be enabled for this to work - or the user must already be in your Discord. This will give the user a role when they login.",
          "enabled": false,
          "guildid": "000000000000000000",
          "roleid": "000000000000000000"
        }
      },
      "passwordgenerator": {
        "signup": true,
        "note": "Use this to disable registering on Flexa.",
        "length": 16
      },
      "allow": {
        "newusers": true,
        "regen": true,
        "server": {
          "create": true,
          "modify": true,
          "delete": true
        },
        "overresourcessuspend": false
      },
      "oauth2": {
        "_comment": "Go to https://discord.dev/ and create an application to set up OAuth2. This is required for Flexa to work. You must also set the redirect URL to the link below.",
        "id": "Your client ID",
        "secret": "Your client secret",
        "link": "http://localhost:3000",
        "callbackpath": "/callback",
        "prompt": true,
        "ip": {
          "trust x-forwarded-for": true,
          "block": [],
          "duplicate check": false,
          "_note": "Anti-alt checks will not work if you are proxying through Cloudflare. This is due to the fact that Cloudflare does not pass the real IP address through the headers. If you are using Cloudflare, please disable this feature."
        }
      },
      "ratelimits": {
        "/callback": 2,
        "/create": 1,
        "/delete": 1,
        "/modify": 1,
        "/updateinfo": 1,
        "/setplan": 2,
        "/admin": 1,
        "/regen": 1,
        "/renew": 1,
        "/api/userinfo": 1
      },
      "packages": {
        "default": "default",
        "list": {
          "default": {
            "ram": 2048,
            "disk": 5120,
            "cpu": 100,
            "servers": 2
          }
        },
        "rolePackages": {
          "note": "This allows you to set a different plan/package to people who have a specific role however this requires the Discord bot to be configured and functioning. This is mainly used for Boost rewards.",
          "roleServer": "Discord Server ID",
          "roles": {
            "Discord Role ID": "Flexa package name"
          }
        }
      },
      "locations": {
        "1": {
          "package": null,
          "name": "Default"
        }
      },
      "eggs": {
        "paper": {
          "display": "Paper",
          "minimum": {
            "ram": 1024,
            "disk": 1024,
            "cpu": 75
          },
          "maximum": {
            "ram": null,
            "disk": null,
            "cpu": null
          },
          "info": {
            "egg": 5,
            "docker_image": "ghcr.io/pterodactyl/yolks:java_17",
            "startup": "java -Xms128M -XX:MaxRAMPercentage=95.0 -Dterminal.jline=false -Dterminal.ansi=true -jar {{SERVER_JARFILE}}",
            "environment": {
              "SERVER_JARFILE": "server.jar",
              "BUILD_NUMBER": "latest"
            },
            "feature_limits": {
              "databases": 4,
              "backups": 4
            }
          }
        },
        "bungeecord": {
          "display": "BungeeCord",
          "minimum": {
            "ram": 512,
            "disk": 512,
            "cpu": 75
          },
          "maximum": {
            "ram": null,
            "disk": null,
            "cpu": null
          },
          "info": {
            "egg": 3,
            "docker_image": "ghcr.io/pterodactyl/yolks:java_17",
            "startup": "java -Xms128M -Xmx{{SERVER_MEMORY}}M -jar {{SERVER_JARFILE}}",
            "environment": {
              "SERVER_JARFILE": "bungeecord.jar",
              "BUNGEE_VERSION": "latest"
            },
            "feature_limits": {
              "databases": 4,
              "backups": 4
            }
          }
        }
      },
      "coins": {
        "enabled": true,
        "store": {
          "enabled": true,
          "ram": {
            "cost": 300,
            "per": 1024
          },
          "disk": {
            "cost": 200,
            "per": 5120
          },
          "cpu": {
            "cost": 350,
            "per": 100
          },
          "servers": {
            "cost": 100,
            "per": 2
          }
        }
      }
    },
    "afk": {
      "path": "ws",
      "every": 60,
      "coins": 1,
      "enabled": true
    }
  },
  "antivpn": {
    "note": "For this to work, generate an API Key on https://proxycheck.io/. If you do not put a key, Flexa will automatically disable anti-VPN.",
    "status": false,
    "APIKey": "Proxycheck API Key",
    "whitelistedIPs": [
      "IP address"
    ]
  },
  "whitelist": {
    "note": "This allows only specific people to be able to login to the Flexa dashboard. This does not affect your Panel.",
    "status": false,
    "users": [
      "User ID"
    ]
  },
  "logging": {
    "status": false,
    "webhook": "Webhook URL",
    "actions": {
      "user": {
        "signup": true,
        "create server": true,
        "gifted coins": true,
        "modify server": true,
        "buy servers": true,
        "buy ram": true,
        "buy cpu": true,
        "buy disk": true
      },
      "admin": {
        "set coins": true,
        "add coins": true,
        "set resources": true,
        "set plan": true,
        "create coupon": true,
        "revoke coupon": true,
        "remove account": true,
        "view ip": true
      }
    }
  }
}
```
