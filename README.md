A.V
Professional Thumbnail Maker App powered by Titan Engine.
<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Titanium Thumbnail Pro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Outfit:wght@400;900&family=Playfair+Display:ital,wght@1,900&family=Bungee&display=swap');

        body { font-family: 'Outfit', sans-serif; background: #000; color: white; margin: 0; overflow: hidden; }
        
        /* Font Styles */
        .f-bold { font-family: 'Bebas Neue', cursive; }
        .f-stylish { font-family: 'Playfair Display', serif; font-style: italic; }
        .f-pop { font-family: 'Bungee', cursive; }

        .editor-canvas {
            position: relative;
            width: 100%;
            aspect-ratio: 16/9;
            background: #111;
            border-radius: 20px;
            overflow: hidden;
            box-shadow: 0 0 40px rgba(99, 102, 241, 0.3);
            border: 1px solid rgba(255,255,255,0.1);
        }

        #thumbnail-bg { width: 100%; height: 100%; object-fit: cover; transition: filter 0.3s; }

        .draggable-layer {
            position: absolute;
            z-index: 50;
            cursor: move;
            touch-action: none;
            padding: 10px;
            text-align: center;
            user-select: none;
        }

        .text-display {
            font-size: 2.2rem;
            line-height: 1;
            text-transform: uppercase;
            text-shadow: 4px 4px 0px #000, -1px -1px 0px #000, 1px -1px 0px #000, -1px 1px 0px #000;
            pointer-events: none;
        }

        .glass-panel {
            background: rgba(10, 10, 10, 0.95);
            backdrop-filter: blur(25px);
            border-top: 1px solid rgba(255,255,255,0.1);
            border-radius: 40px 40px 0 0;
        }

        .color-dot {
            width: 40px; height: 40px; border-radius: 12px; flex-shrink: 0;
            border: 2px solid rgba(255,255,255,0.1); cursor: pointer;
            transition: 0.2s;
        }
        .active-dot { border-color: white; transform: scale(1.1); box-shadow: 0 0 15px currentColor; }

        #welcome-screen {
            position: fixed; inset: 0; background: #000; z-index: 1000;
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            background: radial-gradient(circle at center, #1e1b4b 0%, #000 100%);
        }

        .hide-scroll::-webkit-scrollbar { display: none; }
        
        .layer-active { outline: 2px dashed rgba(255,255,255,0.5); border-radius: 5px; }
    </style>
</head>
<body class="h-screen flex flex-col max-w-md mx-auto">

    <!-- START SCREEN -->
    <div id="welcome-screen">
        <div class="text-center mb-12">
            <h1 class="text-4xl font-black f-bold italic tracking-widest text-indigo-500">TITANIUM</h1>
            <h2 class="text-xl font-bold tracking-[0.5em] text-white">THUMBNAIL PRO</h2>
        </div>
        <button onclick="document.getElementById('img-input').click()" class="w-24 h-24 bg-indigo-600 rounded-full flex items-center justify-center shadow-[0_0_50px_rgba(79,70,229,0.5)] active:scale-90 transition-transform">
            <i class="fa-solid fa-plus text-4xl"></i>
        </button>
        <p class="mt-6 text-[10px] font-black text-zinc-500 uppercase tracking-widest">Select Image to Begin</p>
    </div>

    <!-- HEADER -->
    <header class="p-6 flex justify-between items-center bg-black">
        <button onclick="location.reload()" class="w-10 h-10 rounded-full bg-white/5 flex items-center justify-center border border-white/10"><i class="fa-solid fa-arrow-left text-xs"></i></button>
        <span class="text-xs font-black tracking-widest text-zinc-500">EDITOR V2.0</span>
        <button onclick="saveThumbnail()" class="bg-white text-black px-5 py-2 rounded-full text-[10px] font-black uppercase tracking-widest">Save</button>
    </header>

    <!-- PREVIEW AREA -->
    <main class="flex-1 px-4 flex flex-col items-center justify-center bg-[#050505] relative">
        <div class="editor-canvas" id="main-canvas">
            <!-- Background Image -->
            <img id="thumbnail-bg" src="" alt="">
            
            <!-- Draggable Text Layer -->
            <div id="text-layer" class="draggable-layer" style="top: 35%; left: 15%;">
                <div id="display-text" class="text-display f-bold text-white">TEXT HERE</div>
            </div>

            <!-- Draggable Sticker Layer -->
            <div id="sticker-layer" class="draggable-layer text-6xl" style="top: 10%; right: 10%;">
                🔥
            </div>
        </div>
        <div class="mt-4 flex gap-4 text-[9px] font-bold text-zinc-500 uppercase tracking-widest">
            <span><i class="fa-solid fa-hand-pointer mr-1"></i> Drag to move</span>
            <span><i class="fa-solid fa-expand mr-1"></i> Scale layers</span>
        </div>
    </main>

    <!-- CONTROLS PANEL -->
    <div class="h-[420px] glass-panel p-6 flex flex-col gap-5">
        
        <!-- Text Input -->
        <div class="relative">
            <input type="text" id="manual-text" placeholder="Likho kuch dhasu..." class="w-full bg-white/5 border border-white/10 p-4 rounded-2xl text-sm outline-none focus:border-indigo-500 pr-12">
            <i class="fa-solid fa-pen-nib absolute right-5 top-5 text-zinc-600"></i>
        </div>

        <!-- Style Controls -->
        <div class="flex gap-2 overflow-x-auto hide-scroll pb-1">
            <button onclick="setFont('f-bold')" id="btn-bold" class="shrink-0 px-5 py-2 bg-indigo-600 rounded-xl text-[10px] font-black uppercase font-btn">Mota</button>
            <button onclick="setFont('f-stylish')" id="btn-style" class="shrink-0 px-5 py-2 bg-white/5 border border-white/10 rounded-xl text-[10px] font-black uppercase font-btn">Stylish</button>
            <button onclick="setFont('f-pop')" id="btn-pop" class="shrink-0 px-5 py-2 bg-white/5 border border-white/10 rounded-xl text-[10px] font-black uppercase font-btn">Pop</button>
            <div class="w-[1px] bg-white/10 mx-2"></div>
            <button onclick="setSticker('🔥')" class="shrink-0 w-10 h-10 bg-white/5 rounded-xl flex items-center justify-center">🔥</button>
            <button onclick="setSticker('👑')" class="shrink-0 w-10 h-10 bg-white/5 rounded-xl flex items-center justify-center">👑</button>
            <button onclick="setSticker('🚀')" class="shrink-0 w-10 h-10 bg-white/5 rounded-xl flex items-center justify-center">🚀</button>
            <button onclick="setSticker('💎')" class="shrink-0 w-10 h-10 bg-white/5 rounded-xl flex items-center justify-center">💎</button>
        </div>

        <!-- 15 Beganihapinala Colors -->
        <div class="space-y-2">
            <div class="flex justify-between items-center">
                <p class="text-[9px] font-black text-zinc-500 uppercase tracking-widest">Premium Colors</p>
                <span id="current-color-name" class="text-[8px] text-indigo-400 font-bold uppercase">White</span>
            </div>
            <div id="palette" class="flex gap-3 overflow-x-auto hide-scroll py-1">
                <!-- Colors via JS -->
            </div>
        </div>

        <!-- Master Actions -->
        <div class="flex gap-3 mt-auto">
            <div class="flex-1 flex bg-white/5 rounded-2xl border border-white/10 overflow-hidden">
                <button onclick="changeSize(1.1)" class="flex-1 h-14 flex items-center justify-center active:bg-white/10"><i class="fa-solid fa-plus"></i></button>
                <div class="w-[1px] bg-white/10 h-8 my-auto"></div>
                <button onclick="changeSize(0.9)" class="flex-1 h-14 flex items-center justify-center active:bg-white/10"><i class="fa-solid fa-minus"></i></button>
            </div>
            <button onclick="document.getElementById('img-input').click()" class="flex-1 bg-white text-black rounded-2xl font-black text-[10px] uppercase tracking-tighter">Change Background</button>
        </div>
    </div>

    <!-- Hidden Input -->
    <input type="file" id="img-input" class="hidden" accept="image/*" onchange="handleImage(event)">

    <script>
        const bg = document.getElementById('thumbnail-bg');
        const displayText = document.getElementById('display-text');
        const manualInput = document.getElementById('manual-text');
        const welcome = document.getElementById('welcome-screen');
        const textLayer = document.getElementById('text-layer');
        const stickerLayer = document.getElementById('sticker-layer');

        let currentFontSize = 2.2;

        // 15 Professional Colors (VIBGYOR + Premium)
        const colorData = [
            {n: 'White', c: '#FFFFFF'}, {n: 'Violet', c: '#8B00FF'}, {n: 'Indigo', c: '#4B0082'}, 
            {n: 'Blue', c: '#0000FF'}, {n: 'Green', c: '#00FF00'}, {n: 'Yellow', c: '#FFFF00'}, 
            {n: 'Orange', c: '#FF7F00'}, {n: 'Red', c: '#FF0000'}, {n: 'Pink', c: '#FF1493'},
            {n: 'Cyan', c: '#00FFFF'}, {n: 'Lime', c: '#32CD32'}, {n: 'Gold', c: '#FFD700'},
            {n: 'Crimson', c: '#DC143C'}, {n: 'Sky', c: '#87CEEB'}, {n: 'Neon', c: '#CCFF00'}
        ];

        function initPalette() {
            const container = document.getElementById('palette');
            colorData.forEach(item => {
                const dot = document.createElement('div');
                dot.className = 'color-dot';
                dot.style.backgroundColor = item.c;
                dot.onclick = () => {
                    document.querySelectorAll('.color-dot').forEach(d => d.classList.remove('active-dot'));
                    dot.classList.add('active-dot');
                    displayText.style.color = item.c;
                    document.getElementById('current-color-name').innerText = item.n;
                };
                container.appendChild(dot);
            });
            // Set first color active
            container.firstChild.classList.add('active-dot');
        }

        function handleImage(e) {
            const file = e.target.files[0];
            if(!file) return;
            const reader = new FileReader();
            reader.onload = (event) => {
                bg.src = event.target.result;
                welcome.style.opacity = '0';
                setTimeout(() => welcome.style.display = 'none', 500);
            };
            reader.readAsDataURL(file);
        }

        manualInput.oninput = () => {
            displayText.innerText = manualInput.value || "TEXT HERE";
        };

        function setFont(styleClass) {
            displayText.className = `text-display ${styleClass}`;
            document.querySelectorAll('.font-btn').forEach(b => b.classList.replace('bg-indigo-600', 'bg-white/5'));
            event.target.classList.replace('bg-white/5', 'bg-indigo-600');
        }

        function setSticker(emoji) {
            stickerLayer.innerText = emoji;
        }

        function changeSize(multiplier) {
            currentFontSize *= multiplier;
            displayText.style.fontSize = currentFontSize + "rem";
        }

        // Draggable Logic (Universal for layers)
        function applyDrag(el) {
            let isDragging = false;
            let offsetX, offsetY;

            el.addEventListener('touchstart', (e) => {
                isDragging = true;
                const touch = e.touches[0];
                offsetX = touch.clientX - el.offsetLeft;
                offsetY = touch.clientY - el.offsetTop;
                el.classList.add('layer-active');
            });

            document.addEventListener('touchmove', (e) => {
                if (!isDragging) return;
                const touch = e.touches[0];
                el.style.left = (touch.clientX - offsetX) + 'px';
                el.style.top = (touch.clientY - offsetY) + 'px';
            });

            document.addEventListener('touchend', () => {
                isDragging = false;
                el.classList.remove('layer-active');
            });
        }

        applyDrag(textLayer);
        applyDrag(stickerLayer);

        function saveThumbnail() {
            const btn = event.target;
            btn.innerText = "SAVING...";
            setTimeout(() => {
                btn.innerText = "SAVED!";
                alert("Thumbnail saved to gallery successfully! (Simulated)");
                setTimeout(() => btn.innerText = "SAVE", 2000);
            }, 1500);
        }

        // Init
        initPalette();
    </script>
</body>
</html>

