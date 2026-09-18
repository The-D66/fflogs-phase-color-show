# FFLogs 精确百分位显示

> ⚠️ **AI 修改说明**：本仓库由 AI（WorkBuddy）基于 [The-D66/fflogs-phase-color-show](https://github.com/The-D66/fflogs-phase-color-show) 原仓库修复并改进。**原脚本已过时，在 FFLogs 7.5x（7.51 妖星乱舞）版本的伤害统计页面下无法正常工作**（表格能显示但百分位数字缺失/错误，且默认取的是国际服数据），本版本已修复使其恢复可用。
>
> - **推送原因**：原脚本过旧，在当前 FFLogs 7.5x 版本下无法使用。
> - **参考来源 / 数据来源**：百分位数据来自 [ITX351/fflogs_phase_ranker](https://github.com/ITX351/fflogs_phase_ranker)（见下文「数据来源」）。修复基于对数据源组织方式的分析：数据按版本目录（`v71`/`v72`/…/`v751`）组织，各版本用 `config.json` 的 `raidMatchNames` + `raidLogsPhase` 映射到具体 CSV，并以各 `config.json` 的 `datasetName` 为准确认 **`j` = 国际服、`z` = 国服**。

一个 Tampermonkey 脚本，用于在 FFLogs 的伤害统计页面中显示精确的百分位数据。

## 功能特点

- 在 FFLogs 的伤害统计页面中添加精确百分位列
- 根据不同的副本和阶段**自动选择对应的 CSV 数据源**（动态选源，不再写死 7.1）
- **默认使用国服数据**（可在脚本 `PREFERRED_REGION` 中改为国际服）
- 仅在**具体分P 页面**（URL 含 `phase=数字`）显示百分位；**ALL Phases（`phase=all`）下不显示**分P百分数
- 百分位列独立表头：国服显示 `CN_logs`，国际服显示 `EN_logs`，并继承 FFLogs 原生 UI 风格
- 支持缓存 CSV 数据，减少网络请求
- 自动适应页面变化，确保百分位列始终显示
- 支持中英文职业名称
- 根据百分位值显示不同颜色

## 安装方法

1. 安装 [Tampermonkey](https://www.tampermonkey.net/) 或者 [ScriptCat](https://docs.scriptcat.org/) 浏览器扩展
2. 点击 Tampermonkey 图标，选择"添加新脚本"
3. 将脚本内容复制粘贴到编辑器中
4. 保存脚本

或者直接从 [GreasyFork](https://greasyfork.org/zh-CN/scripts/531958-fflogs-%E6%B7%BB%E5%8A%A0%E7%B2%BE%E7%A1%AE%E7%99%BE%E5%88%86%E4%BD%8D%E6%98%BE%E7%A4%BA) 安装（推荐分发渠道），也可从 [ScriptCat](https://scriptcat.org/zh-CN/script-show-page/7414) 安装。

## 使用方法

1. 安装脚本后，访问 FFLogs 的伤害统计页面
2. 如果页面包含具体的 phase 参数（如 `phase=3`），脚本会自动添加百分位列
3. 百分位列会显示在表格的最左侧
4. 百分位值会根据不同的区间显示不同的颜色

## 数据来源

脚本从 [ITX351/fflogs_phase_ranker](https://github.com/ITX351/fflogs_phase_ranker) 获取 dps 数据，感谢该项目提供的数据支持。

## 主要修复（v0.5 ~ v0.21，由 AI 完成）

1. CSV 数据源路径修正（补 `v71/` 子目录，原 404 导致无数据）
2. 职业名匹配标准化（兼容 `BlackMage` ↔ `Black Mage`）
3. 动态选择数据源（按副本名 + 分P + 区服匹配对应版本 CSV，不再写死 7.1）
4. 默认使用国服数据（修正 `j`/`z` 含义理解反的问题）
5. ALL Phases（`phase=all`）下不再显示分P百分数
6. 为百分位列补独立表头，修复原表头整体错位
7. 表头文案 `CN_logs` / `EN_logs` 并继承 FFLogs 原生 UI 风格
8. 修复 TDZ 诊断日志位置错误（v0.14）：原误放在 `rdps` 声明前，导致 `Cannot access 'rdps' before initialization`、整列构建中断、表现为逐行加载
9. 整页仅解析一次数据源 + 并行拉取各版本 config（v0.15），消除「逐行加载 / 非常慢」
10. 补齐 `@connect cdn.jsdelivr.net`（v0.16）：切换镜像源时不再因跨域确认永久卡在「加载中」（并默认优先走 jsDelivr 镜像，国内更快更稳）
11. 副本名优先取 `document.title`（v0.17），避免误匹配页面上其它副本引用（如把 Futures Rewritten 当当前副本，拉错伊甸数据）
12. 清理死代码 + 元数据整份缓存 localStorage（v0.18），减少重复联网
13. 修复 SPA 内切换副本的缓存污染（v0.19）：按「报告 + 战斗 + 分P」上下文失效缓存，避免用旧副本数据算新副本的 dps 颜色（原作者评审建议）
14. 修正与事实相反的 `j`/`z` 注释（v0.20）：原注释写「j=国服, z=国际服」，与实际相反，已改正以免后续维护者把区服逻辑改反
15. 修复 v72 数据源静默丢失（v0.20）：`fetchVersionConfig` 原硬编码 `config.json`，而 v72 实际文件是 `config_glb.json`；现按 `config_file_list.json` 的 `fileNames` 取值，且拉取失败不再写入 24h 元数据缓存
16. 恢复 GreasyFork 自动更新（v0.20）：`@updateURL` / `@downloadURL` 原来缺脚本 ID，已改为含 `531958` 的 `update.greasyfork.org` 权威地址
17. 提示文案中的版本号改为引用 `SCRIPT_VERSION` 常量（v0.20），不再写死而过期
18. 所有 `console.log` 收口到受 DEBUG 开关控制的 `log()`（v0.20），轮询 / MutationObserver 不再刷屏（`warn`/`error` 保留常开以便排查）
19. 缓存键与 DOM 版本标记统一为 v20（v0.20），并清理历史遗留缓存键；`extractEncounterName` 中两段重复的副本名匹配逻辑提取为 `matchEncounter()`
20. 索引与配置文件也走「镜像优先 + 主源兜底」（v0.21）：此前只有 CSV 走镜像，`raw.githubusercontent.com` 被墙时整个脚本会无数据
21. 无 `j`/`z` 后缀的旧目录按 `datasetName` 推断区服（v0.21）：`v72`/`v73` 实为国际服、`v71`/`v725`/`v738` 为国服，此前被当成「任何区服通用」，国服用户可能拿到国际服数据
22. 缓存键与 DOM 标记统一由 `SCHEMA_VERSION` 常量派生（v0.21），与脚本版本解耦，避免每次发版都让用户重新下载一遍 CSV

## 真实验证结果（2026-09-18，国服页面实测）

用一个临时监控脚本（未随本 PR 提交）记录浏览器里的实际表现，同时独立按数据源重算期望值，逐人比对：

| 报告（已脱敏） | 副本 | 场次 / 分P | 结果 |
|---|---|---|---|
| `G3J2************` | 妖星乱舞 Dancing Mad | fight 2 P5、fight 5 P5、fight 5 P3 | 24/24 逐位一致 |
| `B91P************` | 龙诗 Dragonsong's Reprise | fight 6 P8、fight 11 P8 | 16/16 逐位一致 |
| `j2LB************` | 欧米茄 The Omega Protocol（外服报告） | fight 12 / 13 / 18 P6 | 24/24 逐位一致 |

累计 **64/64 百分位逐位一致**，**控制台 0 报错**（日志中出现的 4 条是 FFLogs 自家 boss 头像图片 404，与本脚本无关）。

同时确认：
- 表头恒为 `CN_logs`，国服数据生效
- 不刷新页面切换战斗 / 分P，百分位会重算，耗时 1.1 ~ 2.7 秒，**不会串用上一个副本的数据**（原作者指出的缓存污染问题）
- 切到 ALL Phases（`phase=all`）时百分位列自动移除
- 副本名识别在 3 个副本上均正确（含报告标题与战斗名不一致的情况）
- 旧副本（龙诗、欧米茄）取到的均为 `z` 国服目录的数据表

已知事项（不影响使用）：
- 表格最下方「合计」行会多一个 `-` 单元格：保留它可避免该行整体列错位
- `PREFERRED_REGION = 'z'` 是固定偏好，外服报告同样按国服排名比对；需要国际服请改为 `'j'`
- `inferRegion()`（无 `j`/`z` 后缀旧目录按 `datasetName` 推断区服）目前只有单元测试覆盖（6/6 通过），上述实测都命中了带 `z` 后缀的最新目录，尚未在真实页面触发该分支

## 注意事项

- 脚本仅在伤害统计页面（包含 `type=damage-done` 的 URL）生效
- 脚本需要访问 GitHub raw 文件，请确保网络连接正常
- 首次加载可能需要一些时间，因为需要下载 CSV 数据

## 许可证

本项目采用 [MIT 许可证](LICENSE)。

## 贡献

欢迎提交问题和改进建议！请访问 [GitHub 仓库](https://github.com/The-D66/fflogs-phase-color-show) 提交 Issue 或 Pull Request。
