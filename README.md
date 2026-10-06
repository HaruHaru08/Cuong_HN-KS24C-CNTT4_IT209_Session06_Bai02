# Cuong_HN-KS24C-CNTT4_IT209_Session06_Bai02

## 1. Mục tiêu
- Tạo user và group mới trên Linux.
- Hiểu cơ chế sudo và tệp `/etc/sudoers`.
- Giới hạn quyền: nhóm `devops-admin` chỉ được chạy `systemctl start/stop/restart/status` mà không cần mật khẩu.

## 2. Môi trường
Ubuntu trong GitHub Codespaces (container, không chạy systemd).

## 3. Nhật ký lệnh

### 3.1. Tạo nhóm và user
```bash
sudo groupadd devops-admin
sudo adduser deployer
sudo usermod -aG devops-admin deployer
```
Kiểm tra:
devops-admin:x:1002:deployer
uid=1003(deployer) gid=1003(deployer) groups=1003(deployer),100(users),1002(devops-admin)

### 3.2. Cấu hình sudoers
```bash
sudo visudo -f /etc/sudoers.d/devops-admin
sudo chmod 0440 /etc/sudoers.d/devops-admin
sudo visudo -c
```
Dòng cấu hình theo đề:
```
%devops-admin ALL=(ALL) NOPASSWD: /usr/bin/systemctl start *, /usr/bin/systemctl stop *, /usr/bin/systemctl restart *, /usr/bin/systemctl status *
```
Nội dung thực tế trong file :
```
%devops-admin ALL=(ALL) NOPASSWD: /usr/bin/systemctl start *, /usr/bin/systemctl stop *, /usr/bin/systemctl restart *, /usr/bin/systemctl status *, /usr/local/bin/systemctl start *, /usr/local/bin/systemctl stop *, /usr/local/bin/systemctl restart *, /usr/local/bin/systemctl status *
```
Kết quả kiểm tra cú pháp:
```
/etc/sudoers: parsed OK
/etc/sudoers.d/README: parsed OK
/etc/sudoers.d/codespace: parsed OK
/etc/sudoers.d/devops-admin: parsed OK
```

## 4. Kết quả kiểm tra (đăng nhập bằng deployer)
```bash
su - deployer
id
sudo -l
sudo systemctl restart cron
```
Đầu ra `id`:
```
uid=1000(codespace) gid=1000(codespace) groups=1000(codespace),986(pipx),987(python),988(oryx),989(golang),990(docker),991(sdkman),992(ruby),993(php),994(conda),995(nvm),996(hugo),1001(ssh)
```
Đầu ra `sudo -l`:
```
e/java/current/bin\:/home/codespace/.ruby/current/bin\:/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/usr/local/bin\:/usr/local/share\:/home/codespace/.local/bin\:/home/codespace/.dotnet\:/home/codespace/nvm/current/bin\:/home/codespace/.php/current/bin\:/home/codespace/.python/current/bin\:/home/codespace/java/current/bin\:/home/codespace/.ruby/current/bin\:/home/codespace/.local/bin\:/usr/local/python/current/bin\:/usr/local/py-utils/bin\:/usr/local/jupyter\:/usr/local/oryx\:/usr/local/go/bin\:/go/bin\:/usr/local/sdkman/bin\:/usr/local/sdkman/candidates/java/current/bin\:/usr/local/sdkman/candidates/gradle/current/bin\:/usr/local/sdkman/candidates/maven/current/bin\:/usr/local/sdkman/candidates/ant/current/bin\:/usr/local/share/rbenv/shims\:/usr/local/share/rbenv/bin\:/usr/local/rubies/current/bin\:/usr/local/php/current/bin\:/opt/conda/bin\:/usr/local/share/nvm/current/bin\:/usr/local/hugo/bin\:/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/usr/share/dotnet

User codespace may run the following commands on codespaces-606de6:
    (root) NOPASSWD: ALL
```
Đầu ra `sudo systemctl restart cron`:
```
"systemd" is not running in this container due to its overhead.
Use the "service" command to start services instead. e.g.: 

service --status-all
```