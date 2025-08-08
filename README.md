# 个人代理规则集

这个仓库包含了个人定制的Clash代理规则集配置文件，支持自动节点选择和智能分流。

## 文件说明

### 主配置文件
- `foreverchen9.yaml` - 主要的Clash配置文件，包含完整的代理组和规则配置
- `foreverchen9_verge.yaml` - Clash Verge 增强版配置（支持 rule-providers 等）
  

### 规则集文件
- `foreverchen9_rules.yaml` - 自定义规则集，包含银行和金融服务相关的域名规则

## 如何使用

### 1. 直接使用主配置文件
将 `foreverchen9.yaml` 导入到Clash客户端中使用。

### 2. 引用远程规则集
其他配置文件可以通过以下方式引用本仓库的规则集：

```yaml
rule-providers:
  foreverchen9_rules:
    type: http
    behavior: classical
    format: yaml
    interval: 86400
    url: https://raw.githubusercontent.com/foreverchen9/personal_proxy_rules/main/foreverchen9_rules.yaml
    path: ./ruleset/foreverchen9_rules.yaml

rules:
  - RULE-SET,foreverchen9_rules,DIRECT
```

### 3. 使用模板创建新配置
复制 `proxy_template.yaml` 并根据以下步骤自定义：

1. 修改 `proxy-providers` 部分：
   - 将 `your_provider_name` 替换为您的代理服务商名称
   - 将 `YOUR_SUBSCRIPTION_URL_HERE` 替换为您的订阅链接

2. 更新 `proxy-groups` 中的 `use` 字段：
   - 将所有 `your_provider_name` 替换为实际的代理服务商名称

3. 根据需要调整规则优先级和代理组设置

## 配置特性

### 🚀 智能节点选择
- **自动选择**：基于延迟测试自动选择最优节点（容差10ms）
- **地区节点**：支持香港、台湾、日本、美国、英国、韩国、新加坡等地区
- **负载均衡**：所有地区节点组都采用url-test自动选择最优节点

### 🏦 银行金融规则集

`foreverchen9_rules.yaml` 包含以下银行和金融服务的规则：

**香港银行**：
- livi bank（理慧银行）
- WeLab Bank
- ZA Bank（众安银行）
- Airstar Bank（天星银行）
- BOCHK（中银香港）
- HSBC HK（汇丰银行香港）

**新加坡银行**：
- OCBC（华侨银行）
- iFast银行

**中国内地银行**：
- 兴业银行

### 添加新规则
在 `foreverchen9_rules.yaml` 的 `payload` 部分添加新规则：

```yaml
payload:
  - DOMAIN-SUFFIX,新银行域名.com
  - DOMAIN-KEYWORD,银行关键词
  - DOMAIN,完整银行域名.com
```

## 推送到GitHub

1. 修改规则集文件后，推送到GitHub：
```bash
git add .
git commit -m "更新银行规则集"
git push origin main
```

2. 规则集将在24小时内（默认86400秒）自动更新到引用此规则集的配置中

## 🎯 性能优化

### 节点选择策略
- **容差设置**：10ms容差，确保快速响应
- **测试间隔**：300秒（5分钟）定期测试节点延迟
- **自动切换**：延迟超过容差自动切换到更优节点

### 规则优先级
1. **银行金融规则**：最高优先级，确保银行服务稳定直连
2. **应用特定规则**：针对不同应用优化的代理策略
3. **地理位置规则**：基于IP地理位置的分流规则
4. **兜底规则**：未匹配规则的默认处理

## 📱 支持的客户端

### 推荐客户端
- **Clash for Windows**：完整功能支持
- **Clash for Android**：完整功能支持
- **Stash (iOS)**：优化了iOS设备的使用体验
- **OpenClash (OpenWrt)**：路由器端完整支持

### Stash专用配置
对于iOS用户，推荐使用以下链接：
```
https://raw.githubusercontent.com/foreverchen9/personal_proxy_rules/main/foreverchen9.yaml
```

#### 可选：在 Stash 中覆盖订阅地址（本地 Override）
1. 订阅上述公开链接，获得基础规则与分流。
2. 在 Stash 打开该配置，添加/编辑本地覆盖（Override）。
3. 写入如下覆盖片段（仅本地生效，不会同步到 GitHub）：
   ```yaml
   proxy-providers:
     fastfly:
       url: https://example.invalid/replace-with-your-private-subscription-url
   ```

或直接使用仓库内模板文件：`override.stash.example.stoverride`（复制为本地 `override.stash.stoverride`，填入你的真实地址后在 Stash 中导入）。
4. 保存后刷新订阅，节点与“订阅流量信息”将从你的真实订阅地址获取。

### Clash Verge 专用配置
用于 Clash Verge 的订阅链接：
```
https://raw.githubusercontent.com/foreverchen9/personal_proxy_rules/main/foreverchen9_verge.yaml
```

#### 可选：在 Clash Verge 中覆盖订阅地址（Mixin）
1. 订阅上述公开链接，得到基础配置与规则。
2. 在 Clash Verge → Profiles → 选中该配置 → Edit → 添加 Mixin（或本地补丁文件）。
3. 写入如下覆盖片段（仅本地生效，不会上传到 GitHub）：
   ```yaml
   proxy-providers:
     fastfly:
       url: https://example.invalid/replace-with-your-private-subscription-url
   ```

或直接使用仓库内模板文件：`override.clash-verge.example.yaml`（复制为本地 `override.clash-verge.yaml`，填入你的真实地址后在 Clash Verge 中导入为 Mixin）。
4. 保存后在 Providers 页面刷新，节点与“订阅流量信息”将读取你的真实订阅响应头。

备选方案：
- 使用 Secret Gist 或自托管（Vercel/Netlify/Cloudflare Pages/Nginx）私密托管你的订阅文件，再用该 URL 覆盖。
- 若需进一步防滥用，可在自托管端添加访问控制（Basic Auth、签名 URL、来源校验等）。

## 注意事项

1. **URL 修改**：如果您的GitHub用户名不是 `foreverchen9`，请修改配置文件中的GitHub URL
2. **分支名称**：确保使用正确的分支名称（通常是 `main` 或 `master`）
3. **文件路径**：确保GitHub仓库中的文件路径正确
4. **格式验证**：推送前请验证YAML文件格式的正确性
5. **节点兼容性**：确保您的代理服务商支持所需的地区节点

## 🔧 代理组说明

### 主要代理组
- **🚀 节点选择**：主选择器，包含所有可用的代理选项
- **♻️ 自动选择**：基于延迟测试自动选择最优节点（容差10ms）
- **🔧 手动切换**：手动选择特定节点，包含所有订阅节点

### 地区节点组（全部自动选择）
- **🇭🇰 香港节点**：自动选择延迟最低的香港节点
- **🇹🇼 台湾节点**：自动选择延迟最低的台湾节点
- **🇯🇵 日本节点**：自动选择延迟最低的日本节点
- **🇺🇸 美国节点**：自动选择延迟最低的美国节点
- **🇬🇧 英国节点**：自动选择延迟最低的英国节点
- **🇰🇷 韩国节点**：自动选择延迟最低的韩国节点
- **🇸🇬 狮城节点**：自动选择延迟最低的新加坡节点

### 应用特定代理组
- **📲 电报消息**：Telegram专用代理组
- **🤖 OpenAI**：OpenAI/ChatGPT专用代理组
- **📹 油管视频**：YouTube专用代理组
- **🎬 奈飞视频**：Netflix专用代理组
- **📱 Instagram**：Instagram专用代理组
- **🐦 Twitter**：Twitter专用代理组
- **💬 WeChat**：微信专用代理组（优先直连）

## 📝 更新日志

### v2.0 (最新)
- ✅ 添加韩国节点支持
- ✅ 所有地区节点改为自动选择模式（url-test）
- ✅ 优化延迟容差至10ms，提升响应速度
- ✅ 完善银行金融规则集
- ✅ 优化Stash客户端兼容性

### v1.0
- ✅ 添加了自定义规则集支持
- ✅ 创建了可复用的代理配置模板
- ✅ 优化了规则优先级设置
