# Windows-11-Simulator
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Windows 11 Simulator Pro</title>
  <style>
    :root {
      --bg-wallpaper: radial-gradient(circle at 20% 30%, #4facfe 0%, transparent 40%),
                      radial-gradient(circle at 80% 70%, #00f2fe 0%, transparent 45%),
                      linear-gradient(135deg, #091e3a 0%, #2f80ed 50%, #1c3b70 100%);
      --glass-bg: rgba(243, 243, 243, 0.85);
      --win-bg: rgba(255, 255, 255, 0.90);
      --title-bg: rgba(255, 255, 255, 0.6);
      --border-color: rgba(255, 255, 255, 0.5);
      --text-main: #222222;
      --text-muted: #555555;
      --hover-bg: rgba(0, 0, 0, 0.08);
      --card-bg: rgba(255, 255, 255, 0.7);
      --input-bg: #ffffff;
    }

    body.dark-mode {
      --glass-bg: rgba(32, 32, 32, 0.85);
      --win-bg: rgba(38, 38, 38, 0.92);
      --title-bg: rgba(28, 28, 28, 0.7);
      --border-color: rgba(255, 255, 255, 0.12);
      --text-main: #f3f3f3;
      --text-muted: #aaaaaa;
      --hover-bg: rgba(255, 255, 255, 0.1);
      --card-bg: rgba(55, 55, 55, 0.7);
      --input-bg: #2b2b2b;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      user-select: none;
    }

    body {
      width: 100vw;
      height: 100vh;
      overflow: hidden;
      background: var(--bg-wallpaper);
      color: var(--text-main);
      position: relative;
      transition: background 0.3s ease;
    }

    /* Desktop Icons */
    .desktop {
      padding: 20px;
      display: flex;
      flex-direction: column;
      flex-wrap: wrap;
      align-content: flex-start;
      gap: 20px;
      height: calc(100vh - 48px);
    }

    .desktop-icon {
      width: 78px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 6px;
      padding: 8px 4px;
      border-radius: 6px;
      color: white;
      text-shadow: 0 1px 3px rgba(0,0,0,0.85);
      font-size: 12px;
      text-align: center;
      cursor: pointer;
      transition: background 0.15s;
      word-break: break-word;
    }

    .desktop-icon:hover {
      background: rgba(255, 255, 255, 0.15);
    }

    .desktop-icon span.icon {
      font-size: 28px;
    }

    /* Right-Click Context Menu */
    .context-menu {
      position: absolute;
      width: 210px;
      background: var(--glass-bg);
      backdrop-filter: blur(22px);
      -webkit-backdrop-filter: blur(22px);
      border: 1px solid var(--border-color);
      border-radius: 8px;
      box-shadow: 0 10px 25px rgba(0,0,0,0.3);
      padding: 6px;
      display: none;
      flex-direction: column;
      gap: 2px;
      z-index: 10000;
    }

    .context-item {
      padding: 8px 12px;
      border-radius: 5px;
      font-size: 13px;
      cursor: pointer;
      display: flex;
      align-items: center;
      gap: 10px;
      color: var(--text-main);
    }

    .context-item:hover {
      background: var(--hover-bg);
    }

    .context-divider {
      height: 1px;
      background: var(--border-color);
      margin: 4px 0;
    }

    /* Windows */
    .window {
      position: absolute;
      width: 480px;
      height: 340px;
      min-width: 260px;
      min-height: 200px;
      background: var(--win-bg);
      backdrop-filter: blur(22px);
      -webkit-backdrop-filter: blur(22px);
      border-radius: 8px;
      box-shadow: 0 14px 36px rgba(0, 0, 0, 0.35);
      border: 1px solid var(--border-color);
      display: none;
      flex-direction: column;
      overflow: hidden;
      resize: both;
      animation: popIn 0.15s ease-out;
    }

    .window.maximized {
      top: 0 !important;
      left: 0 !important;
      width: 100vw !important;
      height: calc(100vh - 48px) !important;
      border-radius: 0;
      resize: none;
    }

    @keyframes popIn {
      from { transform: scale(0.95); opacity: 0; }
      to { transform: scale(1); opacity: 1; }
    }

    .title-bar {
      height: 34px;
      background: var(--title-bg);
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0 0 0 12px;
      cursor: grab;
      font-size: 13px;
      font-weight: 500;
      color: var(--text-main);
      flex-shrink: 0;
    }

    .title-bar:active { cursor: grabbing; }

    .window-controls {
      display: flex;
      height: 100%;
    }

    .window-controls button {
      width: 42px;
      height: 100%;
      border: none;
      background: transparent;
      color: var(--text-main);
      font-size: 13px;
      cursor: pointer;
      transition: background 0.15s;
    }

    .window-controls button:hover { background: var(--hover-bg); }
    .window-controls button.close-btn:hover { background: #e81123; color: white; }

    .window-content {
      flex: 1;
      padding: 12px;
      overflow: auto;
      user-select: text;
    }

    /* Notepad App */
    #notepad-area {
      width: 100%;
      height: 100%;
      border: none;
      background: transparent;
      color: var(--text-main);
      resize: none;
      outline: none;
      font-family: 'Consolas', monospace;
      font-size: 14px;
    }

    /* Terminal App */
    .terminal-content {
      background: #0c0c0c;
      color: #cccccc;
      font-family: 'Consolas', 'Courier New', monospace;
      font-size: 13px;
      padding: 12px;
      display: flex;
      flex-direction: column;
      height: 100%;
    }

    #terminal-output {
      flex: 1;
      overflow-y: auto;
      white-space: pre-wrap;
      line-height: 1.4;
    }

    .terminal-input-line {
      display: flex;
      align-items: center;
      margin-top: 6px;
    }

    .terminal-input-line span { color: #4cc2ff; margin-right: 6px; }

    #terminal-input {
      flex: 1;
      background: transparent;
      border: none;
      color: #ffffff;
      font-family: inherit;
      font-size: 13px;
      outline: none;
    }

    /* Calculator App */
    .calc-grid {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 6px;
      height: 100%;
    }

    .calc-display {
      grid-column: span 4;
      background: var(--card-bg);
      border: 1px solid var(--border-color);
      color: var(--text-main);
      border-radius: 4px;
      text-align: right;
      padding: 10px;
      font-size: 22px;
      font-weight: bold;
    }

    .calc-btn {
      border: 1px solid var(--border-color);
      background: var(--card-bg);
      color: var(--text-main);
      border-radius: 4px;
      font-size: 16px;
      cursor: pointer;
    }

    .calc-btn:hover { filter: brightness(1.1); }
    .calc-btn.op { background: rgba(0, 120, 212, 0.2); }

    /* Explorer App */
    .folder-grid {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
      gap: 12px;
    }
    .folder-item {
      padding: 10px;
      border-radius: 6px;
      background: var(--card-bg);
      border: 1px solid var(--border-color);
      display: flex;
      align-items: center;
      gap: 8px;
      font-size: 13px;
    }

    /* Start Menu */
    .start-menu {
      position: absolute;
      bottom: 58px;
      left: 50%;
      transform: translateX(-50%) translateY(20px);
      width: 480px;
      height: 440px;
      background: var(--glass-bg);
      backdrop-filter: blur(25px);
      -webkit-backdrop-filter: blur(25px);
      border-radius: 10px;
      border: 1px solid var(--border-color);
      box-shadow: 0 15px 35px rgba(0, 0, 0, 0.35);
      padding: 24px;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      opacity: 0;
      pointer-events: none;
      transition: all 0.2s cubic-bezier(0.16, 1, 0.3, 1);
      z-index: 9998;
    }

    .start-menu.open {
      opacity: 1;
      pointer-events: auto;
      transform: translateX(-50%) translateY(0);
    }

    .start-search {
      width: 100%;
      padding: 10px 14px;
      border-radius: 20px;
      border: 1px solid var(--border-color);
      background: var(--input-bg);
      color: var(--text-main);
      outline: none;
      font-size: 13px;
    }

    .pinned-apps {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 16px;
      margin-top: 20px;
    }

    .pinned-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 6px;
      padding: 10px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 12px;
    }

    .pinned-item:hover { background: var(--hover-bg); }

    .start-footer {
      border-top: 1px solid var(--border-color);
      padding-top: 12px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 13px;
    }

    /* Taskbar */
    .taskbar {
      position: absolute;
      bottom: 0;
      width: 100%;
      height: 48px;
      background: var(--glass-bg);
      backdrop-filter: blur(20px);
      -webkit-backdrop-filter: blur(20px);
      border-top: 1px solid var(--border-color);
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 9999;
    }

    .taskbar-icons { display: flex; gap: 8px; }

    .tb-icon {
      width: 38px;
      height: 38px;
      border-radius: 6px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 20px;
      cursor: pointer;
      transition: background 0.15s, transform 0.1s;
    }

    .tb-icon:hover { background: var(--hover-bg); }
    .tb-icon:active { transform: scale(0.92); }

    .win-logo {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 2px;
      width: 18px;
      height: 18px;
    }
    .win-logo div { background: #0078d4; border-radius: 1px; }

    .system-tray {
      position: absolute;
      right: 16px;
      display: flex;
      align-items: center;
      gap: 12px;
      font-size: 12px;
    }
  </style>
</head>
<body>
  <script>
let topZ = 100;
// Wallpapers List
const wallpapers = [
'radial-gradient(circle at 20% 30%, #4facfe 0%, transparent 40%), radial-gradient(circle at 80% 70%, #00f2fe 0%, transparent 45%), linear-gradient(135deg, #091e3a 0%, #2f80ed 50%, #1c3b70 100%)',
'radial-gradient(circle at 75% 25%, #ff758c 0%, transparent 45%), linear-gradient(135deg, #2b1055 0%, #7597de 100%)',
'radial-gradient(circle at 30% 70%, #11998e 0%, transparent 50%), linear-gradient(135deg, #0f2027 0%, #203a43 50%, #2c5364 100%)'
];
let currentWall = parseInt(localStorage.getItem('win11_wall')) || 0;
// Load Saved State on Startup
window.addEventListener('DOMContentLoaded', () => {
// Load Notepad
const savedNote = localStorage.getItem('win11_notepad');
if (savedNote) document.getElementById('notepad-area').value = savedNote;
// Load Dark Mode
if (localStorage.getItem('win11_dark') === 'true') {
document.body.classList.add('dark-mode');
}
// Load Wallpaper
document.body.style.setProperty('--bg-wallpaper', wallpapers[currentWall]);
// Load Custom Folders
const savedFolders = JSON.parse(localStorage.getItem('win11_folders') || '[]');
savedFolders.forEach(name => renderFolder(name));
});
// Save Notepad on every keystroke
document.getElementById('notepad-area').addEventListener('input', (e) => {
localStorage.setItem('win11_notepad', e.target.value);
});
// Right-Click Context Menu Logic
const desktop = document.getElementById('desktop');
const contextMenu = document.getElementById('context-menu');
desktop.addEventListener('contextmenu', (e) => {
e.preventDefault();
contextMenu.style.display = 'flex';
contextMenu.style.left = Math.min(e.clientX, window.innerWidth - 220) + 'px';
contextMenu.style.top = Math.min(e.clientY, window.innerHeight - 180) + 'px';
});
document.addEventListener('click', () => {
contextMenu.style.display = 'none';
});
function createNewFolder() {
const name = prompt('Enter folder name:', 'New Folder');
if (!name || !name.trim()) return;
const cleanName = name.trim();
renderFolder(cleanName);
const savedFolders = JSON.parse(localStorage.getItem('win11_folders') || '[]');
savedFolders.push(cleanName);
localStorage.setItem('win11_folders', JSON.stringify(savedFolders));
}
function renderFolder(name) {
// Add to Desktop
const icon = document.createElement('div');
icon.className = 'desktop-icon';
icon.ondblclick = () => openWindow('explorer');
icon.innerHTML = `<span class="icon">📁</span><span>${name}</span>`;
desktop.appendChild(icon);
// Also sync inside File Explorer
const grid = document.getElementById('explorer-grid');
const item = document.createElement('div');
item.className = 'folder-item';
item.innerText = `📁 ${name}`;
grid.appendChild(item);
}
function nextWallpaper() {
currentWall = (currentWall + 1) % wallpapers.length;
document.body.style.setProperty('--bg-wallpaper', wallpapers[currentWall]);
localStorage.setItem('win11_wall', currentWall);
}
function resetSystem() {
if (confirm('Clear all saved notes, folders, and theme settings?')) {
localStorage.clear();
location.reload();
}
}
// Window Controls
function openWindow(app) {
const win = document.getElementById('win-' + app);
win.style.display = 'flex';
bringToFront(win);
if (app === 'terminal') {
setTimeout(() => document.getElementById('terminal-input').focus(), 50);
}
}
function closeWindow(id) {
const win = document.getElementById(id);
win.style.display = 'none';
win.classList.remove('maximized');
}
function minimizeWindow(id) {
document.getElementById(id).style.display = 'none';
}
function toggleMaximize(id) {
const win = document.getElementById(id);
win.classList.toggle('maximized');
bringToFront(win);
}
function bringToFront(win) {
topZ++;
win.style.zIndex = topZ;
}
document.querySelectorAll('.window').forEach(win => {
win.addEventListener('mousedown', () => bringToFront(win));
});
function toggleDarkMode() {
const isDark = document.body.classList.toggle('dark-mode');
localStorage.setItem('win11_dark', isDark);
}
function toggleStartMenu() {
document.getElementById('start-menu').classList.toggle('open');
}
desktop.addEventListener('click', () => {
document.getElementById('start-menu').classList.remove('open');
});
// Draggable Windows
let activeWin = null, offsetX = 0, offsetY = 0;
function startDrag(e, id) {
if (e.target.tagName === 'BUTTON') return;
const win = document.getElementById(id);
if (win.classList.contains('maximized')) return;
activeWin = win;
bringToFront(activeWin);
offsetX = e.clientX - activeWin.offsetLeft;
offsetY = e.clientY - activeWin.offsetTop;
}
document.addEventListener('mousemove', (e) => {
if (!activeWin) return;
activeWin.style.left = (e.clientX - offsetX) + 'px';
activeWin.style.top = (e.clientY - offsetY) + 'px';
});
document.addEventListener('mouseup', () => { activeWin = null; });
// Live Clock
function updateClock() {
const now = new Date();
document.getElementById('time').innerText = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
document.getElementById('date').innerText = now.toLocaleDateString();
}
setInterval(updateClock, 1000);
updateClock();
// Calculator Logic
let calcExpr = '';
function calcInput(val) {
calcExpr += val;
document.getElementById('calc-display').innerText = calcExpr;
}
function calcClear() {
calcExpr = '';
document.getElementById('calc-display').innerText = '0';
}
function calcEqual() {
try {
calcExpr = eval(calcExpr).toString();
document.getElementById('calc-display').innerText = calcExpr;
} catch {
document.getElementById('calc-display').innerText = 'Error';
calcExpr = '';
}
}
// Terminal Logic
const termInput = document.getElementById('terminal-input');
const termOutput = document.getElementById('terminal-output');
termInput.addEventListener('keydown', function(e) {
if (e.key === 'Enter') {
const rawCmd = this.value.trim();
const cmd = rawCmd.toLowerCase();
termOutput.innerHTML += `\nPS C:\\Users\\User> ${rawCmd}\n`;
if (cmd === 'help') {
termOutput.innerHTML += `Available commands: help, dir, date, echo [text], theme dark, theme light, cls, exit\n`;
} else if (cmd === 'dir') {
termOutput.innerHTML += `Directory: C:\\Users\\User\n  Documents\n  Downloads\n  Pictures\n`;
} else if (cmd === 'date') {
termOutput.innerHTML += `${new Date().toString()}\n`;
} else if (cmd.startsWith('echo ')) {
termOutput.innerHTML += `${rawCmd.slice(5)}\n`;
} else if (cmd === 'theme dark') {
document.body.classList.add('dark-mode');
localStorage.setItem('win11_dark', true);
termOutput.innerHTML += `Switched to Dark Mode.\n`;
} else if (cmd === 'theme light') {
document.body.classList.remove('dark-mode');
localStorage.setItem('win11_dark', false);
termOutput.innerHTML += `Switched to Light Mode.\n`;
} else if (cmd === 'cls' || cmd === 'clear') {
termOutput.innerHTML = '';
} else if (cmd === 'exit') {
closeWindow('win-terminal');
} else if (rawCmd !== '') {
termOutput.innerHTML += `'${rawCmd}' is not recognized. Type 'help'.\n`;
}
this.value = '';
termOutput.scrollTop = termOutput.scrollHeight;
}
});
  </script>
</body>
</html>
<!-- App 1: Microsoft Edge (Mini Browser) -->
<div class="window" id="win-browser" style="top: 60px; left: 160px; width: 620px; height: 420px;">
  <div class="title-bar" onmousedown="startDrag(event, 'win-browser')" ondblclick="toggleMaximize('win-browser')">
    <span>🌐 Microsoft Edge</span>
    <div class="window-controls">
      <button onclick="minimizeWindow('win-browser')">—</button>
      <button onclick="toggleMaximize('win-browser')">⬜</button>
      <button class="close-btn" onclick="closeWindow('win-browser')">✕</button>
    </div>
  </div>
  <div style="display: flex; gap: 6px; padding: 6px 10px; background: var(--title-bg); border-bottom: 1px solid var(--border-color);">
    <input type="text" id="browser-url" value="https://wikipedia.org" 
           style="flex: 1; padding: 4px 10px; border-radius: 14px; border: 1px solid var(--border-color); background: var(--input-bg); color: var(--text-main); font-size: 12px; outline: none;"
           onkeydown="if(event.key==='Enter') navigateBrowser()">
    <button onclick="navigateBrowser()" style="padding: 4px 12px; border-radius: 14px; border: none; background: #0078d4; color: white; cursor: pointer; font-size: 12px;">Go</button>
  </div>
  <div class="window-content" style="padding: 0;">
    <iframe id="browser-frame" src="https://wikipedia.org" style="width: 100%; height: 100%; border: none; background: white;"></iframe>
  </div>
</div>

<!-- App 2: Paint (Drawing Canvas) -->
<div class="window" id="win-paint" style="top: 90px; left: 220px; width: 520px; height: 380px;">
  <div class="title-bar" onmousedown="startDrag(event, 'win-paint')" ondblclick="toggleMaximize('win-paint')">
    <span>🎨 Paint</span>
    <div class="window-controls">
      <button onclick="minimizeWindow('win-paint')">—</button>
      <button onclick="toggleMaximize('win-paint')">⬜</button>
      <button class="close-btn" onclick="closeWindow('win-paint')">✕</button>
    </div>
  </div>
  <div style="display: flex; align-items: center; gap: 12px; padding: 6px 12px; background: var(--title-bg); border-bottom: 1px solid var(--border-color); font-size: 12px;">
    <label>Color: <input type="color" id="paint-color" value="#0078d4"></label>
    <label>Size: <input type="range" id="paint-size" min="1" max="20" value="4" style="width: 80px;"></label>
    <button onclick="clearCanvas()" style="padding: 3px 10px; border-radius: 4px; border: 1px solid var(--border-color); background: var(--card-bg); color: var(--text-main); cursor: pointer;">Clear</button>
  </div>
  <div class="window-content" style="padding: 0; background: #ffffff; overflow: hidden;">
    <canvas id="paint-canvas" width="800" height="600" style="cursor: crosshair; display: block;"></canvas>
  </div>
</div>

<!-- App 3: Settings -->
<div class="window" id="win-settings" style="top: 120px; left: 260px; width: 460px; height: 340px;">
  <div class="title-bar" onmousedown="startDrag(event, 'win-settings')" ondblclick="toggleMaximize('win-settings')">
    <span>⚙️ Settings</span>
    <div class="window-controls">
      <button onclick="minimizeWindow('win-settings')">—</button>
      <button onclick="toggleMaximize('win-settings')">⬜</button>
      <button class="close-btn" onclick="closeWindow('win-settings')">✕</button>
    </div>
  </div>
  <div class="window-content" style="display: flex; flex-direction: column; gap: 14px;">
    <h3 style="font-size: 16px;">Personalization & System</h3>
    <div class="folder-item" style="justify-content: space-between;">
      <span>🌓 App & System Theme</span>
      <button onclick="toggleDarkMode()" style="padding: 5px 12px; border-radius: 5px; border: none; background: #0078d4; color: white; cursor: pointer;">Toggle Theme</button>
    </div>
    <div class="folder-item" style="justify-content: space-between;">
      <span>🖼️ Desktop Background</span>
      <button onclick="nextWallpaper()" style="padding: 5px 12px; border-radius: 5px; border: none; background: #0078d4; color: white; cursor: pointer;">Switch Wallpaper</button>
    </div>
    <div class="folder-item" style="justify-content: space-between;">
      <span>💻 System Info</span>
      <span style="color: var(--text-muted); font-size: 12px;">Windows 11 Web Edition</span>
    </div>
  </div>
</div>
<div class="desktop-icon" ondblclick="openWindow('browser')">
  <span class="icon">🌐</span><span>Edge</span>
</div>
<div class="desktop-icon" ondblclick="openWindow('paint')">
  <span class="icon">🎨</span><span>Paint</span>
</div>
<div class="desktop-icon" ondblclick="openWindow('settings')">
  <span class="icon">⚙️</span><span>Settings</span>
</div>
<div class="tb-icon" onclick="openWindow('browser')" title="Microsoft Edge">🌐</div>
<div class="tb-icon" onclick="openWindow('paint')" title="Paint">🎨</div>
<div class="tb-icon" onclick="openWindow('settings')" title="Settings">⚙️</div>

