# 快捷键工坊 (shortcut-studio)

OpenRevo **Tab 型第三方插件**：可视化全局快捷键配置中心。在游戏独占全屏中随时按键切换 Mini 面板里的常用功能——强冷、性能模式、刷新率、Win 键锁等 16 种动作，无需打开控制中心。

**零宿主改动**，完全基于 OpenRevo 0.9.3 官方插件 API 开发（`docs/OpenRevo_第三方插件开发手册.md` 用例 11），兼容官方发行版。

## 功能

- 🎹 **组合键录制**：点击录制框直接按键捕获，也支持手动输入修正
- ⚡ **16 种动作**：强冷 / 性能模式（循环+直达）/ 刷新率 / 键盘背光 / 触控板 / Win锁 / Fn锁 / 息屏 / 锁屏 / 启动程序 / 音量 / 插件事件广播 等
- 🛡️ **冲突检测**：实时标出被多条绑定占用的组合键（宿主仅最先注册的生效）
- 🔄 **重启自动恢复**：自定义键保存在 localStorage，打开本插件页时自动重新注册
- 📤 **导出固化**：一键生成 manifest 片段，粘贴进本插件 manifest.json 后随宿主启动永久生效
- ▶️ **即时测试**：每条绑定可手动触发一次验证效果

## 安装

把 `shortcut-studio` 整个文件夹复制到：

```
%APPDATA%\OpenRevo\plugins\shortcut-studio\
```

最终结构：

```
%APPDATA%\OpenRevo\plugins\shortcut-studio\
├── manifest.json
├── index.html
└── README.md
```

重启 OpenRevo，在插件管理页启用后，顶部导航栏会出现「快捷键工坊」选项卡。

## 预置快捷键

| 组合键 | 动作 |
|---|---|
| `Ctrl+Shift+F` | 一键全速强冷 |
| `Ctrl+Shift+M` | 循环切换性能模式 |
| `Ctrl+Shift+R` | 刷新率瞬切（高刷/省电） |
| `Ctrl+Shift+W` | Win 键锁定切换 |
| `Ctrl+Shift+L` | 立即息屏 |

预置键随 OpenRevo 启动自动生效（宿主加载 manifest 声明式 `shortcuts`）。

## 持久化机制（重要）

宿主（0.9.3）的快捷键注册表是**纯内存**的，动态注册的快捷键重启即丢。本插件用三层设计弥补：

1. **预置层**：manifest.json 里声明的 `shortcuts`，随宿主启动自动注册 ✅
2. **恢复层**：自定义/改键保存在浏览器 localStorage，**打开本插件页时自动恢复注册**（重启后首次触发前需打开一次本页）
3. **固化层**：点「导出固化」生成 JSON，粘贴回 manifest.json 的 `shortcuts` 数组，保存后重新开关一次插件，即可完全开机自生效

## 已知限制

- 摄像头 / 麦克风 / 防休眠 / 显卡直连(MUX) / 电池限额：宿主快捷键总线（0.9.3）未提供对应 `ShortcutAction`，界面中已灰显标注
- `Win` 组合键录制偶尔会被系统钩子拦截，可直接在录制框手动输入
- 纯单键（无修饰键）绑定会拦截正常打字，已禁止录制（仍可手动输入，自担风险）
- 同一组合键冲突时，仅最先注册的一条生效

## 技术实现

- 沙盒 iframe 内运行，优先走宿主 `__OPENREVO_BRIDGE__`（独立视窗模式），否则走 Tab postMessage 桥（`GET_SHORTCUTS` / `REGISTER_SHORTCUT` / `UNREGISTER_SHORTCUT` / `TRIGGER_SHORTCUT_ACTION`）
- `telemetry.enabled: false`——本插件不需要硬件遥测，零开销
- 样式遵循 OpenRevo Design System Tokens（iframe 沙盒内本地镜像定义），暗色/透明背景与宿主一致
- 组合键解析逻辑与宿主 Rust 端 `ParsedKeyCombo::parse` 对齐（[src-tauri/src/system/shortcuts/types.rs](../src-tauri/src/system/shortcuts/types.rs)）

## License

GPL-3.0-or-later（随宿主仓库）
