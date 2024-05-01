![alt text](https://media.discordapp.net/attachments/1235321743833305239/1235335936674369546/FlexaCP.png?ex=6633ffbe&is=6632ae3e&hm=da31853581925cddceb8b228e1e2063d77cdcd05674a8c993b0f77e6515106c1&=&format=webp&quality=lossless&width=904&height=509)

# Flexa V1.0

> [!NOTE]
> Flexa is a Heliactyl 14 but a Flexa Style, We just changed the design to the modern way.

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
  
  Ubuntu 20.04 - 22.04 ✅
  - Install Nodejs 16, it's recommended to install it with nvm `sudo apt install curl && curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash`
  - reopen a new ssh session (eg restart putty)
  - `nvm install 16`
  - check the node version with `node -v`
  - Clone the repo: `https://github.com/Pterify/flexacp.git /var/www/heliactyl`
  - `cd /var/www/heliactyl && npm i`
  - To run Flexa, use `node .`
  
  Windows ✅
  - Download Nodejs: [Download x64](https://nodejs.org/dist/v18.20.2/node-v18.20.2-x64.msi)
  - Download repo: [Download](https://github.com/Pterify/flexacp/archive/refs/heads/main.zip)
  - `npm install`
  - To run Flexa, use `node .`
  
#### Enter the directory and configure the `settings.json` file - most are optional except the Pterodactyl and OAuth2 settings which **must** be configured

```nginx
{
  "name": "Flexa",
  "pterodactyl": {
    "domain": "https://panel.example.com",
    "key": "ptla_0000000000000000000000000000000000000000000"
  },
  "timezone": "Europe/London",
  "version": "1.0",
  "testing": false,
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
        "note": "Use this to disable registering on flexa.",
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
        "_comment": "Go to https://discord.dev/ and create an application to set up OAuth2. This is required for flexa to work. You must also set the redirect URL to the link below.",
        "id": "0000000000000000000",
        "secret": "0000000000000000000000000000-",
        "link": "https://client.example.com",
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
            "ram": 1024,
            "disk": 1024,
            "cpu": 100,
            "servers": 2
          }
        },
        "rolePackages": {
          "note": "This allows you to set a different plan/package to people who have a specific role however this requires the Discord bot to be configured and functioning. This is mainly used for Boost rewards.",
          "roleServer": "Discord Server ID",
          "roles": {
            "Discord Role ID": "flexa package name"
          }
        }
      },
      "locations": {
        "1": {
          "package": null,
          "name": "Location 1"
        }
      },
      "eggs": {
        "paper": {
          "display": "Paper",
          "minimum": {
            "ram": 1024,
            "disk": 1024,
            "cpu": 100
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
              "databases": 0,
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
    "note": "For this to work, generate an API Key on https://proxycheck.io/. If you do not put a key, flexa will automatically disable anti-VPN.",
    "status": false,
    "APIKey": "Proxycheck API Key",
    "whitelistedIPs": [
      "IP address"
    ]
  },
  "whitelist": {
    "note": "This allows only specific people to be able to login to the flexa dashboard. This does not affect your Panel.",
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

4. Create SSL certificates for your target domain and set up the NGINX reverse proxy

## NGINX Reverse Proxy

Here's a proxy config that we recommend, however you are free to change it:

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

## Development Tools

These commands are available:
```
npm run start - starts Heliactyl via nodemon
npm run build - builds TailwindCSS, required for making changes to the UI
```

## Heliactyl API v2

In v14, we've introduced the next generation of Heliactyl's API. You can see the documentation below:

### /api/v2/userinfo

```
Method: GET
Query Parameters:
  - id (string): The user's ID

Response:
  - status (string): "success" or an error message
  - package (object): The user's package details
  - extra (object): The user's additional resources
  - userinfo (object): The user's information from the Pterodactyl panel
  - coins (number | null): The user's coin balance (if coins is enabled)
```

### /api/v2/setcoins

```
Method: POST
Request Body:
  - id (string): The user's ID
  - coins (number): The number of coins to set

Response:
  - status (string): "success" or an error message
```

### /api/v2/setplan

```
Method: POST
Request Body:
  - id (string): The user's ID
  - package (string, optional): The package name (if not provided, the user's package will be removed)

Response:
  - status (string): "success" or an error message
```

### /api/v2/setresources

```
Method: POST
Request Body:
  - id (string): The user's ID
  - ram (number): The amount of RAM to set
  - disk (number): The amount of disk space to set
  - cpu (number): The amount of CPU to set
  - servers (number): The number of servers to set

Response:
  - status (string): "success" or an error message
```
