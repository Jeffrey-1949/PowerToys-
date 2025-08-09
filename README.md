# PowerToys Keyboard Manager Switcher PowerToys键盘管理器切换工具

一个基于 **Python** 的轻量级工具，用于在 **Windows 系统托盘** 中快速管理  
[Microsoft PowerToys](https://learn.microsoft.com/zh-cn/windows/powertoys/) 的 **Keyboard Manager 引擎**。

你可以直接在托盘图标中 **启动 / 停止 / 切换键盘映射状态**，并实时看到运行状态（绿色=已启用，红色=已禁用）。

---

## 功能特性
- ✅ 托盘图标实时显示 Keyboard Manager 状态（绿色=已启用，红色=已禁用）
- ✅ 左键单击一键切换键盘映射状态
- ✅ 右键菜单快速启动 / 停止 Keyboard Manager
- ✅ 自动适配高 DPI 显示
- ✅ 持续后台监控进程状态并刷新图标
- ✅ 完全隐藏控制台窗口运行

---

## 使用方法
1. 下载源码：克隆或下载本项目到本地
2. 配置路径：
   用文本编辑器打开 main.py，找到并修改以下行：
   ```python
   KEYBOARD_MANAGER_PATH = r"C:\Users\<你的用户名>\AppData\Local\PowerToys\KeyboardManagerEngine\PowerToys.KeyboardManagerEngine.exe"
   ```
   将路径替换为你本机 KeyboardManagerEngine.exe 的实际路径
3. 启动程序：
   在命令行进入脚本所在目录，运行：
   ```bash
   python main.py
   ```
4. 图标含义：
   - 🟢 绿色图标 → Keyboard Manager 已启用
   - 🔴 红色图标 → Keyboard Manager 已禁用
5. 操作方式：
   - 左键单击 → 切换键盘映射状态（启用/禁用）
   - 右键菜单：
     - 切换键盘映射 → 功能同左键
     - 启动键盘映射 → 启动 Keyboard Manager 引擎
     - 停止键盘映射 → 停止 Keyboard Manager 引擎
     - 退出 → 关闭程序并移除托盘图标
6. 退出程序：
   - 右键托盘图标 → 选择 退出
   - 或在任务管理器中结束 Python 进程（不推荐）

---

## 环境要求
- Windows 10 / 11
- 已安装 **PowerToys** 并启用 **Keyboard Manager**
- Python 3.8 或更高版本
- 安装依赖：
  ```bash
  pip install psutil pystray pillow pywin32
  ```

---

## 支持项目
如果你觉得这个工具对你有帮助，可以请我喝杯咖啡 ☕  
你的支持将帮助我持续优化和维护这个项目 ❤️

微信捐赠：  
[微信捐赠二维码链接](https://github.com/user-attachments/assets/b847f743-c990-4b8f-a3de-c3a9ee09d412)

支付宝捐赠：  
[支付宝捐赠二维码链接](https://github.com/user-attachments/assets/54c0f1fe-5e96-429b-84dc-3d848612f8db)

---
作者：The K
