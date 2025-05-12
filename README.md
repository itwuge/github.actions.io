[toc]

# [官方文档](https://docs.github.com/zh/actions)

```yml
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

# 含义

```yml
# 自动化的名称
name: Build-Openwrt-Lean
# on字段指定触发 workflow 的条件，通常是某些事件。
on:
	# 手动触发工作流 比如在窗口添加一个按钮
  repository_dispatch:
  # orkflow_dispatch 事件 最多可以定义 10 个 inputs
  workflow_dispatch:
  	# 提供一个按钮
    inputs:
    	# 按钮的名称 可以调用 github.event.inputs.ssh 来获取输入的变量值
      ssh:
      	# 按钮的标题
        description: 'SSH 连接Actions'
        # 定义是否为必须填写
        required: false
        # 默认不是必须填写
        default: 'false'
    # 可以在发生 push 事件时运行工作流。
    push:
      # 在计划的时间触发工作流
      schedule:
        - cron: 0 */18 * * *
# 设置环境变量 调用方式:$变量名  
env:
  REPO_URL: https://github.com/coolsnowwolf/lede
  REPO_BRANCH: master
  CONFIG_FILE: config-x86-Lean
  DIY_SH: diy-x86-Lean.sh
  SSH_ACTIONS:  false
  TZ: Asia/Shanghai
# 执行的一项或多项任务。
jobs:
	# jobs ID 自定义 一项任务就一个 ID 
  build:
  	#  字段指定任务运行所需要的虚拟服务器环境
    runs-on: ubuntu-20.04
    # 判断是仓库持有者
    if: github.event.repository.owner.id == github.event.sender.id
    # 
    steps:
    - name: 拉取分支
    	uses: actions/checkout@main
    	- name: 初始化环境
    		env:
        	DEBIAN_FRONTEND: noninteractive
      	run: |
          sudo rm -rf /etc/apt/sources.list.d/* /usr/share/dotnet /usr/local/lib/android /opt/ghc
          sudo -E apt-get update
          sudo -E apt-get -y install $(curl -fsSL git.io/depends-ubuntu-2004)
          sudo -E apt-get -y autoremove --purge
          sudo -E apt-get clean
          sudo timedatectl set-timezone "$TZ" 
          export FORCE_UNSAFE_CONFIGURE=1
          
          echo "========================================"
          echo "[Space Usage]:"
          echo "========================================"
          df -hT
          echo "========================================"
          echo "[File List]:" 
          echo "========================================"
          echo "Current Path:" $PWD
          ls -l -A
          echo "========================================

```

