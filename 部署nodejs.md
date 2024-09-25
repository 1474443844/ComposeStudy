要在Ubuntu服务器上部署Node.js项目，并使用Nginx作为反向代理，请按照以下步骤操作：

---

### **1. 安装Node.js**

首先，确保您的Ubuntu系统上安装了Node.js和npm（Node.js的包管理器）。

- **添加Node.js源并安装**

  ```bash
  # 更新包列表
  sudo apt update

  # 安装curl（如果未安装）
  sudo apt install curl

  # 下载Node.js 14.x版本的安装脚本（您也可以选择其他版本，如16.x、18.x）
  curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -

  # 安装Node.js
  sudo apt install nodejs
  ```

- **验证安装**

  ```bash
  node -v
  npm -v
  ```

### **2. 准备Node.js项目**

- **上传或克隆您的项目**

  - **通过Git克隆项目**

    ```bash
    # 导航到您的项目目录，例如/var/www
    cd /var/www

    # 克隆您的项目仓库
    git clone https://github.com/yourusername/your-nodejs-project.git
    ```

  - **直接上传项目文件**

    您可以使用`scp`、`ftp`或其他工具将项目文件上传到服务器上的指定目录。

- **安装项目依赖**

  ```bash
  cd your-nodejs-project
  npm install
  ```

### **3. 安装并配置PM2**

PM2是一个Node.js的进程管理器，可用于确保您的应用在崩溃后自动重启，并在服务器重启后自动启动。

- **全局安装PM2**

  ```bash
  sudo npm install pm2@latest -g
  ```

- **使用PM2启动应用**

  ```bash
  pm2 start app.js --name your_app_name
  ```

  请将`app.js`替换为您应用的入口文件，`your_app_name`替换为您的应用名称。

- **将PM2配置为开机自启**

  ```bash
  pm2 startup systemd

  # 该命令会输出一条命令，复制并执行它。例如：
  sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u your_username --hp /home/your_username

  # 保存当前的PM2进程列表
  pm2 save
  ```

### **4. 配置Nginx作为反向代理**

- **创建Nginx服务器块配置文件**

  ```bash
  sudo nano /etc/nginx/sites-available/your_domain
  ```

  添加以下内容：

  ```nginx
  server {
      listen 80;
      server_name your_domain www.your_domain;

      location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection 'upgrade';
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
      }
  }
  ```

  - **注意事项**：
    - 将`your_domain`替换为您的实际域名。
    - 确保`proxy_pass`指向您的Node.js应用正在监听的端口（默认通常是3000）。

- **启用配置并测试Nginx**

  ```bash
  # 删除默认配置（可选）
  sudo rm /etc/nginx/sites-enabled/default

  # 创建符号链接
  sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/

  # 测试Nginx配置
  sudo nginx -t

  # 重启Nginx
  sudo systemctl restart nginx
  ```

### **5. 配置防火墙（如果适用）**

确保防火墙允许HTTP和HTTPS流量：

```bash
sudo ufw allow 'Nginx Full'
```

### **6. 测试您的应用**

- **通过域名访问**

  在浏览器中访问`http://your_domain`，查看您的Node.js应用是否正常运行。

### **7. （可选）设置SSL证书，启用HTTPS**

为了安全性，建议为您的网站配置SSL证书。

- **使用Certbot获取Let’s Encrypt免费SSL证书**

  ```bash
  sudo apt install certbot python3-certbot-nginx

  sudo certbot --nginx -d your_domain -d www.your_domain
  ```

- **按照提示完成证书申请和配置**

- **测试自动续期**

  ```bash
  sudo certbot renew --dry-run
  ```

### **8. （可选）环境变量和配置**

如果您的应用需要环境变量，您可以：

- **在PM2中设置环境变量**

  ```bash
  pm2 start app.js --name your_app_name --env production
  ```

  或者创建`.env`文件，并使用像`dotenv`这样的包在应用中加载环境变量。

### **9. （可选）日志管理**

- **查看应用日志**

  ```bash
  pm2 logs your_app_name
  ```

- **设置日志轮换**

  PM2可以自动处理日志轮换，防止日志文件过大。

  ```bash
  pm2 install pm2-logrotate
  ```

  **配置日志轮换参数**：

  ```bash
  pm2 set pm2-logrotate:max_size 10M
  pm2 set pm2-logrotate:retain 10
  ```

---

**注意事项**：

- **端口号**：确保您的Node.js应用监听的端口与Nginx配置中的`proxy_pass`一致。
- **权限问题**：确保您的用户对项目目录具有适当的权限。
- **防火墙设置**：如果仍无法访问，检查防火墙或云服务提供商的安全组设置。

**常见问题排查**：

- **无法访问应用**：检查Node.js应用是否正在运行，以及Nginx配置是否正确。
- **Nginx配置测试失败**：使用`sudo nginx -t`查看具体的错误信息，并根据提示修复配置文件。
- **域名解析问题**：确保您的域名正确解析到服务器的IP地址，DNS解析可能需要一些时间生效。

如果您在部署过程中遇到任何问题，请提供具体的错误信息，我会尽力协助您解决。
要使用PM2重启您的Node.js服务，可以按照以下步骤操作：

---

### **1. 查看PM2管理的进程列表**

首先，您可以查看当前由PM2管理的所有进程，以确定要重启的服务名称或ID。

```bash
pm2 list
```

这将显示所有进程的列表，包括它们的名称（Name）和进程ID（Id）。

### **2. 重启特定的服务**

使用`pm2 restart`命令，可以通过进程的名称或ID来重启服务。

- **通过名称重启**

  ```bash
  pm2 restart your_app_name
  ```

  将`your_app_name`替换为您的实际应用名称。

- **通过ID重启**

  ```bash
  pm2 restart process_id
  ```

  将`process_id`替换为实际的进程ID。

### **3. 重启所有服务**

如果您希望重启所有由PM2管理的进程，可以使用以下命令：

```bash
pm2 restart all
```

### **4. 验证服务状态**

重启完成后，您可以再次查看进程列表，确保服务已成功重启并处于`online`状态。

```bash
pm2 list
```

### **5. 查看服务日志（可选）**

如果在重启过程中遇到问题，您可以查看服务的日志以获取更多信息。

```bash
pm2 logs your_app_name
```

按下`Ctrl+C`可以退出日志查看。

### **6. 保存PM2进程列表**

如果您希望在服务器重启后，PM2能够自动启动您的服务，请保存当前的进程列表。

```bash
pm2 save
```

### **示例**

假设您的应用名称为`myapp`，您可以使用以下命令重启它：

```bash
pm2 restart myapp
```

---

### **注意事项**

- **确保应用名称正确**：应用名称是在您启动应用时指定的，例如`pm2 start app.js --name myapp`。
- **检查PM2版本**：使用`pm2 -v`查看PM2版本，确保您使用的是最新版本。
- **权限问题**：确保当前用户具有执行PM2命令的权限，必要时可使用`sudo`。

### **常见问题排查**

- **服务未成功重启**：查看PM2日志以获取错误信息。
  ```bash
  pm2 logs your_app_name
  ```
- **PM2命令不可用**：确认PM2已全局安装，并且在环境变量`PATH`中。
- **环境变量未加载**：如果您的应用依赖于环境变量，确保这些变量在重启后仍然可用。

---

如果您在重启服务的过程中遇到任何问题，请提供具体的错误信息或症状，我会尽力帮助您解决。
