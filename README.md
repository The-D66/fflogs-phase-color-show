# FFLogs 精确百分位显示

一个Tampermonkey脚本，用于在FFLogs的伤害统计页面中显示精确的百分位数据。

## 功能特点

- 在FFLogs的伤害统计页面中添加精确百分位列
- 根据不同的副本和阶段自动选择对应的CSV数据源
- 支持缓存CSV数据，减少网络请求
- 自动适应页面变化，确保百分位列始终显示
- 支持中英文职业名称
- 根据百分位值显示不同颜色

## 安装方法

1. 安装[Tampermonkey](https://www.tampermonkey.net/)浏览器扩展
2. 点击Tampermonkey图标，选择"添加新脚本"
3. 将脚本内容复制粘贴到编辑器中
4. 保存脚本

或者直接从[GreasyFork](https://greasyfork.org/scripts/your-script-id)安装。

## 使用方法

1. 安装脚本后，访问FFLogs的伤害统计页面
2. 如果页面包含phase参数，脚本会自动添加百分位列
3. 百分位列会显示在表格的最左侧
4. 百分位值会根据不同的区间显示不同的颜色

## 数据来源

脚本从[ITX351/fflogs_phase_ranker](https://github.com/ITX351/fflogs_phase_ranker)获取dps数据，感谢该项目提供的数据支持。

## 注意事项

- 脚本仅在伤害统计页面（包含 `type=damage-done`的URL）生效
- 脚本需要访问GitHub raw文件，请确保网络连接正常
- 首次加载可能需要一些时间，因为需要下载CSV数据

## 许可证

本项目采用[MIT许可证](LICENSE)。

## 贡献

欢迎提交问题和改进建议！请访问[GitHub仓库](https://github.com/The-D66/fflogs-phase-color-show)提交Issue或Pull Request。
