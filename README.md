# Year-1
Super Minds
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Catch the Food Answer! - Super Minds AI Edition</title>

    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Google Fonts: Fredoka -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700;800&display=swap" rel="stylesheet">

    <!-- MediaPipe Hands & Camera Utilities -->
    <script src="https://cdn.jsdelivr.net/npm/@mediapipe/camera_utils/camera_utils.js" crossorigin="anonymous"></script>
    <script src="https://cdn.jsdelivr.net/npm/@mediapipe/hands/hands.js" crossorigin="anonymous"></script>

    <style>
        body {
            font-family: 'Fredoka', cursive, sans-serif;
            user-select: none;
            -webkit-user-select: none;
            touch-action: manipulation;
        }

        .cloud-bg {
            background-color: #e0f2fe;
            background-image: radial-gradient(#bae6fd 2.5px, transparent 2.5px);
            background-size: 28px 28px;
        }

        /* Screen Shake Animation */
        @keyframes screenShake {
            0%, 100% { transform: translate(0, 0) rotate(0deg); }
            20% { transform: translate(-8px, 5px) rotate(-1.5deg); }
            40% { transform: translate(8px, -5px) rotate(1.5deg); }
            60% { transform: translate(-5px, -3px) rotate(-1deg); }
            80% { transform: translate(5px, 3px) rotate(1deg); }
        }
        .shake-screen {
            animation: screenShake 0.4s ease-in-out;
        }

        /* Red Mistake Flash Animation */
        @keyframes redFlashAnim {
            0% { opacity: 0.5; }
            100% { opacity: 0; }
        }
        .red-flash-active {
            animation: redFlashAnim 0.35s ease-out forwards;
        }

        /* Modal Pop-in Animation */
        @keyframes popIn {
            0% { transform: scale(0.4); opacity: 0; }
            70% { transform: scale(1.06); opacity: 1; }
            100% { transform: scale(1); opacity: 1; }
        }
        .pop-in {
            animation: popIn 0.35s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        /* Floating Feedback Animation */
        @keyframes floatUpFade {
            0% { transform: translateY(0) scale(0.8); opacity: 1; }
            50% { transform: translateY(-40px) scale(1.2); opacity: 1; }
            100% { transform: translateY(-80px) scale(1); opacity: 0; }
        }
        .float-text {
            animation: floatUpFade 0.95s cubic-bezier(0.22, 1, 0.36, 1) forwards;
        }

        /* Level Up Floating Banner */
        @keyframes levelUpAnim {
            0% { transform: translate(-50%, -20px) scale(0.5); opacity: 0; }
            30% { transform: translate(-50%, 0) scale(1.15); opacity: 1; }
            80% { transform: translate(-50%, -10px) scale(1); opacity: 1; }
            100% { transform: translate(-50%, -40px) scale(0.8); opacity: 0; }
        }
        .level-up-banner {
            animation: levelUpAnim 1.6s ease-out forwards;
        }

        /* Pulse glow effect for combo counter */
        @keyframes pulseGlow {
            0%, 100% { transform: scale(1); filter: drop-shadow(0 0 4px rgba(245, 158, 11, 0.6)); }
            50% { transform: scale(1.08); filter: drop-shadow(0 0 12px rgba(245, 158, 11, 0.9)); }
        }
        .combo-pulse {
            animation: pulseGlow 0.8s infinite ease-in-out;
        }
    </style>
</head>
<body class="cloud-bg min-h-screen flex items-center justify-center p-2 sm:p-4 text-slate-800 overflow-hidden">

    <!-- MAIN GAME CONTAINER -->
    <div id="gameContainer" class="w-full max-w-4xl bg-white/95 backdrop-blur-md border-4 border-amber-400 rounded-3xl shadow-2xl overflow-hidden flex flex-col relative h-[95vh] max-h-[780px] transition-transform">

        <!-- Red Mistake Flash Overlay -->
        <div id="redFlash" class="absolute inset-0 bg-rose-500/30 pointer-events-none z-30 opacity-0"></div>

        <!-- HEADER / HUD BAR -->
        <header class="bg-gradient-to-r from-amber-400 via-orange-400 to-amber-500 p-2.5 sm:p-3 border-b-4 border-amber-600 flex items-center justify-between shadow-md relative z-10 flex-wrap gap-2">
            
            <!-- Game Title & Live Rank Badge -->
            <div class="flex items-center gap-2 sm:gap-3">
                <span class="text-2xl sm:text-3xl">🧺</span>
                <div>
                    <h1 class="text-xs sm:text-base font-extrabold text-white tracking-wide drop-shadow-[0_2px_2px_rgba(0,0,0,0.4)] leading-tight">
                        Catch the Food Answer!
                    </h1>
                    <!-- Real-time Rank Badge -->
                    <div id="rankBadge" class="inline-flex items-center px-2 py-0.5 rounded-full text-[10px] sm:text-xs font-black bg-white/90 text-amber-800 shadow-sm border border-amber-300">
                        🐣 Beginner
                    </div>
                </div>
            </div>

            <!-- Action Controls (Camera Toggle & Sound Toggle) -->
            <div class="flex items-center gap-2">
                <!-- AI Hand Control Toggle Button -->
                <button id="btnCameraToggle" class="bg-white hover:bg-sky-50 text-sky-800 font-bold px-2.5 py-1 sm:px-3 sm:py-1.5 rounded-2xl border-2 border-sky-500 shadow-md active:scale-95 transition-all text-xs sm:text-sm flex items-center gap-1.5">
                    <span id="camBtnIcon">📷</span>
                    <span id="camBtnText">Turn On Camera Control</span>
                </button>

                <!-- Sound Toggle Button -->
                <button id="btnSoundToggle" title="Toggle Sound" class="bg-white/90 hover:bg-white text-slate-700 p-1.5 sm:p-2 rounded-2xl border-2 border-amber-600 shadow-md active:scale-95 transition-all text-sm sm:text-base flex items-center justify-center">
                    <span id="soundIcon">🔊</span>
                </button>
            </div>

            <!-- Stats HUD (Combo, Score, 60s Timer) -->
            <div class="flex items-center gap-1.5 sm:gap-2">
                <!-- Combo Streak Badge -->
                <div id="comboContainer" class="bg-gradient-to-r from-orange-500 to-amber-500 text-white px-2 sm:px-2.5 py-1 rounded-2xl border-2 border-orange-600 shadow-inner flex items-center gap-1 opacity-90 transition-all">
                    <span class="text-xs sm:text-sm">🔥</span>
                    <div class="text-center">
                        <div class="text-[8px] sm:text-[9px] font-bold uppercase tracking-wider leading-none text-orange-100">Combo</div>
                        <div id="comboDisplay" class="text-xs sm:text-sm font-black leading-none">0x</div>
                    </div>
                </div>

                <!-- Score Counter -->
                <div class="bg-white px-2 sm:px-3 py-1 rounded-2xl border-2 border-amber-600 shadow-inner flex items-center gap-1">
                    <span class="text-xs sm:text-sm">⭐</span>
                    <div>
                        <div class="text-[8px] sm:text-[9px] text-amber-800 font-bold uppercase tracking-wider leading-none">Score</div>
                        <div id="scoreDisplay" class="text-xs sm:text-sm font-black text-amber-600 leading-none">0</div>
                    </div>
                </div>

                <!-- 60s Timer Card -->
                <div id="timerCard" class="bg-white px-2 sm:px-3 py-1 rounded-2xl border-2 border-amber-600 shadow-inner flex items-center gap-1 min-w-[60px] sm:min-w-[75px]">
                    <span class="text-xs sm:text-sm">⏱️</span>
                    <div>
                        <div class="text-[8px] sm:text-[9px] text-amber-800 font-bold uppercase tracking-wider leading-none">Time</div>
                        <div id="timerDisplay" class="text-xs sm:text-sm font-black text-sky-600 leading-none">60s</div>
                    </div>
                </div>
            </div>
        </header>

        <!-- MAIN GAMEPLAY CANVAS AREA -->
        <main class="relative flex-1 bg-gradient-to-b from-sky-100 via-emerald-50 to-amber-50 flex flex-col items-center justify-between p-2 overflow-hidden">
            
            <!-- Target Food Prompt Flashcard -->
            <div id="foodPromptBanner" class="w-full max-w-lg bg-white/95 border-4 border-sky-400 rounded-2xl p-2 sm:p-2.5 shadow-lg flex items-center justify-center gap-3 z-10 transition-all duration-300">
                <div id="promptSvgContainer" class="w-12 h-12 sm:w-16 sm:h-16 flex-shrink-0 bg-sky-50 rounded-xl p-1 border-2 border-sky-200 flex items-center justify-center shadow-inner">
                    <!-- Dynamic SVG injected here -->
                </div>
                <div class="flex-1">
                    <span id="promptCategoryBadge" class="bg-sky-200 text-sky-800 text-[10px] sm:text-xs font-black px-2 py-0.5 rounded-full uppercase tracking-wide">Super Minds Module 4</span>
                    <h2 id="promptHeadingText" class="text-sm sm:text-lg font-black text-slate-800 leading-tight mt-0.5">
                        Catch the correct word!
                    </h2>
                    <p class="text-[10px] sm:text-xs text-slate-600 font-semibold">Which falling choice matches this food picture?</p>
                </div>
            </div>

            <!-- Picture-in-Picture Floating Camera Window -->
            <div id="cameraPipWindow" class="absolute top-16 right-3 w-32 h-24 sm:w-44 sm:h-32 bg-slate-900 border-4 border-sky-400 rounded-2xl shadow-2xl z-20 overflow-hidden flex flex-col hidden transition-all">
                <!-- Video & Hand Landmark Canvas -->
                <div class="relative w-full h-full">
                    <video id="webcamVideo" class="w-full h-full object-cover -scale-x-100" playsinline></video>
                    <canvas id="handOverlayCanvas" class="w-full h-full absolute inset-0 -scale-x-100 pointer-events-none"></canvas>
                    
                    <!-- Hand Detection Active Status Badge -->
                    <div id="handStatusBadge" class="absolute bottom-1 left-1/2 -translate-x-1/2 px-2 py-0.5 rounded-full text-[9px] sm:text-[10px] font-black bg-slate-800/85 text-slate-300 backdrop-blur flex items-center gap-1 border border-white/20 whitespace-nowrap shadow-md">
                        <span id="handStatusDot" class="w-2 h-2 rounded-full bg-rose-500"></span>
                        <span id="handStatusText">No Hand</span>
                    </div>
                </div>
            </div>

            <!-- Physics Game Canvas -->
            <canvas id="gameCanvas" class="w-full h-full absolute inset-0 cursor-pointer"></canvas>

            <!-- On-Screen Touch Controls (Mobile/Touch Fallback) -->
            <div class="sm:hidden absolute bottom-2 inset-x-4 flex justify-between pointer-events-none z-20">
                <button id="btnLeft" class="pointer-events-auto bg-amber-400 active:bg-amber-500 border-4 border-amber-600 text-white font-black text-2xl w-14 h-14 rounded-2xl shadow-lg flex items-center justify-center active:scale-95 transition-transform">
                    ◀
                </button>
                <button id="btnRight" class="pointer-events-auto bg-amber-400 active:bg-amber-500 border-4 border-amber-600 text-white font-black text-2xl w-14 h-14 rounded-2xl shadow-lg flex items-center justify-center active:scale-95 transition-transform">
                    ▶
                </button>
            </div>

            <!-- Floating Text Feedback Overlay Container -->
            <div id="feedbackContainer" class="absolute inset-0 pointer-events-none z-30 flex flex-col items-center justify-center"></div>

            <!-- START SCREEN MODAL -->
            <div id="startModal" class="absolute inset-0 bg-slate-900/60 backdrop-blur-sm z-40 flex items-center justify-center p-3 sm:p-4">
                <div class="bg-white border-8 border-amber-400 rounded-3xl p-4 sm:p-6 max-w-lg w-full text-center shadow-2xl relative pop-in">
                    <div class="w-16 h-16 sm:w-20 sm:h-20 bg-amber-100 rounded-full border-4 border-amber-400 flex items-center justify-center mx-auto mb-2 text-3xl sm:text-4xl shadow-md">
                        🍕
                    </div>
                    <h2 class="text-2xl sm:text-3xl font-black text-amber-500 tracking-wide mb-1">
                        Catch the Food Answer!
                    </h2>
                    <p class="text-xs sm:text-sm font-bold text-slate-500 mb-2.5 uppercase tracking-wider">Super Minds Module 4: Food, Please!</p>

                    <!-- Instructions Card -->
                    <div class="bg-amber-50 border-2 border-amber-200 rounded-2xl p-2.5 sm:p-3.5 text-left mb-3 space-y-1.5 text-xs sm:text-sm text-slate-700 font-semibold">
                        <div class="flex items-center gap-2">
                            <span class="text-base sm:text-lg">👀</span>
                            <span>Look at the <b>food picture</b> at the top.</span>
                        </div>
                        <div class="flex items-center gap-2">
                            <span class="text-base sm:text-lg">⏱️</span>
                            <span>Answers fall in <b>7 seconds!</b> (60s game clock).</span>
                        </div>
                        <div class="flex items-center gap-2">
                            <span class="text-base sm:text-lg">📷</span>
                            <span><b>AI Hand Control:</b> Wave your hand at camera to guide the basket, or use Mouse/Keyboard/Touch!</span>
                        </div>
                        <div class="flex items-center gap-2">
                            <span class="text-base sm:text-lg">🎯</span>
                            <span><b>Scoring:</b> Correct answer = <b class="text-emerald-600">+10 pts</b> | Wrong answer = <b class="text-rose-600">-5 pts</b>.</span>
                        </div>
                        <div class="flex items-center gap-2">
                            <span class="text-base sm:text-lg">🔥</span>
                            <span><b>Combo System:</b> 3 consecutive correct catches triggers <b class="text-orange-600">+5 BONUS!</b></span>
                        </div>
                        <div class="flex items-center gap-2">
                            <span class="text-base sm:text-lg">🏆</span>
                            <span><b>Ranks:</b> 🐣 Beginner (&lt;50) | 🌟 Great (50-95) | 🏆 AI Master (100+)</span>
                        </div>
                    </div>

                    <!-- Personal High Score Badge -->
                    <div class="mb-3 text-xs font-black text-slate-500 uppercase tracking-wide">
                        🏆 Personal Best: <span id="startHighScore" class="text-amber-600 font-extrabold text-base">0</span>
                    </div>

                    <div class="flex flex-col sm:flex-row gap-2">
                        <button id="btnStartWithCam" class="flex-1 bg-gradient-to-r from-sky-400 to-blue-500 hover:from-sky-500 hover:to-blue-600 text-white font-black text-base sm:text-lg py-2.5 rounded-2xl border-b-4 border-blue-700 shadow-lg active:translate-y-0.5 transition-all flex items-center justify-center gap-1.5">
                            📷 Play with AI Camera
                        </button>
                        <button id="btnStartNormal" class="flex-1 bg-gradient-to-r from-emerald-400 to-green-500 hover:from-emerald-500 hover:to-green-600 text-white font-black text-base sm:text-lg py-2.5 rounded-2xl border-b-4 border-green-700 shadow-lg active:translate-y-0.5 transition-all flex items-center justify-center gap-1.5">
                            🎮 Play Normal
                        </button>
                    </div>
                </div>
            </div>

            <!-- GAME OVER MODAL -->
            <div id="gameOverModal" class="absolute inset-0 bg-slate-900/70 backdrop-blur-sm z-40 flex items-center justify-center p-4 hidden">
                <div class="bg-white border-8 border-amber-400 rounded-3xl p-5 sm:p-6 max-w-lg w-full text-center shadow-2xl relative pop-in">
                    
                    <div class="text-4xl sm:text-5xl mb-2" id="starContainer">⭐⭐⭐</div>

                    <div id="endRankBadge" class="inline-flex items-center px-3 py-1 rounded-full text-xs sm:text-sm font-black bg-purple-100 text-purple-800 border-2 border-purple-300 mb-2">
                        🏆 AI Master
                    </div>

                    <h2 class="text-2xl sm:text-3xl font-black text-amber-500 mb-1" id="gameOverHeading">
                        Awesome Job! 🎉
                    </h2>
                    <p id="praiseMessage" class="text-slate-600 font-bold text-xs sm:text-sm mb-3">
                        You are a Super Minds Food Master!
                    </p>

                    <!-- Score Breakdown Grid -->
                    <div class="bg-amber-50 border-2 border-amber-200 rounded-2xl p-3 mb-4 grid grid-cols-3 gap-2">
                        <div class="bg-white p-2 rounded-xl border border-amber-200 shadow-sm text-center">
                            <div class="text-[9px] sm:text-xs text-slate-500 font-bold uppercase">Final Score</div>
                            <div id="finalScore" class="text-lg sm:text-2xl font-black text-amber-500">0</div>
                        </div>
                        <div class="bg-white p-2 rounded-xl border border-amber-200 shadow-sm text-center">
                            <div class="text-[9px] sm:text-xs text-slate-500 font-bold uppercase">Catches</div>
                            <div id="finalCatches" class="text-lg sm:text-2xl font-black text-emerald-500">0</div>
                        </div>
                        <div class="bg-white p-2 rounded-xl border border-amber-200 shadow-sm text-center">
                            <div class="text-[9px] sm:text-xs text-slate-500 font-bold uppercase">Max Combo</div>
                            <div id="finalMaxCombo" class="text-lg sm:text-2xl font-black text-orange-500">0x</div>
                        </div>
                    </div>

                    <div class="mb-4 text-xs font-black text-slate-600 uppercase tracking-wide">
                        🥇 Personal Best: <span id="endHighScore" class="text-amber-600 text-base">0</span>
                    </div>

                    <button id="btnPlayAgain" class="w-full bg-gradient-to-r from-sky-400 to-blue-500 hover:from-sky-500 hover:to-blue-600 text-white font-black text-xl sm:text-2xl py-3 rounded-2xl border-b-8 border-blue-700 shadow-xl active:translate-y-1 active:border-b-4 transition-all">
                        PLAY AGAIN 🔄
                    </button>
                </div>
            </div>

            <!-- Toast Message Banner -->
            <div id="toastNotification" class="absolute bottom-16 left-1/2 -translate-x-1/2 bg-slate-900/90 text-white px-4 py-2 rounded-2xl text-xs font-bold shadow-2xl z-50 opacity-0 pointer-events-none transition-opacity duration-300 border border-white/20 flex items-center gap-2">
                <span id="toastIcon">ℹ️</span>
                <span id="toastMessage">Notification text</span>
            </div>

        </main>
    </div>

    <!-- JAVASCRIPT GAME LOGIC -->
    <script>
        class SoundEngine {
            constructor() {
                this.ctx = null;
                this.muted = false;
            }

            init() {
                if (!this.ctx) {
                    const AudioCtx = window.AudioContext || window.webkitAudioContext;
                    this.ctx = new AudioCtx();
                }
                if (this.ctx.state === 'suspended') {
                    this.ctx.resume();
                }
            }

            toggleSound() {
                this.muted = !this.muted;
                return !this.muted;
            }

            playCorrect() {
                if (this.muted) return;
                this.init();
                if (!this.ctx) return;

                const notes = [523.25, 659.25, 783.99, 1046.50]; // C5, E5, G5, C6
                notes.forEach((freq, idx) => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(freq, this.ctx.currentTime + idx * 0.05);
                    gain.gain.setValueAtTime(0.2, this.ctx.currentTime + idx * 0.05);
                    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + idx * 0.05 + 0.2);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(this.ctx.currentTime + idx * 0.05);
                    osc.stop(this.ctx.currentTime + idx * 0.05 + 0.2);
                });
            }

            playComboFanfare() {
                if (this.muted) return;
                this.init();
                if (!this.ctx) return;

                const notes = [523.25, 659.25, 783.99, 1046.50, 1318.51];
                notes.forEach((freq, idx) => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, this.ctx.currentTime + idx * 0.07);
                    gain.gain.setValueAtTime(0.25, this.ctx.currentTime + idx * 0.07);
                    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + idx * 0.07 + 0.3);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(this.ctx.currentTime + idx * 0.07);
                    osc.stop(this.ctx.currentTime + idx * 0.07 + 0.3);
                });
            }

            playLevelUp() {
                if (this.muted) return;
                this.init();
                if (!this.ctx) return;

                const notes = [440, 554.37, 659.25, 880, 1108.73];
                notes.forEach((freq, idx) => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(freq, this.ctx.currentTime + idx * 0.08);
                    gain.gain.setValueAtTime(0.25, this.ctx.currentTime + idx * 0.08);
                    gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + idx * 0.08 + 0.35);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(this.ctx.currentTime + idx * 0.08);
                    osc.stop(this.ctx.currentTime + idx * 0.08 + 0.35);
                });
            }

            playWrong() {
                if (this.muted) return;
                this.init();
                if (!this.ctx) return;

                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(180, this.ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(80, this.ctx.currentTime + 0.25);
                gain.gain.setValueAtTime(0.2, this.ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + 0.25);
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start();
                osc.stop(this.ctx.currentTime + 0.25);
            }

            playTick() {
                if (this.muted) return;
                this.init();
                if (!this.ctx) return;

                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(880, this.ctx.currentTime);
                gain.gain.setValueAtTime(0.12, this.ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + 0.05);
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start();
                osc.stop(this.ctx.currentTime + 0.05);
            }

            playVictory() {
                if (this.muted) return;
                this.init();
                if (!this.ctx) return;

                const tune = [
                    { f: 523.25, d: 0.12 }, { f: 659.25, d: 0.12 }, { f: 783.99, d: 0.12 },
                    { f: 1046.50, d: 0.4 }
                ];
                let time = this.ctx.currentTime;
                tune.forEach(note => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(note.f, time);
                    gain.gain.setValueAtTime(0.25, time);
                    gain.gain.exponentialRampToValueAtTime(0.001, time + note.d);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(time);
                    osc.stop(time + note.d);
                    time += note.d + 0.04;
                });
            }

            playClick() {
                if (this.muted) return;
                this.init();
                if (!this.ctx) return;

                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(750, this.ctx.currentTime);
                gain.gain.setValueAtTime(0.1, this.ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + 0.04);
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start();
                osc.stop(this.ctx.currentTime + 0.04);
            }
        }

        const audio = new SoundEngine();

        // Sound Toggle Handler
        const soundBtn = document.getElementById('btnSoundToggle');
        const soundIcon = document.getElementById('soundIcon');
        soundBtn.addEventListener('click', () => {
            const isEnabled = audio.toggleSound();
            soundIcon.innerText = isEnabled ? '🔊' : '🔇';
        });

        const FOOD_DATABASE = [
            { id: 'apples', name: 'Apples', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M50 25 C30 10, 15 35, 20 65 C25 85, 45 92, 50 82 C55 92, 75 85, 80 65 C85 35, 70 10, 50 25 Z" fill="#ef4444" stroke="#b91c1c" stroke-width="3"/><path d="M48 24 C45 15, 52 10, 56 8" fill="none" stroke="#78350f" stroke-width="4" stroke-linecap="round"/><path d="M55 12 C65 10, 70 18, 62 20 Z" fill="#22c55e" stroke="#15803d" stroke-width="2"/><ellipse cx="35" cy="40" rx="4" ry="10" fill="#fca5a5" transform="rotate(-20 35 40)"/></svg>` },
            { id: 'bananas', name: 'Bananas', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M20 30 C35 25, 70 30, 80 75 C70 80, 40 75, 20 30 Z" fill="#facc15" stroke="#ca8a04" stroke-width="3"/><path d="M20 30 C30 35, 60 45, 80 75" fill="none" stroke="#eab308" stroke-width="2"/><path d="M18 28 L23 32" stroke="#78350f" stroke-width="5" stroke-linecap="round"/><path d="M79 74 L83 78" stroke="#78350f" stroke-width="4" stroke-linecap="round"/></svg>` },
            { id: 'cheese', name: 'Cheese', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M15 65 L85 65 L70 30 L15 65 Z" fill="#facc15" stroke="#ca8a04" stroke-width="3"/><path d="M15 65 L85 65 L85 75 L15 75 Z" fill="#eab308" stroke="#ca8a04" stroke-width="3"/><circle cx="35" cy="55" r="5" fill="#ca8a04"/><circle cx="55" cy="50" r="7" fill="#ca8a04"/><circle cx="68" cy="60" r="4" fill="#ca8a04"/></svg>` },
            { id: 'pizza', name: 'Pizza', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M50 15 L85 80 L15 80 Z" fill="#f59e0b" stroke="#d97706" stroke-width="3"/><path d="M15 80 Q50 90 85 80 L85 85 Q50 95 15 85 Z" fill="#b45309" stroke="#78350f" stroke-width="2"/><circle cx="45" cy="45" r="6" fill="#ef4444"/><circle cx="60" cy="65" r="7" fill="#ef4444"/><circle cx="35" cy="68" r="6" fill="#ef4444"/><path d="M40 30 Q50 35 60 28" stroke="#22c55e" stroke-width="3" fill="none"/></svg>` },
            { id: 'sausages', name: 'Sausages', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><rect x="20" y="30" width="60" height="20" rx="10" fill="#dc2626" stroke="#991b1b" stroke-width="3" transform="rotate(-15 50 40)"/><rect x="20" y="55" width="60" height="20" rx="10" fill="#dc2626" stroke="#991b1b" stroke-width="3" transform="rotate(10 50 65)"/></svg>` },
            { id: 'chicken', name: 'Chicken', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M30 65 C20 40, 50 25, 75 40 C85 55, 65 80, 45 75 Z" fill="#b45309" stroke="#78350f" stroke-width="3"/><path d="M30 65 L15 75" stroke="#f8fafc" stroke-width="8" stroke-linecap="round"/><circle cx="12" cy="73" r="5" fill="#f8fafc"/><circle cx="16" cy="79" r="5" fill="#f8fafc"/></svg>` },
            { id: 'carrots', name: 'Carrots', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M50 85 L30 35 C28 25, 60 25, 58 35 Z" fill="#f97316" stroke="#c2410c" stroke-width="3"/><path d="M40 28 Q45 10 30 10 M44 28 Q50 8 50 8 M48 28 Q55 10 70 12" fill="none" stroke="#22c55e" stroke-width="4" stroke-linecap="round"/><line x1="38" y1="45" x2="48" y2="47" stroke="#ea580c" stroke-width="2"/><line x1="42" y1="60" x2="50" y2="62" stroke="#ea580c" stroke-width="2"/></svg>` },
            { id: 'peas', name: 'Peas', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M15 50 Q50 25 85 50 Q50 80 15 50 Z" fill="#4ade80" stroke="#15803d" stroke-width="3"/><circle cx="33" cy="50" r="8" fill="#22c55e"/><circle cx="50" cy="50" r="8" fill="#22c55e"/><circle cx="67" cy="50" r="8" fill="#22c55e"/></svg>` },
            { id: 'cake', name: 'Cake', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M20 50 L80 50 L80 80 L20 80 Z" fill="#f472b6" stroke="#be185d" stroke-width="3"/><path d="M20 50 L50 25 L80 50 Z" fill="#fbcfe8" stroke="#be185d" stroke-width="3"/><path d="M20 65 L80 65" stroke="#fbcfe8" stroke-width="4"/><circle cx="50" cy="22" r="6" fill="#ef4444"/></svg>` },
            { id: 'milk', name: 'Milk', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M30 35 L40 20 L60 20 L70 35 L70 85 L30 85 Z" fill="#f8fafc" stroke="#64748b" stroke-width="3"/><path d="M30 50 L70 50 L70 70 L30 70 Z" fill="#38bdf8"/><path d="M45 55 L55 65 M55 55 L45 65" stroke="#ffffff" stroke-width="3"/></svg>` },
            { id: 'icecream', name: 'Ice cream', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><polygon points="35,50 65,50 50,90" fill="#d97706" stroke="#78350f" stroke-width="3"/><circle cx="50" cy="42" r="18" fill="#f472b6" stroke="#be185d" stroke-width="3"/><circle cx="50" cy="28" r="14" fill="#38bdf8" stroke="#0284c7" stroke-width="3"/><circle cx="50" cy="12" r="5" fill="#ef4444"/></svg>` },
            { id: 'steak', name: 'Steak', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M25 35 C20 20, 70 20, 80 35 C90 55, 75 80, 50 75 C25 70, 15 50, 25 35 Z" fill="#9f1239" stroke="#4c0519" stroke-width="3"/><path d="M35 40 Q50 45 65 38 M30 55 Q50 60 70 52" stroke="#be123c" stroke-width="4" stroke-linecap="round"/><circle cx="35" cy="35" r="4" fill="#fecdd3"/></svg>` },
            { id: 'burger', name: 'Burger', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M20 45 C20 20, 80 20, 80 45 Z" fill="#f59e0b" stroke="#b45309" stroke-width="3"/><rect x="15" y="47" width="70" height="10" rx="3" fill="#22c55e"/><rect x="15" y="58" width="70" height="12" rx="4" fill="#78350f"/><rect x="20" y="72" width="60" height="12" rx="5" fill="#f59e0b" stroke="#b45309" stroke-width="3"/></svg>` },
            { id: 'orangejuice', name: 'Orange juice', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M30 30 L35 85 L65 85 L70 30 Z" fill="#38bdf8" fill-opacity="0.3" stroke="#0284c7" stroke-width="3"/><path d="M33 40 L35 83 L65 83 L67 40 Z" fill="#f97316"/><line x1="55" y1="15" x2="45" y2="80" stroke="#facc15" stroke-width="5" stroke-linecap="round"/></svg>` },
            { id: 'salad', name: 'Salad', svg: `<svg viewBox="0 0 100 100" class="w-full h-full"><path d="M20 50 Q50 85 80 50 Z" fill="#0284c7" stroke="#0369a1" stroke-width="3"/><circle cx="35" cy="45" r="10" fill="#4ade80"/><circle cx="50" cy="40" r="12" fill="#22c55e"/><circle cx="65" cy="45" r="10" fill="#16a34a"/><circle cx="42" cy="48" r="5" fill="#ef4444"/><circle cx="58" cy="46" r="4" fill="#ef4444"/></svg>` }
        ];

        /* Gameplay State */
        let score = 0;
        let timeLeft = 60; // 60s Countdown Timer
        let comboStreak = 0;
        let maxCombo = 0;
        let catchesCount = 0;
        let currentRankLevel = 0; // 0: Beginner, 1: Great, 2: AI Master

        let gameTimer = null;
        let animationFrameId = null;
        let isGameRunning = false;

        let currentTargetFood = null;
        let fallingChoices = [];
        let particles = [];

        let isCameraActive = false;
        let handsModel = null;
        let cameraInstance = null;
        let isHandDetected = false;
        let trackedHandXNorm = 0.5; // Normalized hand X position [0, 1]

        const videoElement = document.getElementById('webcamVideo');
        const cameraPipWindow = document.getElementById('cameraPipWindow');
        const btnCameraToggle = document.getElementById('btnCameraToggle');
        const camBtnText = document.getElementById('camBtnText');
        const camBtnIcon = document.getElementById('camBtnIcon');

        function showToast(message, icon = "ℹ️") {
            const toast = document.getElementById('toastNotification');
            document.getElementById('toastMessage').innerText = message;
            document.getElementById('toastIcon').innerText = icon;
            toast.classList.remove('opacity-0');
            setTimeout(() => {
                toast.classList.add('opacity-0');
            }, 3000);
        }

        async function initMediaPipeHands() {
            if (handsModel) return true;
            if (typeof Hands === 'undefined') {
                showToast("MediaPipe library not loaded. Check connection.", "⚠️");
                return false;
            }

            try {
                handsModel = new Hands({
                    locateFile: (file) => `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`
                });

                handsModel.setOptions({
                    maxNumHands: 1,
                    modelComplexity: 1,
                    minDetectionConfidence: 0.5,
                    minTrackingConfidence: 0.5
                });

                handsModel.onResults(onHandResults);
                return true;
            } catch (err) {
                console.error("Failed to init MediaPipe:", err);
                return false;
            }
        }

        async function startCameraControl() {
            camBtnText.innerText = "Initializing AI...";
            const success = await initMediaPipeHands();

            if (!success) {
                showToast("Camera AI initialization failed. Using keyboard/mouse!", "⚠️");
                camBtnText.innerText = "Turn On Camera Control";
                return;
            }

            try {
                if (typeof Camera === 'undefined') {
                    showToast("Camera Utils library missing.", "⚠️");
                    camBtnText.innerText = "Turn On Camera Control";
                    return;
                }

                cameraInstance = new Camera(videoElement, {
                    onFrame: async () => {
                        if (isCameraActive && handsModel) {
                            await handsModel.send({ image: videoElement });
                        }
                    },
                    width: 320,
                    height: 240
                });

                await cameraInstance.start();
                isCameraActive = true;
                cameraPipWindow.classList.remove('hidden');
                btnCameraToggle.classList.replace('bg-white', 'bg-emerald-100');
                btnCameraToggle.classList.replace('text-sky-800', 'text-emerald-800');
                camBtnIcon.innerText = '🟢';
                camBtnText.innerText = 'Camera Control Active';
                showToast("Wave hand at camera to control basket!", "📷");

            } catch (err) {
                console.error("Camera access error:", err);
                showToast("Camera permission denied. Use Mouse/Touch!", "⚠️");
                camBtnText.innerText = "Turn On Camera Control";
            }
        }

        function stopCameraControl() {
            isCameraActive = false;
            if (cameraInstance) {
                try { cameraInstance.stop(); } catch(e){}
            }
            cameraPipWindow.classList.add('hidden');
            btnCameraToggle.classList.replace('bg-emerald-100', 'bg-white');
            btnCameraToggle.classList.replace('text-emerald-800', 'text-sky-800');
            camBtnIcon.innerText = '📷';
            camBtnText.innerText = 'Turn On Camera Control';
            setHandDetectedState(false);
        }

        btnCameraToggle.addEventListener('click', () => {
            audio.playClick();
            if (isCameraActive) {
                stopCameraControl();
            } else {
                startCameraControl();
            }
        });

        function setHandDetectedState(detected) {
            isHandDetected = detected;
            const dot = document.getElementById('handStatusDot');
            const txt = document.getElementById('handStatusText');
            const pip = document.getElementById('cameraPipWindow');

            if (detected) {
                dot.className = "w-2 h-2 rounded-full bg-emerald-400 animate-ping";
                txt.innerText = "✋ Hand Control Active";
                txt.className = "text-emerald-300 font-bold";
                pip.classList.replace('border-sky-400', 'border-emerald-400');
            } else {
                dot.className = "w-2 h-2 rounded-full bg-rose-500";
                txt.innerText = "No Hand Detected";
                txt.className = "text-slate-300";
                pip.classList.replace('border-emerald-400', 'border-sky-400');
            }
        }

        function onHandResults(results) {
            const handOverlay = document.getElementById('handOverlayCanvas');
            const handCtx = handOverlay.getContext('2d');

            if (handOverlay.width !== videoElement.videoWidth && videoElement.videoWidth > 0) {
                handOverlay.width = videoElement.videoWidth;
                handOverlay.height = videoElement.videoHeight;
            }

            handCtx.clearRect(0, 0, handOverlay.width, handOverlay.height);

            if (results.multiHandLandmarks && results.multiHandLandmarks.length > 0) {
                const landmarks = results.multiHandLandmarks[0];
                setHandDetectedState(true);

                // Draw Hand Landmark Skeleton on PIP overlay
                drawHandSkeleton(handCtx, landmarks, handOverlay.width, handOverlay.height);

                // Track Index Tip (Landmark 8) or Wrist (Landmark 0)
                const trackedPoint = landmarks[8] || landmarks[9];

                // Mirrored horizontal position => physical right is 1 - landmark.x
                trackedHandXNorm = 1 - trackedPoint.x;

                // Smooth position translation to basket coordinates
                const targetBasketX = trackedHandXNorm * canvas.width - basket.width / 2;
                basket.x += (targetBasketX - basket.x) * 0.35; // Exponential lerp
                clampBasket();

            } else {
                setHandDetectedState(false);
            }
        }

        function drawHandSkeleton(ctx, landmarks, width, height) {
            const connections = [
                [0,1],[1,2],[2,3],[3,4],
                [0,5],[5,6],[6,7],[7,8],
                [5,9],[9,10],[10,11],[11,12],
                [9,13],[13,14],[14,15],[15,16],
                [13,17],[17,18],[18,19],[19,20],[0,17]
            ];

            ctx.strokeStyle = '#f59e0b';
            ctx.lineWidth = 3;

            connections.forEach(([i, j]) => {
                const p1 = landmarks[i];
                const p2 = landmarks[j];
                ctx.beginPath();
                ctx.moveTo(p1.x * width, p1.y * height);
                ctx.lineTo(p2.x * width, p2.y * height);
                ctx.stroke();
            });

            landmarks.forEach((lm, idx) => {
                ctx.fillStyle = (idx === 8) ? '#22c55e' : '#38bdf8';
                ctx.beginPath();
                ctx.arc(lm.x * width, lm.y * height, (idx === 8) ? 6 : 3.5, 0, Math.PI * 2);
                ctx.fill();
            });
        }

        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let basket = {
            x: 0,
            y: 0,
            width: 120,
            height: 55,
            speed: 10
        };

        const keys = { left: false, right: false };

        function resizeCanvas() {
            const rect = canvas.parentElement.getBoundingClientRect();
            canvas.width = rect.width;
            canvas.height = rect.height;
            basket.y = canvas.height - basket.height - 15;
            if (basket.x === 0) {
                basket.x = canvas.width / 2 - basket.width / 2;
            }
        }
        window.addEventListener('resize', resizeCanvas);

        // Keyboard listeners
        window.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') keys.left = true;
            if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') keys.right = true;
        });

        window.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') keys.left = false;
            if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') keys.right = false;
        });

        // Mouse & Touch listeners (Fallback when hand control isn't active)
        canvas.addEventListener('mousemove', (e) => {
            if (!isGameRunning || isHandDetected) return;
            const rect = canvas.getBoundingClientRect();
            basket.x = (e.clientX - rect.left) - basket.width / 2;
            clampBasket();
        });

        canvas.addEventListener('touchmove', (e) => {
            if (!isGameRunning || isHandDetected || e.touches.length === 0) return;
            const rect = canvas.getBoundingClientRect();
            basket.x = (e.touches[0].clientX - rect.left) - basket.width / 2;
            clampBasket();
            e.preventDefault();
        }, { passive: false });

        // Touch Control Buttons
        const btnLeft = document.getElementById('btnLeft');
        const btnRight = document.getElementById('btnRight');
        btnLeft.addEventListener('touchstart', (e) => { keys.left = true; e.preventDefault(); });
        btnLeft.addEventListener('touchend', () => { keys.left = false; });
        btnLeft.addEventListener('mousedown', () => { keys.left = true; });
        btnLeft.addEventListener('mouseup', () => { keys.left = false; });

        btnRight.addEventListener('touchstart', (e) => { keys.right = true; e.preventDefault(); });
        btnRight.addEventListener('touchend', () => { keys.right = false; });
        btnRight.addEventListener('mousedown', () => { keys.right = true; });
        btnRight.addEventListener('mouseup', () => { keys.right = false; });

        function clampBasket() {
            if (basket.x < 0) basket.x = 0;
            if (basket.x + basket.width > canvas.width) basket.x = canvas.width - basket.width;
        }

        /* Rank & Progression System */
        function updateRankBadge() {
            let rankText = "🐣 Beginner";
            let rankClass = "bg-white/90 text-amber-800 border-amber-300";
            let newRankLevel = 0;

            if (score >= 100) {
                rankText = "🏆 AI Master";
                rankClass = "bg-purple-600 text-white border-purple-300 shadow-md";
                newRankLevel = 2;
            } else if (score >= 50) {
                rankText = "🌟 Great";
                rankClass = "bg-emerald-500 text-white border-emerald-300 shadow-sm";
                newRankLevel = 1;
            }

            if (isGameRunning && newRankLevel > currentRankLevel) {
                currentRankLevel = newRankLevel;
                audio.playLevelUp();
                showLevelUpBanner(rankText);
            } else {
                currentRankLevel = newRankLevel;
            }

            const badge = document.getElementById('rankBadge');
            badge.className = `inline-flex items-center px-2 py-0.5 rounded-full text-[10px] sm:text-xs font-black shadow-sm border ${rankClass}`;
            badge.innerText = rankText;
        }

        function showLevelUpBanner(rankTitle) {
            const container = document.getElementById('feedbackContainer');
            const el = document.createElement('div');
            el.className = 'absolute top-1/3 left-1/2 -translate-x-1/2 bg-gradient-to-r from-purple-600 to-indigo-600 text-white border-4 border-yellow-300 px-6 py-2.5 rounded-3xl font-black text-xl sm:text-2xl shadow-2xl level-up-banner z-40 pointer-events-none flex items-center gap-2';
            el.innerHTML = `🎉 LEVEL UP! ${rankTitle}!`;
            container.appendChild(el);
            setTimeout(() => el.remove(), 1600);
        }

        function triggerParticles(x, y, isCorrect) {
            const count = isCorrect ? 25 : 12;
            const colors = isCorrect ? ['#facc15', '#4ade80', '#38bdf8', '#f472b6', '#a855f7'] : ['#ef4444', '#f97316'];

            for (let i = 0; i < count; i++) {
                particles.push({
                    x: x,
                    y: y,
                    vx: (Math.random() - 0.5) * 10,
                    vy: (Math.random() - 0.7) * 10,
                    radius: Math.random() * 6 + 3,
                    color: colors[Math.floor(Math.random() * colors.length)],
                    alpha: 1,
                    decay: Math.random() * 0.03 + 0.015
                });
            }
        }

        function showFloatingText(x, y, text, isCorrect, isCombo = false) {
            const container = document.getElementById('feedbackContainer');
            const el = document.createElement('div');
            
            let colorClasses = isCorrect 
                ? 'text-emerald-600 border-emerald-400 bg-white' 
                : 'text-rose-600 border-rose-400 bg-white';
            
            if (isCombo) {
                colorClasses = 'text-white bg-gradient-to-r from-orange-500 to-amber-500 border-yellow-300 combo-pulse';
            }

            el.className = `absolute px-4 py-1.5 rounded-2xl font-black text-lg sm:text-xl border-4 shadow-2xl float-text pointer-events-none z-30 ${colorClasses}`;
            el.style.left = `${Math.min(canvas.width - 130, Math.max(20, x))}px`;
            el.style.top = `${y - 40}px`;
            el.innerText = text;

            container.appendChild(el);
            setTimeout(() => el.remove(), 950);
        }

        function spawnQuestionRound() {
            const foodIndex = Math.floor(Math.random() * FOOD_DATABASE.length);
            currentTargetFood = FOOD_DATABASE[foodIndex];

            document.getElementById('promptSvgContainer').innerHTML = currentTargetFood.svg;

            const distractors = FOOD_DATABASE.filter(f => f.id !== currentTargetFood.id);
            const shuffled = distractors.sort(() => 0.5 - Math.random()).slice(0, 2);

            const options = [
                { name: currentTargetFood.name, isCorrect: true },
                { name: shuffled[0].name, isCorrect: false },
                { name: shuffled[1].name, isCorrect: false }
            ].sort(() => 0.5 - Math.random());

            // 7-SECOND FALL SPEED FORMULA
            const fallDurationFrames = 7 * 60; // 7 Seconds at 60fps
            const fallSpeed = Math.max(1.3, (canvas.height - 70) / fallDurationFrames);

            const boxWidth = Math.min(180, (canvas.width - 40) / 3);
            const boxHeight = 48;
            const laneWidth = canvas.width / 3;

            fallingChoices = options.map((opt, idx) => {
                const laneX = idx * laneWidth + (laneWidth - boxWidth) / 2;
                return {
                    text: opt.name,
                    isCorrect: opt.isCorrect,
                    x: laneX,
                    y: -boxHeight - (Math.random() * 15),
                    width: boxWidth,
                    height: boxHeight,
                    speed: fallSpeed
                };
            });
        }

        function drawBasket() {
            ctx.save();
            
            // Basket Drop Shadow
            ctx.fillStyle = 'rgba(0, 0, 0, 0.15)';
            ctx.beginPath();
            ctx.ellipse(basket.x + basket.width / 2, basket.y + basket.height, basket.width / 2 + 6, 8, 0, 0, Math.PI * 2);
            ctx.fill();

            // Main Basket Body
            ctx.fillStyle = '#d97706';
            ctx.strokeStyle = '#78350f';
            ctx.lineWidth = 3.5;

            ctx.beginPath();
            ctx.roundRect(basket.x, basket.y + 10, basket.width, basket.height - 10, [8, 8, 22, 22]);
            ctx.fill();
            ctx.stroke();

            // Basket Rim
            ctx.fillStyle = '#f59e0b';
            ctx.beginPath();
            ctx.roundRect(basket.x - 6, basket.y, basket.width + 12, 15, [8]);
            ctx.fill();
            ctx.stroke();

            // Weave lines
            ctx.strokeStyle = '#b45309';
            ctx.lineWidth = 2.5;
            for (let x = basket.x + 12; x < basket.x + basket.width; x += 16) {
                ctx.beginPath();
                ctx.moveTo(x, basket.y + 15);
                ctx.lineTo(x, basket.y + basket.height);
                ctx.stroke();
            }

            // Draw glowing AI Hand Pointer Cursor when hand tracking is active
            if (isHandDetected) {
                const cursorX = basket.x + basket.width / 2;
                const cursorY = basket.y - 12;

                ctx.fillStyle = '#22c55e';
                ctx.strokeStyle = '#ffffff';
                ctx.lineWidth = 3;

                ctx.beginPath();
                ctx.arc(cursorX, cursorY, 12, 0, Math.PI * 2);
                ctx.fill();
                ctx.stroke();

                ctx.fillStyle = '#ffffff';
                ctx.font = '700 12px sans-serif';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText('✋', cursorX, cursorY);
            }

            ctx.restore();
        }

        function drawChoices() {
            fallingChoices.forEach(choice => {
                ctx.save();

                // Drop Shadow
                ctx.fillStyle = 'rgba(0, 0, 0, 0.12)';
                ctx.beginPath();
                ctx.roundRect(choice.x + 3, choice.y + 4, choice.width, choice.height, [16]);
                ctx.fill();

                // Answer Box
                ctx.fillStyle = '#ffffff';
                ctx.strokeStyle = '#38bdf8';
                ctx.lineWidth = 3.5;

                ctx.beginPath();
                ctx.roundRect(choice.x, choice.y, choice.width, choice.height, [16]);
                ctx.fill();
                ctx.stroke();

                // Gloss Highlight
                ctx.fillStyle = '#e0f2fe';
                ctx.beginPath();
                ctx.roundRect(choice.x + 4, choice.y + 4, choice.width - 8, 12, [10, 10, 4, 4]);
                ctx.fill();

                // Choice Text
                ctx.fillStyle = '#0f172a';
                ctx.font = '800 16px "Fredoka", sans-serif';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';
                ctx.fillText(choice.text, choice.x + choice.width / 2, choice.y + choice.height / 2 + 3);

                ctx.restore();
            });
        }

        function updateParticles() {
            particles.forEach((p, idx) => {
                p.x += p.vx;
                p.y += p.vy;
                p.alpha -= p.decay;

                if (p.alpha <= 0) {
                    particles.splice(idx, 1);
                } else {
                    ctx.save();
                    ctx.globalAlpha = p.alpha;
                    ctx.fillStyle = p.color;
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.restore();
                }
            });
        }

        function updateGame() {
            if (!isGameRunning) return;

            // Keyboard input fallback
            if (keys.left) basket.x -= basket.speed;
            if (keys.right) basket.x += basket.speed;
            clampBasket();

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            let resetRound = false;

            fallingChoices.forEach(choice => {
                choice.y += choice.speed;

                const bBox = { x: basket.x, y: basket.y, w: basket.width, h: basket.height };
                const cBox = { x: choice.x, y: choice.y, w: choice.width, h: choice.height };

                // Collision Detection between Basket/Hand and Choice
                if (
                    cBox.x < bBox.x + bBox.w &&
                    cBox.x + cBox.w > bBox.x &&
                    cBox.y < bBox.y + bBox.h &&
                    cBox.y + cBox.h > bBox.y
                ) {
                    const catchX = choice.x + choice.width / 2;
                    const catchY = choice.y;

                    if (choice.isCorrect) {
                        catchesCount++;
                        comboStreak++;
                        if (comboStreak > maxCombo) maxCombo = comboStreak;

                        let gainedPoints = 10;
                        let isComboTrigger = (comboStreak > 0 && comboStreak % 3 === 0);

                        if (isComboTrigger) {
                            gainedPoints += 5; // +5 Extra Combo Bonus (+15 Total)
                            audio.playComboFanfare();
                            showFloatingText(catchX, catchY, `🔥 ${comboStreak}x COMBO! +15 PTS!`, true, true);
                            
                            const gameContainer = document.getElementById('gameContainer');
                            gameContainer.classList.add('shake-screen');
                            setTimeout(() => gameContainer.classList.remove('shake-screen'), 400);
                        } else {
                            audio.playCorrect();
                            showFloatingText(catchX, catchY, `+10 ⭐`, true);
                        }

                        score += gainedPoints;
                        triggerParticles(catchX, catchY, true);

                        const comboContainer = document.getElementById('comboContainer');
                        const comboDisplay = document.getElementById('comboDisplay');
                        comboDisplay.innerText = `${comboStreak}x`;
                        comboContainer.classList.add('scale-110');
                        setTimeout(() => comboContainer.classList.remove('scale-110'), 200);

                    } else {
                        // Incorrect choice (-5 pts deduction)
                        score = Math.max(0, score - 5);
                        comboStreak = 0;
                        audio.playWrong();
                        triggerParticles(catchX, catchY, false);
                        showFloatingText(catchX, catchY, `-5 ❌`, false);

                        const gameContainer = document.getElementById('gameContainer');
                        const redFlash = document.getElementById('redFlash');
                        gameContainer.classList.add('shake-screen');
                        redFlash.classList.add('red-flash-active');

                        setTimeout(() => {
                            gameContainer.classList.remove('shake-screen');
                            redFlash.classList.remove('red-flash-active');
                        }, 350);

                        document.getElementById('comboDisplay').innerText = `0x`;
                    }

                    document.getElementById('scoreDisplay').innerText = score;
                    updateRankBadge();
                    resetRound = true;
                }
            });

            const allPassed = fallingChoices.every(c => c.y > canvas.height + 20);
            if (allPassed || resetRound) {
                spawnQuestionRound();
            }

            drawChoices();
            drawBasket();
            updateParticles();

            animationFrameId = requestAnimationFrame(updateGame);
        }

        function loadHighScore() {
            const saved = localStorage.getItem('catch_food_highscore') || '0';
            document.getElementById('startHighScore').innerText = saved;
            return parseInt(saved, 10);
        }

        function saveHighScore(newScore) {
            const currentHigh = loadHighScore();
            if (newScore > currentHigh) {
                localStorage.setItem('catch_food_highscore', newScore.toString());
                document.getElementById('startHighScore').innerText = newScore;
            }
        }

        function startGame(enableCamera = false) {
            audio.playClick();
            score = 0;
            timeLeft = 60;
            comboStreak = 0;
            maxCombo = 0;
            catchesCount = 0;
            currentRankLevel = 0;
            isGameRunning = true;

            document.getElementById('scoreDisplay').innerText = '0';
            document.getElementById('comboDisplay').innerText = '0x';
            document.getElementById('timerDisplay').innerText = '60s';
            updateRankBadge();

            document.getElementById('startModal').classList.add('hidden');
            document.getElementById('gameOverModal').classList.add('hidden');

            resizeCanvas();
            spawnQuestionRound();

            if (enableCamera && !isCameraActive) {
                startCameraControl();
            }

            // 60-Second Countdown Timer
            clearInterval(gameTimer);
            gameTimer = setInterval(() => {
                timeLeft--;
                document.getElementById('timerDisplay').innerText = `${timeLeft}s`;

                if (timeLeft <= 5 && timeLeft > 0) {
                    audio.playTick();
                    document.getElementById('timerCard').classList.add('border-rose-500', 'bg-rose-100', 'scale-105');
                } else {
                    document.getElementById('timerCard').classList.remove('border-rose-500', 'bg-rose-100', 'scale-105');
                }

                if (timeLeft <= 0) {
                    endGame();
                }
            }, 1000);

            cancelAnimationFrame(animationFrameId);
            updateGame();
        }

        function endGame() {
            isGameRunning = false;
            clearInterval(gameTimer);
            cancelAnimationFrame(animationFrameId);

            saveHighScore(score);
            const highScore = loadHighScore();

            audio.playVictory();

            document.getElementById('finalScore').innerText = score;
            document.getElementById('finalCatches').innerText = catchesCount;
            document.getElementById('finalMaxCombo').innerText = `${maxCombo}x`;
            document.getElementById('endHighScore').innerText = highScore;

            let stars = '⭐';
            let heading = 'Good Effort! 👍';
            let praise = 'Keep practicing your Super Minds food words!';
            let rankTag = '🐣 Beginner';
            let rankClass = 'bg-amber-100 text-amber-800 border-amber-300';

            if (score >= 100) {
                stars = '⭐⭐⭐';
                heading = 'AI Master! 🏆';
                praise = 'Outstanding! You are a Super Minds Food Master!';
                rankTag = '🏆 AI Master';
                rankClass = 'bg-purple-100 text-purple-800 border-purple-300';
            } else if (score >= 50) {
                stars = '⭐⭐';
                heading = 'Great Job! 🌟';
                praise = 'Super catches! You are getting really fast!';
                rankTag = '🌟 Great';
                rankClass = 'bg-emerald-100 text-emerald-800 border-emerald-300';
            }

            document.getElementById('starContainer').innerText = stars;
            document.getElementById('gameOverHeading').innerText = heading;
            document.getElementById('praiseMessage').innerText = praise;

            const endBadge = document.getElementById('endRankBadge');
            endBadge.className = `inline-flex items-center px-3 py-1 rounded-full text-xs sm:text-sm font-black border-2 mb-2 ${rankClass}`;
            endBadge.innerText = rankTag;

            document.getElementById('gameOverModal').classList.remove('hidden');
        }

        document.getElementById('btnStartNormal').addEventListener('click', () => startGame(false));
        document.getElementById('btnStartWithCam').addEventListener('click', () => startGame(true));
        document.getElementById('btnPlayAgain').addEventListener('click', () => startGame(isCameraActive));

        window.onload = () => {
            loadHighScore();
            resizeCanvas();
        };
    </script>
</body>
</html>
