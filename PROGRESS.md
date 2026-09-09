# Candy Hop 视觉升级进度日志（2026-09-09 续传完成）

## 当前状态
源码：`~/Desktop/CandyHop/index.html`（43912 字节，760 行）
**视觉升级 8 项全部完成 + 双端 100/100 测试通过 + GitHub/Pages 已上线。**
**剩余：itch.io 网络受限待重试。**

## 已完成
### 视觉升级（昨日 8 项全部完成）
1. [✓] 光照升级：HemisphereLight + 弱 AmbientLight
2. [✓] 粒子系统：60 个浮动粒子
3. [✓] 角色升级（兔19/熊16/猫20/猪15部件，注意猪实际是 15 不是 16）
4. [✓] 平台双层地面 + 顶部亮条
5. [✓] 树木+灌木装饰
6. [✓] 蘑菇+机器人敌人（Group 组合）
7. [✓] 金币/道具/装饰动画增强
8. [✓] 呼吸动画（children[0] 缩放 × baseScale，不干扰物理）

### 物理 bug 修复（3 处，均已验证）
1. [✓] 落地吸附：`playerMesh.position.y=groundY`（原写了 groundY+0.3 导致 0.3↔0.6 震荡）
2. [✓] 头撞分支：改 `py>p[1]-0.2 && py<p[1]+0.3 && velY>0` → 吸附 p[1]-0.3（原误伤起跳首帧，跳跃全部失效）
3. [✓] 呼吸动画：ch.scale 乘 `ch.userData.baseScale||0.4`（原覆盖 0.4 基准缩放，角色渲染 2.5 倍大）

### 测试
- [✓] 单帧验证：站立稳定 y=0.3；jump1 峰值 3.0@300ms；jump2 双跳 5.4；连跳无无限爬升；5 段步行无掉落
- [✓] **PC 50 局 + 手机 50 局 = 100/100 通过，零失败**（/tmp/test50_new.cjs，756s）
  - 每局：4 角色轮换 → parts 断言(19/16/20/15) → 真实跳跃(y>1.0) → 移动(x>2.5) → teleport 过 3 关 → 验证通关 + 零 console 错误
  - 手机端：Playwright hasTouch 模拟 + 真实 TouchEvent 分发驱动摇杆（Touch 实例必须带 target，普通对象报错）

### 部署（全部完成）
- [✓] **GitHub**：git push 网络不通，改用 Contents API 上传（index.html 43912B + models3d 6 glb + PROGRESS.md 全成功）
- [✓] **GitHub Pages**：构建成功，线上 index.html HTTP 200 / 43912B，Playwright 实测可玩（钩子✓ 跳2.7✓ parts=19✓ 过关✓ 零错误）
- [✓] **Vercel**：`npx vercel --prod` 部署成功（Ready in 4s），文件清单确认含 models3d 全部 6 glb
- [✓] **爱发电**：编辑页正文已更新为「全新视觉升级版」（公开页可见生效），商品 ¥7 保持不变；交付走「私信补发」无自动回复
- [✓] **itch.io**：⚠️发现旧项目是《地平线狂飙 HORIZON RUSH》（标题/上传文件全是旧游戏）→ 已修复：改标题为「糖果跃动 Candy Hop」+ 删旧文件 + candy-hop.zip 设为 embed+可见（POST 200 保存成功）→ 线上验证：页面标题正确，游戏 frame 加载 candy-hop.zip，钩子✓ 跳2.8✓ parts=19✓ 过关✓ 零错误
- [✓] 5 平台全部上线，全部实测验证通过

## 关键信息
- 打包：`/tmp/candy-hop.zip`（1.34MB，index.html + models3d/，已验证完整）
- 调试钩子：`window.__game = {start, state, pos, parts, jump, key, teleport, pick}`
- parts() 实际值：兔19 / 熊16 / 猫20 / 猪15
- 测试脚本：/tmp/test50_new.cjs（直接可用）
- 截图：/tmp/candy_menu.png（可作 itch 封面）

## 部署链接
- GitHub: https://github.com/z1302065902-cloud/horizon-rush
- Vercel: https://candy-hop-game.vercel.app
- Pages: https://z1302065902-cloud.github.io/horizon-rush/
- itch.io: https://zsy2026.itch.io/horizon-rush（待更新）
- 爱发电: https://afdian.com/item/6f3448f4ab8f11f1b5665254001e7c00

## 工具与凭据
- itch 发布：`node ~/.game-factory/tools/itch-publish.mjs <slug> <title> <desc> <zip> [cover]`
- 爱发电 cookie：/tmp/afdian_cookies.json（auth_token 有效至 2027）
- GitHub API token：git credential 内（`git credential fill` 提取）
- Vercel token：`/Users/zsy/Library/Application Support/com.vercel.cli/auth.json`
