# videoccc

从一句需求，到分镜图、可编辑动画、本地配音、同步字幕和高清 MP4。

这是一个 **仅包含一个项目级 Codex skill** 的仓库，不是 Web 应用、Agent SDK 或模型发行包。技能位于 [`.agents/skills/videoccc/SKILL.md`](.agents/skills/videoccc/SKILL.md)，只在此项目及其子目录内由 Codex 发现，不安装到用户级技能目录。

## 使用

在此仓库目录启动 Codex，输入：

```text
$videoccc 制作一段鹈鹕骑自行车的动画，使用甜美女声配音和中文字幕，高清 30 帧。
```

也可以要求只修改已有视频的分镜、背景、配音或字幕，技能会复用可用产物，不默认整条重做。

默认分工：

- **分镜 Agent**：Codex CLI + 当前账号可用的最新 GPT Sol，规划并调用实际生图工具生成分镜图。创建时核实的基线是 `gpt-6.1-sol`，运行时重新核验，不把“latest”当成模型 ID。
- **执行 Agent**：全局安装的 `deepccc` 包提供的 `deepccc-cli`，本次调用显式选择 `deepseek-v4-flash`，继承其全局连接配置，不修改全局设置。
- **本地声音与字幕**：优先复用本机 Qwen3-TTS 等语音模型，以及本地 ASR / 强制对齐模型。不能用文本长度分配冒充准确语音对齐。
- **渲染与验收**：从动画源文件直接渲染全彩帧，以 FFmpeg 合成视频。默认高清、30 fps；不经过 GIF 中转。

需要本机可用的 Codex CLI、生图能力、全局 DeepCCC CLI、FFmpeg/ffprobe、独立 Python 环境及本地语音/对齐模型。仓库不自动安装这些依赖，也不内置 API Key；实际制作时先预检。

## 私人制作目录

所有输入、成品、中间帧、脚本、日志、模型缓存和本机配置都写入 **`private/`**，整个目录已被 `.gitignore` 排除：

```text
private/
  local-config.json          # 本机路径和运行偏好；不是必需的全局配置
  runs/<run-id>/
    brief.json
    storyboard/
    source/
    audio/
    subtitles/
    frames/
    output/
    logs/
    manifest.json
```

新克隆的仓库不包含 `private/`，首次使用时创建即可。不要用 `git add -f` 提交其内容；`.gitignore` 不是加密、访问控制或云端上传授权。

可选 `private/local-config.json` 可以记录本地 TTS Python、模型目录、已选参考音频、ASR 运行方式、Chrome CDP 地址等。缺失时检查当前工具与环境，不把其他机器的绝对路径当成默认值。认证沿用 CLI 自身配置或环境变量，不复制到制作说明或 Git。

## 边界

默认走 SVG / HTML / Canvas / Manim 等 **代码动画渲染路线**，适合插画动画、图解和产品演示，不声称具备任意实拍级视频生成能力。图像生成服务由实际生图工具提供，Sol 是分镜设计与工具调用的 Agent，不是原生图像输出模型。

本仓库不包含示例视频、模型权重、业务素材或应用框架。Git 提交和外部分享不是视频制作的隐含步骤，需要用户另行提出。

项目级技能发现规则参考 [Codex 官方文档](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills)。
