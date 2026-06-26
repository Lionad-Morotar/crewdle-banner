# crewdle-banner

一个用于学习前端视觉还原的 Demo 项目，目标是复刻 [Crewdle.com](https://crewdle.com) 首页 Banner 的视觉效果。

项目着重练习暗色主题 landing page 的还原：顶部导航、主标题文案、CTA 按钮、提示卡片，以及核心的 3D 反射球体。

## Token 消耗

统计基于本项目在 Claude Code 中所有会话的 transcript（截至当前会话）。

| 指标 | 数值 |
| --- | --- |
| API 调用次数 | 715 |
| Input Tokens | 1,703,608 |
| Output Tokens | 304,997 |
| Cache Read Input Tokens | 88,179,778 |
| **合计** | **~90.2 M** |

## 效果对比

### Current Banner

![Current Banner](./assets/banner/current-fullpage.png)

### Target Banner

![Target Banner](./assets/banner/target-fullpage.png)
