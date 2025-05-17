[toc]

# [官方文档](https://docs.github.com/zh/actions)

```yml
https://docs.github.com/zh/actions
https://docs.github.com/zh/actions
```



# 配置

```yml
# 在项目的.github目录下新建一个目录、目录名字为：workflows 在目录下新建一个.yml
mkdir -p .github/workflows
cd .github/workflows
touch wuge.yml
```

# 基本结构

```yml
name 自动构建的名字
on 触发条件
schedule: # 时间表
cron: '0 19 * * *' # 每天国际时间 19 点，北京时间凌晨 3 点执行(北京+8)
watch # 监视
type: started # 类型：点击了星标
env 环境变量
jobs 任务
build 工作的 id
run-on 工作运行的环境平台
if 工作运行的判断
steps 包含一系列任务步骤
name 子任务名
user 使用官方的一些库完成一些操作
run 运行脚本
id 运行 id
```

## 自动构建的名字

```yml
name: Makefile CI
```

## 自动触发工作流

单个事件触发

```yml
on: [push]
```

多个事件触发

```yml
on: [push,fork]
```

在 `branches` 和 `tags` 中定义的模式：当以下项出现 `push` 事件时，将运行以下工作流

```yml
on:
  push:
    # 指定下列 分支
    branches:
      - main
      - 'mona/octocat'
      - 'releases/**'
    # 指定下列 tags
    tags:
      - v2
      - v1.*
```

在 branches-ignore 和 tags-ignore 中定义的模式：当以下项出现 `push` 事件时，将不运行以下工作流

```yml
on:
  push:
    # 排除下列 分支
    branches-ignore:
      - 'mona/octocat'
      - 'releases/**-alpha'
    # 排除下列 tags
    tags-ignore:
      - v2
      - v1.*
```

不能在一个工作流中使用 `branches` 、`tags` 和 `branches-ignore`  `tags-ignore` 筛选同一事件



`paths` 筛选器来包含哪些路径。目录或其子目录中的内容更改了，此示例就会运行

```yml
on:
  push:
    paths:
      - '**.js'
```

`paths-ignore:` 筛选器来排除路径。目录或其子目录中的内容更改了，此示例也不会运行

```yaml
on:
  push:
    paths-ignore:
      - 'docs/**'
```

`release:`筛查器来排查版本。版本状态更改了运行	

```yml
on:
  release:
    types: [published]
```

```yml
- published    	-出版
- unpublished 	-未发表	 --不触发工作流
- created				-创建		  --不触发工作流 会自动另存为草稿
- edited				-编辑		 --不触发工作流 会自动另存为草稿
- deleted				-已删除   --不触发工作流 会自动另存为草稿
- prereleased		-预发行   --不触发工作流
- released			-释放			--不触发工作流
```

 `schedule` 事件，可以在计划的时间触发工作流。

```yaml
on:
  schedule:
    - cron: "15 4,5 * * *"
```

计划任务语法有五个字段，中间用空格分隔，每个字段代表一个时间单位。

```yml
┌───────────── 分 (0 - 59)
│ ┌───────────── 时 (0 - 23)
│ │ ┌───────────── 一个月中的第几天 (1 - 31)
│ │ │ ┌───────────── 月 (1-12 或JAN-DEC)
│ │ │ │ ┌───────────── 星期 (0-6 或 SUN-SAT)
│ │ │ │ │
│ │ │ │ │
│ │ │ │ │
* * * * *
```

你可在这五个字段中使用以下运算符：

| 运算符 | 说明         | 示例                                                         |
| ------ | ------------ | ------------------------------------------------------------ |
| *      | 任何值       | `15 * * * *` 在每天每小时的每个第 15 分钟运行。              |
| ，     | 值列表分隔符 | `2,10 4,5 * * *` 在每天第 4 和第 5 小时的第 2 和第 10 分钟运行。 |
| -      | 值的范围     | `30 4-6 * * *` 在第 4、5 和 6 小时的第 30 分钟运行。         |
| /      | 步骤值       | `20/15 * * * *` 在第 20 分钟到第 59 分钟每隔 15 分钟运行一次（第 20、35 和 50 分钟）。 |

# 手动触发工作流

使用 `workflow_dispatch` 事件时，你可以在浏览器指定传递到工作流的输入

```yml
on:
  workflow_dispatch:
    inputs:
      logLevel:
        description: 'Log level'
        required: true
        default: 'warning'
        type: choice
        options:
        - info
        - warning
        - debug
      tags:
        description: 'Test scenario tags'
        required: false
        type: boolean
      environment:
        description: 'Environment to run tests against'
        type: environment
        required: true

jobs:
  log-the-inputs:
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "Log level: $LEVEL"
          echo "Tags: $TAGS"
          echo "Environment: $ENVIRONMENT"
        env:
          LEVEL: ${{ inputs.logLevel }}
          TAGS: ${{ inputs.tags }}
          ENVIRONMENT: ${{ inputs.environment }}
```

还可以在从脚本运行工作流程时传递输入，或者使用 GitHub CLI。 例如：

```shell
gh workflow run run-tests.yml -f logLevel=warning -f tags=false -f environment=staging
```

| **虚拟机**           | **处理器 (CPU)** | **内存 (RAM)** | **存储 (SSD)** | **体系结构** | **工作流标签**                                               |
| -------------------- | ---------------- | -------------- | -------------- | ------------ | ------------------------------------------------------------ |
| Linux                | 4                | 16 GB          | 14 GB          | X64          | `ubuntu-latest`, `ubuntu-24.04`, `ubuntu-22.04`              |
| Windows              | 4                | 16 GB          | 14 GB          | x64          | `windows-latest`、`windows-2025`、`windows-2022`、`windows-2019` |
| Linux [公共预览版]   | 4                | 16 GB          | 14 GB          | arm64        | `ubuntu-24.04-arm`, `ubuntu-22.04-arm`                       |
| Windows [公共预览版] | 4                | 16 GB          | 14 GB          | arm64        | `windows-11-arm`                                             |
| macOS                | 4                | 14 GB          | 14 GB          | Intel        | `macos-13`                                                   |
| macOS                | 3 (M1)           | 7 GB           | 14 GB          | arm64        | `macos-latest`, `macos-14`, `macos-15`                       |
