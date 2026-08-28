# Learning Platform Points（学习平台积分）

[English](README.md) | **简体中文**

一个可移植的 Agent Skill，用于高效操作已获授权的在线学习平台，同时遵守平台真实的完成条件、出勤要求和风控规则。

## 功能

- 继续当前课程，或选择符合条件且预计用时最短的未完成课程。
- 使用正常界面中可见的最高播放倍速。
- 在平台允许此类自动化时，处理普通且可见的继续学习提示。
- 等待有效学习倒计时结束并彻底消失后，才进入下一项。
- 在已获自动提交授权的前提下，完成平台允许的低风险课程测验。
- 核验服务端可见的任务数和积分变化，并为积分延迟入账或会话中断保存检查点。

## 语言支持

本 README 提供英文和简体中文版本；Skill 指令与参考文件目前仍以英文维护。

运行流程不依赖特定界面语言：它会发现可见标签并记录到平台配置中，而不是依赖固定短语列表。本仓库中的具体选择器和标签示例来自一个简体中文的云学堂类平台；其他语言和平台必须重新验证配置。此 Skill 不负责翻译课程内容或考试题目。

## 安全边界

用户授权控制浏览器会话，并不自动代表平台或所属组织允许自动化操作。

此 Skill 始终只保持一路课程媒体播放，不修改计时器、不注入完成状态、不使用界面未提供的倍速，也不尝试规避并发、身份、监考、出勤或风控机制。遇到验证码、短信验证码、人脸或语音验证、付款、法律声明确认或高风险考试时会停止。

## 安装

### WorkBuddy

1. 安装并连接 [Tencent BrowserSkill](https://github.com/Tencent/BrowserSkill)（`bsk` CLI 及其 Chrome 或 Edge 扩展）。
2. 将此文件夹作为 Skill 导入 WorkBuddy，并保持 `SKILL.md` 位于文件夹根目录。
3. 打开一个已经登录学习平台的标签页，然后用明确目标调用此 Skill，例如：

   > 继续我的已授权学习项目。仅使用正常界面中可见且允许的最高倍速，处理平台允许自动确认的普通继续学习提示，自动提交已明确获准的低风险课程测验；只有在没有符合条件的任务，或必须由我本人介入时才停止。

浏览器能力映射见 [`references/workbuddy-adapter.md`](references/workbuddy-adapter.md)。

### Codex 或其他支持 Agent Skills 的宿主

将此文件夹安装或复制到宿主的 Skills 目录，然后调用 `learning-platform-points` 执行已授权的学习任务。通用流程见 [`SKILL.md`](SKILL.md)。

## 仓库结构

```text
learning-platform-points/
├── README.md
├── README.zh-CN.md
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── assessment-workflow.md
    ├── platform-profile.md
    ├── recovery-and-checkpoints.md
    └── workbuddy-adapter.md
```

## 说明

- 平台专属选择器和积分规则必须根据可见证据确认，不得猜测。
- 本地化控件标签只是已观察到的提示，不是通用固定字符串。
- 公共资料可以帮助理解课程概念，但完成状态和积分仍以已登录平台显示的信息为准。
- 达到及格即可；除非再次考试确实是获得积分的必要条件，否则不重考。
