import os
import subprocess
import psutil
import pystray
from PIL import Image, ImageDraw
import threading
import time
import ctypes
import sys
import win32process
import win32con
import win32gui
import win32api

# -------------------------------------------------------------------
# 配置区
# -------------------------------------------------------------------
# Keyboard Manager 引擎，此处替换为PowerToys.KeyboardManagerEngine.exe的路径。
KEYBOARD_MANAGER_PATH = r"C:\YourPath\PowerToys\KeyboardManagerEngine\PowerToys.KeyboardManagerEngine.exe"

# -------------------------------------------------------------------
# 工具函数
# -------------------------------------------------------------------
def get_scaling_factor() -> float:
    """获取系统缩放因子，支持高 DPI"""
    try:
        user32 = ctypes.windll.user32
        user32.SetProcessDPIAware()
        return user32.GetDpiForSystem() / 96.0
    except Exception:
        return 1.0

def is_keyboard_manager_active() -> bool:
    """检测 Keyboard Manager 引擎进程是否在运行"""
    name = os.path.basename(KEYBOARD_MANAGER_PATH)
    for proc in psutil.process_iter(['name']):
        if proc.info['name'] == name:
            return True
    return False

def hide_console_window():
    """完全隐藏控制台窗口"""
    try:
        hwnd = ctypes.windll.kernel32.GetConsoleWindow()
        if hwnd:
            ctypes.windll.user32.ShowWindow(hwnd, win32con.SW_HIDE)
        kernel32 = ctypes.WinDLL('kernel32')
        kernel32.FreeConsole()
    except Exception as e:
        print(f"隐藏控制台错误: {e}", file=sys.stderr)

# -------------------------------------------------------------------
# 菜单回调
# -------------------------------------------------------------------
def start_keyboard_manager(icon, item):
    """启动键盘管理器"""
    try:
        startupinfo = subprocess.STARTUPINFO()
        startupinfo.dwFlags |= subprocess.STARTF_USESHOWWINDOW
        startupinfo.wShowWindow = win32con.SW_HIDE
        subprocess.Popen(
            KEYBOARD_MANAGER_PATH,
            startupinfo=startupinfo,
            creationflags=subprocess.CREATE_NO_WINDOW | subprocess.DETACHED_PROCESS
        )
    except Exception as e:
        print(f"启动错误: {e}", file=sys.stderr)

def stop_keyboard_manager(icon, item):
    """停止键盘管理器"""
    name = os.path.basename(KEYBOARD_MANAGER_PATH)
    for proc in psutil.process_iter(['name']):
        if proc.info['name'] == name:
            try:
                handle = win32api.OpenProcess(win32con.PROCESS_TERMINATE, 0, proc.pid)
                win32process.TerminateProcess(handle, 0)
                win32api.CloseHandle(handle)
            except Exception as e:
                print(f"停止错误: {e}", file=sys.stderr)

def toggle_keyboard_manager(icon, item):
    """切换键盘映射状态"""
    if is_keyboard_manager_active():
        stop_keyboard_manager(icon, None)
    else:
        start_keyboard_manager(icon, None)

def on_quit(icon, item):
    """退出程序"""
    icon.stop()
    sys.exit(0)

# -------------------------------------------------------------------
# 托盘图标
# -------------------------------------------------------------------
def create_tray_icon_image(active: bool) -> Image.Image:
    """创建托盘图标"""
    base_size = 32
    factor = get_scaling_factor()
    size = int(base_size * factor)
    img = Image.new("RGBA", (size, size), (0, 0, 0, 0))
    dc = ImageDraw.Draw(img)

    # 活动/未活动背景
    bg = (46, 204, 113, 255) if active else (231, 76, 60, 255)
    dc.ellipse([(0, 0), (size-1, size-1)], fill=bg, outline=(255, 255, 255))

    key = size // 4
    off = size // 6
    # 上
    dc.rectangle([(size//2-key//2, off), (size//2+key//2, off+key)], fill="white")
    # 左
    dc.rectangle([(off, size//2-key//2), (off+key, size//2+key//2)], fill="white")
    # 右
    dc.rectangle([(size-off-key, size//2-key//2), (size-off, size//2+key//2)], fill="white")

    return img.resize((32, 32), Image.Resampling.LANCZOS)

def update_icon_state(icon: pystray.Icon, active: bool):
    """更新图标状态"""
    try:
        icon.icon = create_tray_icon_image(active)
        icon.title = f"PowerToys 键盘管理器: {'已启用' if active else '已禁用'}"
    except Exception as e:
        print(f"更新图标错误: {e}", file=sys.stderr)

# -------------------------------------------------------------------
# 状态监控（代码2策略）
# -------------------------------------------------------------------
def monitor_status(icon: pystray.Icon):
    """持续监控状态并更新图标"""
    while True:
        try:
            active = is_keyboard_manager_active()
            update_icon_state(icon, active)
        except Exception as e:
            print(f"监控错误: {e}", file=sys.stderr)
        time.sleep(2)  # 每2秒检查一次

# -------------------------------------------------------------------
# 单击处理
# -------------------------------------------------------------------
def on_left_click(icon, item):
    """左键单击切换"""
    toggle_keyboard_manager(icon, None)

# -------------------------------------------------------------------
# 主程序入口
# -------------------------------------------------------------------
if __name__ == "__main__":
    hide_console_window()
    try:
        ctypes.windll.shcore.SetProcessDpiAwareness(1)
    except:
        pass

    init_active = is_keyboard_manager_active()

    menu = pystray.Menu(
        pystray.MenuItem("切换键盘映射", on_left_click, default=True),
        pystray.MenuItem("启动键盘映射", start_keyboard_manager),
        pystray.MenuItem("停止键盘映射", stop_keyboard_manager),
        pystray.MenuItem("退出", on_quit)
    )

    icon = pystray.Icon(
        "pt_km",
        create_tray_icon_image(init_active),
        f"PowerToys 键盘管理器: {'已启用' if init_active else '已禁用'}",
        menu=menu
    )

    threading.Thread(target=monitor_status, args=(icon,), daemon=True).start()

    try:
        icon.run()
    except Exception as e:
        print(f"托盘运行错误: {e}", file=sys.stderr)
