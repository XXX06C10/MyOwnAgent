# MyOwnAgent 使用指南

## 环境要求

- Python 3.10+
- 虚拟环境路径：`d:/SWE_Agent/.venv`

## 快速开始

### 1. 设置 API Key

```bash
export DEEPSEEK_API_KEY="sk-your-key-here"
export OPENAI_API_KEY="sk-your-key-here"
```

支持所有 litellm 兼容的模型（OpenAI / Anthropic / DeepSeek 等）。

### 2. 运行

```bash
cd d:/SWE_Agent/MyOwnAgent

PYTHONPATH="d:/SWE_Agent/MyOwnAgent/src" \
  d:/SWE_Agent/.venv/Scripts/python.exe \
  -m minisweagent.run.cli "你的任务描述" -m deepseek/deepseek-chat
```

### 3. 查看帮助

```bash
PYTHONPATH="d:/SWE_Agent/MyOwnAgent/src" \
  d:/SWE_Agent/.venv/Scripts/python.exe \
  -m minisweagent.run.cli --help
```

## 参数说明

| 参数 | 简写 | 默认值 | 说明 |
|------|------|--------|------|
| `TASK` | 位置参数 | 必填 | 要 agent 完成的任务 |
| `--model` | `-m` | `gpt-4o` | LLM 模型名称 |
| `--step-limit` | `-s` | `30` | 最大执行步数 |
| `--cost-limit` | `-c` | `10.0` | 费用上限（美元） |
| `--output-dir` | `-o` | `./trajectories` | 轨迹保存目录 |

## 示例

```bash
# 列出文件
PYTHONPATH="d:/SWE_Agent/MyOwnAgent/src" \
  d:/SWE_Agent/.venv/Scripts/python.exe \
  -m minisweagent.run.cli "列出当前目录下的所有文件" -m deepseek/deepseek-chat

# 统计代码行数
PYTHONPATH="d:/SWE_Agent/MyOwnAgent/src" \
  d:/SWE_Agent/.venv/Scripts/python.exe \
  -m minisweagent.run.cli "统计 src 目录下所有 .py 文件的总行数" -m deepseek/deepseek-chat

# 限制步数
PYTHONPATH="d:/SWE_Agent/MyOwnAgent/src" \
  d:/SWE_Agent/.venv/Scripts/python.exe \
  -m minisweagent.run.cli "创建文件并写入 Hello World" -m deepseek/deepseek-chat -s 5
```

## 运行测试

```bash
d:/SWE_Agent/.venv/Scripts/python.exe -m pytest d:/SWE_Agent/MyOwnAgent/tests/ -v
```

## 工作流程

```
MODEL（模型思考下一步）
  → PARSE（严格解析出恰好 1 个命令，否则 FormatError）
    → EXECUTE（本地 Shell 执行）
      → OBSERVE（结果反馈给模型，循环直至完成）
```

- 命令格式：`` ```mswea_bash_command ... ``` `` 或 `<mswea_bash_command>...</mswea_bash_command>`
- 完成标志：执行 `echo COMPLETE_TASK_AND_SUBMIT_FINAL_OUTPUT` 且 returncode = 0
- 每步只能输出 1 个命令，多了会被拒绝并要求重试
- 运行结束后轨迹保存至 `trajectories/trajectory.json`
