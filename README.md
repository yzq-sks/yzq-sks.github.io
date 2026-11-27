# 知识产权政策平台 — GitHub Pages 部署说明

目标：把 `ipzc-site` 目录里的页面发布到 `https://<你的用户名>.github.io`，并绑定自定义域名 `ipzc.ip4sme.info`。

---

## 一、文件说明

| 文件 | 作用 |
|---|---|
| `index.html` | 首页，单文件、无任何外部依赖（不加载 CDN 字体/脚本，国内网络一定能打开） |
| `CNAME` | 自定义域名声明，内容为 `ipzc.ip4sme.info`，**不要改文件名** |
| `.nojekyll` | 告诉 GitHub 不要用 Jekyll 处理，避免带下划线/特殊目录名时 404 |

---

## 二、创建仓库并上传（约 3 分钟）

1. 登录 GitHub，右上角 **+ → New repository**
2. **Repository name 必须填：`<你的用户名>.github.io`**
   （例如用户名是 `zhangsan`，仓库名就是 `zhangsan.github.io`。名字必须完全一致，否则不会成为主页站点）
3. 可见性选 **Public**（Private 仓库的 Pages 需要付费方案）
4. 勾选 **Add a README file**，点 **Create repository**
5. 进入仓库 → **Add file → Upload files** → 把 `index.html`、`CNAME`、`.nojekyll` 三个文件一起拖进去
   - Windows 资源管理器默认不显示 `.nojekyll`，需要先在「查看」里勾选「隐藏的项目」
   - 拖拽会把文件放进子目录，请确认三个文件都在仓库**根目录**，不要出现 `ipzc-site/` 这层
6. 底部 **Commit changes**

> 备选：也可以直接 `git clone` 后复制文件、`git push`。

---

## 三、开启 Pages

1. 仓库 → **Settings → Pages**
2. **Source** 选 `Deploy from a branch`
3. **Branch** 选 `main`，目录选 `/ (root)`，**Save**
4. 等 1–2 分钟，页面顶部会出现站点地址：`https://<你的用户名>.github.io`
5. 打开这个地址，确认首页正常显示

---

## 四、绑定自定义域名

1. 仍在 **Settings → Pages**，在 **Custom domain** 里填 `ipzc.ip4sme.info`，点 **Save**
   - 仓库里已有 `CNAME` 文件，这一步通常会自动带出来
2. 此时会提示 **DNS check unsuccessful** —— **这是正常的**，因为 DNS 还没配（见第五节）
3. 等 DNS 生效、证书签发后，勾选 **Enforce HTTPS**

---

## 五、DNS 配置（由域名持有人操作，不是我们这边）

`ip4sme.info` 的 DNS 在新网（`ns11/ns12.xincache.com`），需要在域名管理后台加一条记录：

| 记录类型 | 主机记录 | 记录值 | TTL |
|---|---|---|---|
| CNAME | `ipzc` | `<你的用户名>.github.io` | 600 |

要点：
- 主机记录只填 `ipzc`，不要填 `ipzc.ip4sme.info`，也不要加 `http://`
- 记录值末尾**不要加点**，填 `<你的用户名>.github.io`
- 如果服务商不支持 CNAME 扁平化或坚持用 A 记录，可改用 GitHub Pages 的四个 A 记录：
  `185.199.108.153`、`185.199.109.153`、`185.199.110.153`、`185.199.111.153`
  （但这样会失去"跟随 GitHub 容灾切换"的好处，优先用 CNAME）

---

## 六、验证

DNS 生效后（通常 10 分钟到几小时），执行：

```bash
nslookup ipzc.ip4sme.info
curl -I https://ipzc.ip4sme.info
```

期望：解析到 `<你的用户名>.github.io`，HTTPS 返回 `200`。

如果浏览器报 `ERR_CERT_COMMON_NAME_INVALID` 或证书错误，说明 GitHub 的证书还在签发中，等 1–24 小时即可。

---

## 七、后续换内容

直接改 `index.html` 再上传覆盖即可，Pages 会自动重新发布。首页里标了 `TODO` 的地方是待替换的真实信息（联系邮箱等）。
