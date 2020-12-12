# 生成 SSH 密钥对

## 创建 ED25519 密钥

默认保存在 ~/.ssh/id_ed25519

```
ssh-keygen -t ed25519 -C "<comment>"
```

## 创建 RSA 密钥

默认保存在 ~/.ssh/id_rsa

```
ssh-keygen -t rsa -b 2048 -C "email@example.com"
```
