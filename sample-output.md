# 出海执行方案：宠物拟人证件照工具

## 1) 定位复述与做减法建议

**定位复述**：宠物照片拟人证件照生成工具，将宠物照片转换成人类证件照风格，目标用户为海外宠物主人，通过Gumroad以数字产品形式销售，单价建议$4.99。

**做减法建议**：
- 核心功能：证件照生成（保留最核心的拟人化效果）
- 可暂缓功能：多种证件照模板、多尺寸导出、社交分享功能
- 保留功能：基础证件照生成、单次生成预览、下载功能
- 简化输入：只需一张宠物正面照，减少复杂场景选择

## 2) 本阶段执行清单（防白嫖体系搭建）

### 防白嫖规则设计
1. 确定兑换码规则：
   - 一人一码，每码**3次**，成功生成证件照才扣次，失败不扣
   - 全站每日熔断**100次**（免费额度天花板）
   - 单IP每日**5次**
   - 首批生成300个码，格式`PET-XXXXXX-XXXXXX`

2. 创建兑换码表：
   - 生成300个兑换码并保存到本地文件
   - 对每个码计算SHA-256哈希值
   - 将哈希值存储到代码中，明文码表只存本地文件

### 技术实现
3. 在Cloudflare Worker中实现三个接口：
   - `/check-code`：检查兑换码有效性和剩余次数
   - `/analyze`：生成证件照（核心功能接口）
   - `/status`：显示当日剩余次数

4. 实现防刷逻辑：
   - 前端调用前先进行`/check-code`检查
   - 后端记录每个兑换码的使用次数和IP
   - 添加IP频率限制和每日总量限制

5. 前端优化：
   - 图片在前端压缩到≤1024px
   - 添加等待提示，避免用户重复提交
   - 界面明确显示剩余次数

## 3) 本阶段完成标准

- 兑换码规则已确定并文档化
- 已生成300个兑换码及其哈希值，明文码表保存在本地
- `/check-code`接口能准确验证兑换码并返回剩余次数
- `/analyze`接口能根据宠物照片生成拟人证件照
- `/status`接口能显示当日剩余总量
- 前端能正确显示兑换状态和等待提示
- 单码第4次使用被拒绝，无码/假码被拒绝
- 全站达到100次上限后新请求被拒绝
- 单IP每日超过5次后新请求被拒绝

## 4) 避坑提醒（防白嫖阶段）

- **哈希存储安全**：兑换码的哈希值不要写进代码仓库，只存在于服务端Secret中；明文码表不要进Git，只存在本地文件
- **免费额度预警**：在页面显眼位置提示"今日剩余X次"，避免用户反复提交浪费资源
- **错误处理**：兑换码无效时提供明确错误信息，避免用户困惑
- **降级策略**：当AI模型达到限流时，显示明确等待提示，不要让用户以为系统出错
- **兑换码格式**：确保兑换码格式包含足够随机性，避免被暴力破解
- **前端验证**：前端只做提示性验证，核心验证必须放在后端接口

## 5) 下一步与要准备的材料

### 下一步
完成防白嫖体系搭建后，进入Gumroad开店阶段：
1. 注册Gumroad账号（选择国家）
2. 设置PayPal收款方式
3. 创建商品并配置License Key
4. 编写商品文案和说明

### 需要准备的材料
1. **宠物证件照样例**：用不同宠物照片生成的证件照样例图（3-5张）
2. **兑换码表**：保存的明文兑换码表（不要上传代码仓库）
3. **文案准备**：
   - 商品描述（使用Gumroad模板）
   - License Key使用说明（使用模板）

### Gumroad Description模板（英文填充版）

```
What you get
• 3 pet portrait generations — one license key = 3 creations
• A human-style ID photo of your pet (formal attire background)
• High-resolution digital download (suitable for printing or sharing)
• A ready-to-post share line with your pet's new look

How it works
1. Checkout — your license key is delivered instantly on the receipt page and by email.
2. Visit <你的英文版网址> and upload a clear front-facing photo of your pet.
3. Paste your license key and hit generate — your pet's ID photo is ready in about a minute.

Please read before buying
• Generations are first-come, first-served. Daily capacity resets at 00:00 (UTC+8), and you can check today's remaining capacity on our site before you generate.
• If today's capacity is full, your key is NOT charged a use — your 3 uses stay yours, just come back tomorrow.
• Your pet's photo is used only to generate the portrait and is never stored or shared.
• This is for entertainment purposes only. Results may vary based on photo quality.
• Key not working, or something went wrong? Message us through Gumroad — we'll fix it or refund you.
```

### Content页key使用说明模板（填充版）

```
How to use your license key
1. Go to <你的英文版网址>
2. Upload a clear front-facing photo of your pet.
3. Paste the license key from this page and hit Generate — your pet's ID photo is ready in about a minute.
One key = 3 generations. Any problem? Message us through Gumroad and we'll fix it or refund you.
```