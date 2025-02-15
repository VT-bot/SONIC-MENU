<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Menu Hack</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        .draggable {
            touch-action: none;
            position: absolute;
            cursor: grab;
        }
    </style>
</head>
<body class="flex items-center justify-center h-screen bg-gray-900">
    <div id="menu" class="draggable w-64 p-4 bg-blue-300 rounded-lg shadow-lg relative">
        <h2 class="text-center text-lg font-bold mb-4">Menu Hack</h2>
        <img src="/mnt/data/IMG_20250213_055316.png" alt="Sonic" class="w-32 h-32 mx-auto mb-4">
        <button class="w-full p-2 mb-2 bg-green-500 rounded" onclick="toggleESP()">ESP</button>
        <button class="w-full p-2 mb-2 bg-red-500 rounded" onclick="toggleNoClip()">No Clip</button>
        <button class="w-full p-2 mb-2 bg-purple-500 rounded" onclick="toggleInvisibilidade()">Invisibilidade</button>
        <a href="https://discord.gg/dDHkEQvP" target="_blank" class="flex items-center justify-center w-full p-2 bg-gray-700 rounded">
            <img src="https://upload.wikimedia.org/wikipedia/en/c/c5/Sonic_the_Hedgehog_character.png" alt="Sonic" class="w-8 h-8 mr-2">
            <span class="text-white">Discord</span>
        </a>
    </div>

    <script>
        let isESPOn = false;
        let isNoClipOn = false;
        let isInvisible = false;

        function toggleESP() {
            isESPOn = !isESPOn;
            alert(`ESP ${isESPOn ? 'Ativado' : 'Desativado'}`);
        }

        function toggleNoClip() {
            isNoClipOn = !isNoClipOn;
            alert(`No Clip ${isNoClipOn ? 'Ativado' : 'Desativado'}`);
        }

        function toggleInvisibilidade() {
            isInvisible = !isInvisible;
            alert(`Invisibilidade ${isInvisible ? 'Ativada' : 'Desativada'}`);
        }

        // Movimentação no Mobile
        const menu = document.getElementById("menu");
        let isDragging = false;
        let offsetX, offsetY;

        menu.addEventListener("mousedown", startDrag);
        menu.addEventListener("touchstart", startDrag);
        document.addEventListener("mousemove", drag);
        document.addEventListener("touchmove", drag);
        document.addEventListener("mouseup", stopDrag);
        document.addEventListener("touchend", stopDrag);

        function startDrag(e) {
            isDragging = true;
            const touch = e.touches ? e.touches[0] : e;
            offsetX = touch.clientX - menu.offsetLeft;
            offsetY = touch.clientY - menu.offsetTop;
        }

        function drag(e) {
            if (!isDragging) return;
            e.preventDefault();
            const touch = e.touches ? e.touches[0] : e;
            menu.style.left = touch.clientX - offsetX + "px";
            menu.style.top = touch.clientY - offsetY + "px";
        }

        function stopDrag() {
            isDragging = false;
        }
    </script>
</body>
</html>
