---
title: 5分鐘免費幫你的網站加上https
tags:
  - https
  - ssl
  - letsencrypt
  - free
keywords: https,ssl,letencrypt,free
date: 2017-05-09 22:18:12
---


[Let's Encrypt](https://letsencrypt.org/) 是 Internet Security Research Group (ISRG) 所建立的一個 Certificate Authority (CA)。建立受信任的 https 連線，你需要從 CA 拿到一個 certificate。 EFF 提供了一套工具 [Certbot](https://certbot.eff.org/)，可以免費的、快速的幫你的網站拿到 Let's Encrypt 的 ssl certificate，以達成 https 連線。

### Certbot
[Certbot](https://certbot.eff.org/) 可以自動的幫你的網站部署 Let's Encrypt 的 certificate。

#### 步驟：
進入 [Certbot](https://certbot.eff.org/)，找到 `I'm using Software on System`（如圖）

![](https://i.gyazo.com/3b5d618749ed2ed77a6f939160834d5b.png)

選擇你的網站使用的軟體跟系統，以筆者來說就是 `Nginx` 跟 `Ubuntu(other)`。

再來，照著網站的安裝流程，得到 `certbot-auto` 這個工具。然後根據你的軟體的不同，執行 certbot-auto 並帶上不同的參數，比如 `Nginx` 就必須輸入 `certbot-auto --nginx`。certbot 會開始根據你的環境，需要你輸入一些資料，以下是如果看到 certbot 要求的資料，你可以怎麼填:

- email: 你就輸入你的 email
- webroot: 輸入你的 http server 的 html 資料夾的路徑，像 `Nginx` 就可能是 `/usr/share/nginx/html`
- 可能會問你要在哪個子網域啟用 https
- 會問你要不要強制使用 https，也就是 http 的連線都會導到 https，建議是選 `Secure`。

等到 certbot 執行完畢，就可以看到你的網站有綠綠的 https 了
![](https://i.gyazo.com/cb0cafb1e73c4b9222a8e1aa65f7a7c5.png)

#### Trouble Shooting
在執行 Certbot 的過程中可能會遇到 80 port 被佔用的問題，error log 類似下面：

```
 0.0.0.0:80 failed (98: Address already in use)
```
你可以先把佔用 80 port 的 process 關掉
```bash
sudo fuser -k 80/tcp
```
等到 certbot 執行完畢後，再把 server 打開。
