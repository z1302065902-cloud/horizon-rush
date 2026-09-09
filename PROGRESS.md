# Candy Hop 视觉升级进度日志（2026-09-08 暂停，明天续传）

## 当前状态
源码：`~/Desktop/CandyHop/index.html`（43826 字节，760 行）
**已完成 8 项视觉升级，未完成测试与重新部署。明天从"自动化测试"开始。**

## 已完成（今日全部改完，括号平衡已校验）
1. [✓] 光照升级：加 HemisphereLight + 弱 AmbientLight（原仅 Directional）
2. [✓] 粒子系统：60 个浮动白色粒子（PARTICLE_COUNT=60，animate 里上浮+闪烁）
3. [✓] 角色升级（兔/熊/猫/猪全部重写）：腮红+眉毛+脚垫+瞳孔+眼睛高光+呼吸 phase
   - 兔 8部件→19部件（白耳内+白眼白+腮红+眉毛）
   - 熊 7→16（耳内粉+口鼻浅色区+鼻+眼高光+脚垫）
   - 猫 8→20（耳内粉+竖瞳+胡须+长尾巴TubeGeometry）
   - 猪 8→16（floppy耳+圆鼻+鼻孔+卷尾巴Tube+蹄子）
4. [✓] 平台升级：2 层地面(土+草顶) + 每平台顶部亮色条
5. [✓] 装饰升级：树木(树干cylinder+树冠sphere) + 灌木球，替代随机方块
6. [✓] 敌人升级：蘑菇(Group: 菌柄+菌盖+白点+眼睛) + 机器人(Group: 天线+发光眼睛+轮子)
7. [✓] 动画增强：金币z轴摆动+道具x轴旋转+树木摇曳+粒子上浮
8. [✓] 呼吸动画：animate 中 playerMesh.children[0] 按 breathePhase 缩放（不影响物理）

## 未完成（明天任务清单）
- [ ] **关键**：游戏自动化测试（PC+手机各 50 局，用 /tmp/test_final.mjs 改造成本版）
- [ ] **注意**：升级改动后需重新验证 3 关都能通关、角色能正常落地
- [ ] 测试通过后重新打包 /tmp/candy-hop.zip（含 index.html + models3d/）
- [ ] 重新部署：GitHub → Vercel → GitHub Pages → itch.io → 爱发电（全部需要更新为新版本）

## 调试中发现的线索（明天先看这个！）
- 升级版跑 Playwright 时玩家**掉出世界**：Move pos 到 y=-3（正常应在 y=0.3 平台面）
- 初步怀疑：`updatePlayer` 里 `playerMesh.position.y+=player.velY*dt` 与 `py` 预计算重复应用位移
  - 现状：先算 `py=playerMesh.position.y+player.velY*dt` 用于碰撞判断，碰撞后再执行
    `playerMesh.position.y+=player.velY*dt` 又加一次 → 每帧位移 ×2？
  - 实际上：velY 在碰撞时被置 0，只有"未碰撞帧"会双加。需要明天仔细核对此处逻辑
  - 对比记忆：**上一版（未升级前）PC/手机各 50/50 通关正常**，说明 onAnyPlatform 逻辑本身没问题，
    问题可能出在：新加的平台顶部亮条(top条)加了 world.add(top) 不影响碰撞；
    但**地面从 1 层变 2 层**（ground 高度 1.5 + grassTop 0.15）——碰撞代码只查 platforms，
    不查 ground mesh，玩家理论上不该受影响……明天用 console.log 逐帧验证
- 另一线索：`page.evaluate(()=>LEVELS)` 报 ReferenceError（LEVELS 是 const，不在 window 上），
  测试脚本要用 `window.__game` 钩子拿数据，不能直接访问 LEVELS

## 关键调试钩子（不变）
`window.__game = {start, state, pos, parts, jump, key, teleport, pick}`
- parts() 现应返回：兔19/熊16/猫20/猪16（子部件数变了！旧断言 8/7/8/8 已失效）

## 测试环境
- 本地服务器：`cd ~/Desktop/CandyHop && python3 -m http.server 8765 &`
- 测试 URL：`http://localhost:8765/index.html?noaudio=1`（headless 无音频必须带 noaudio）
- 测试脚本基础：/tmp/test_final.mjs（PC+手机各 50 局版本，需适配新 parts 数）

## 部署链接（更新前勿动）
- GitHub: https://github.com/z1302065902-cloud/horizon-rush
- Vercel: https://candy-hop-game.vercel.app
- Pages: https://z1302065902-cloud.github.io/horizon-rush/
- itch.io: https://zsy2026.itch.io/horizon-rush
- 爱发电: https://afdian.com/item/6f3448f4ab8f11f1b5665254001e7c00

## 工具与凭据
- itch 发布：`node ~/.game-factory/tools/itch-publish.mjs <slug> <title> <desc> <zip> [cover]`
- 爱发电 cookie：/tmp/afdian_cookies.json（auth_token 有效至 2027）
- 测试脚本：/tmp/test_final.mjs

## 今日已改代码位置索引（新文件行号）
- 光照+粒子：~line 70-90
- createRabbit：line 85 起（19部件）
- createBear：line 140 起（16部件）
- createCat：line 190 起（20部件）
- createPig：line 252 起（16部件）
- buildLevel 地面/平台/装饰/敌人：line 379-480
- animate 动画增强：line 609-700
- updatePlayer 碰撞：line 518-536（**明天重点排查处**）
