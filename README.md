<!DOCTYPE html>
<html lang="en" class="dark h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>BREL_OS v7.0 | CYBER_DEFENSE_INTERFACE</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <!-- Google Fonts Fira Code & Share Tech Mono -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fira+Code:wght@300;400;500;600;700&family=Share+Tech+Mono&display=swap" rel="stylesheet">

    <!-- Tailwind Config -->
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        cyber: {
                            bg: '#020617',
                            panel: '#080d1a',
                            border: '#1e293b',
                            highlight: '#0f172a',
                            green: '#00ff66',
                            emerald: '#10b981',
                            cyan: '#06b6d4',
                            amber: '#f59e0b',
                            red: '#ef4444',
                            dim: '#475569'
                        }
                    },
                    fontFamily: {
                        mono: ['"Fira Code"', '"Share Tech Mono"', 'monospace'],
                    },
                    boxShadow: {
                        'cyber-glow': '0 0 15px rgba(0, 255, 102, 0.2)',
                        'cyan-glow': '0 0 15px rgba(6, 182, 212, 0.25)',
                        'red-glow': '0 0 15px rgba(239, 68, 68, 0.3)',
                    }
                }
            }
        }
    </script>

    <style>
        body {
            background-color: #020617;
            color: #10b981;
            font-family: 'Fira Code', monospace;
            overflow-x: hidden;
            user-select: none;
        }

        /* CRT Scanline Effect */
        .crt::before {
            content: " ";
            display: block;
            position: absolute;
            top: 0; left: 0; bottom: 0; right: 0;
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.03), rgba(0, 255, 0, 0.01), rgba(0, 0, 255, 0.03));
            z-index: 40;
            background-size: 100% 3px, 6px 100%;
            pointer-events: none;
            opacity: 0.7;
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #080d1a;
        }
        ::-webkit-scrollbar-thumb {
            background: #1e293b;
            border-radius: 3px;
            border: 1px solid #10b981;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #10b981;
        }

        /* Pulse Animations */
        @keyframes radar-sweep {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        .radar-sweep {
            animation: radar-sweep 4s linear infinite;
        }

        .glitch-text {
            text-shadow: 0 0 5px rgba(0, 255, 102, 0.7), 0 0 10px rgba(6, 182, 212, 0.5);
        }

        .clip-corner {
            clip-path: polygon(0 0, calc(100% - 12px) 0, 100% 12px, 100% 100%, 12px 100%, 0 calc(100% - 12px));
        }

        .clip-top-right {
            clip-path: polygon(0 0, calc(100% - 16px) 0, 100% 16px, 100% 100%, 0 100%);
        }
    </style>
</head>
<body class="h-screen w-screen flex flex-col crt relative overflow-hidden bg-cyber-bg text-cyber-emerald">

    <!-- Background Canvas for Matrix Rain -->
    <canvas id="matrixCanvas" class="fixed inset-0 pointer-events-none opacity-20 z-0"></canvas>

    <!-- TOP TELEMETRY & OS HEADER BAR -->
    <header class="relative z-30 bg-cyber-panel/90 border-b border-cyber-border px-4 py-2 flex flex-wrap items-center justify-between text-xs backdrop-blur-md">
        <!-- Brand / Identity -->
        <div class="flex items-center space-x-3">
            <div class="relative flex items-center justify-center">
                <span class="w-3 h-3 bg-cyber-green rounded-full animate-ping absolute opacity-75"></span>
                <span class="w-2.5 h-2.5 bg-cyber-green rounded-full relative"></span>
            </div>
            <div class="font-bold text-sm tracking-wider text-white flex items-center gap-2">
                <i class="fa-solid fa-shield-halved text-cyber-green"></i>
                <span class="text-cyber-green">BREL_OS</span>
                <span class="text-gray-500">v7.0_SECURE</span>
            </div>
            <span class="hidden md:inline-block px-2 py-0.5 text-[10px] bg-cyber-green/10 text-cyber-green border border-cyber-green/30 rounded">
                GABON_NODE_ONLINE
            </span>
        </div>

        <!-- Telemetry Metrics & Real-time Info -->
        <div class="flex items-center space-x-4 md:space-x-6 my-1 md:my-0 text-slate-300">
            <div class="flex items-center space-x-1.5">
                <i class="fa-solid fa-gauge-high text-cyber-cyan"></i>
                <span class="text-gray-400">FPS:</span>
                <span id="fpsCounter" class="text-cyber-cyan font-semibold">60</span>
            </div>
            <div class="hidden sm:flex items-center space-x-2">
                <i class="fa-solid fa-microchip text-cyber-amber"></i>
                <span class="text-gray-400">CPU:</span>
                <div class="w-16 bg-slate-800 h-2 rounded overflow-hidden border border-slate-700">
                    <div id="cpuMeter" class="bg-cyber-amber h-full w-1/3 transition-all duration-500"></div>
                </div>
                <span id="cpuText" class="text-[10px] text-cyber-amber">34%</span>
            </div>
            <div class="hidden lg:flex items-center space-x-2">
                <i class="fa-solid fa-memory text-cyber-green"></i>
                <span class="text-gray-400">RAM:</span>
                <div class="w-16 bg-slate-800 h-2 rounded overflow-hidden border border-slate-700">
                    <div id="ramMeter" class="bg-cyber-green h-full w-2/5 transition-all duration-500"></div>
                </div>
                <span id="ramText" class="text-[10px] text-cyber-green">4.2GB</span>
            </div>
            <div class="flex items-center space-x-1.5 text-cyber-cyan font-mono">
                <i class="fa-regular fa-clock"></i>
                <span id="utcClock">00:00:00 UTC</span>
            </div>
        </div>

        <!-- OS Controls & SFX Toggles -->
        <div class="flex items-center space-x-2">
            <button id="sfxToggle" onclick="toggleAudio()" class="px-2.5 py-1 text-[11px] bg-slate-800 hover:bg-slate-700 border border-slate-600 rounded text-gray-300 transition flex items-center gap-1.5" title="Toggle Synthesized Sound FX">
                <i id="sfxIcon" class="fa-solid fa-volume-xmark text-cyber-red"></i>
                <span class="hidden sm:inline">SFX</span>
            </button>
            <button onclick="toggleCRT()" class="px-2.5 py-1 text-[11px] bg-slate-800 hover:bg-slate-700 border border-slate-600 rounded text-gray-300 transition flex items-center gap-1.5" title="Toggle CRT Scanlines">
                <i class="fa-solid fa-tv text-cyber-cyan"></i>
                <span class="hidden sm:inline">CRT</span>
            </button>
            <button onclick="openTab('terminal')" class="px-2.5 py-1 text-[11px] bg-cyber-green/20 hover:bg-cyber-green/30 border border-cyber-green/50 text-cyber-green rounded transition flex items-center gap-1.5">
                <i class="fa-solid fa-terminal"></i>
                <span class="hidden sm:inline">CLI</span>
            </button>
        </div>
    </header>

    <!-- MAIN INTERFACE DASHBOARD WORKSPACE -->
    <main class="relative z-10 flex-1 grid grid-cols-1 lg:grid-cols-12 gap-3 p-3 overflow-hidden">

        <!-- LEFT PANEL: PROFILE & THREAT ENGINE (3 Cols) -->
        <section class="lg:col-span-3 flex flex-col gap-3 h-full overflow-hidden">
            <!-- Operator Bio Matrix Card -->
            <div class="bg-cyber-panel/80 border border-cyber-border clip-corner p-3.5 flex flex-col gap-3 relative shadow-lg backdrop-blur-sm">
                <div class="flex items-center justify-between border-b border-cyber-border/60 pb-2">
                    <div class="flex items-center space-x-2">
                        <i class="fa-solid fa-id-card text-cyber-cyan"></i>
                        <span class="text-xs font-bold tracking-wider text-gray-200">OPERATOR_ID</span>
                    </div>
                    <span class="text-[10px] text-cyber-green font-mono">CONFIDENTIAL</span>
                </div>

                <div class="flex items-center space-x-3">
                    <div class="relative w-14 h-14 bg-slate-900 border border-cyber-cyan rounded flex items-center justify-center overflow-hidden">
                        <i class="fa-solid fa-user-ninja text-3xl text-cyber-cyan/80"></i>
                        <div class="absolute inset-0 bg-gradient-to-t from-cyber-cyan/20 to-transparent"></div>
                    </div>
                    <div>
                        <h2 class="font-bold text-sm text-white tracking-wide">Brel MASSIMA</h2>
                        <p class="text-[11px] text-cyber-cyan">Libreville, Gabon 🇬🇦</p>
                        <p class="text-[10px] text-gray-400 mt-0.5">Cybersecurity & Dev Engineer</p>
                    </div>
                </div>

                <div class="text-[11px] text-slate-300 space-y-1 bg-slate-950/60 p-2.5 rounded border border-slate-800">
                    <div class="flex justify-between">
                        <span class="text-gray-500">ROLE:</span>
                        <span class="text-cyber-green font-mono">Full-Stack Architect</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-gray-500">FOCUS:</span>
                        <span class="text-cyber-cyan font-mono">GovTech & SOC Ops</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-gray-500">SEEKING:</span>
                        <span class="text-cyber-amber font-mono font-bold animate-pulse">PFE Internship '26</span>
                    </div>
                </div>

                <!-- Action Button Matrix -->
                <div class="grid grid-cols-2 gap-2 text-[11px]">
                    <button onclick="executeCommand('whoami')" class="p-1.5 bg-slate-800/80 hover:bg-cyber-cyan/20 border border-slate-700 hover:border-cyber-cyan text-slate-200 rounded text-center transition">
                        <i class="fa-solid fa-terminal text-cyber-cyan mr-1"></i> Bio Details
                    </button>
                    <button onclick="executeCommand('contact')" class="p-1.5 bg-slate-800/80 hover:bg-cyber-green/20 border border-slate-700 hover:border-cyber-green text-slate-200 rounded text-center transition">
                        <i class="fa-solid fa-paper-plane text-cyber-green mr-1"></i> Contact
                    </button>
                </div>
            </div>

            <!-- Real-time Intrusion Detection Log Stream -->
            <div class="bg-cyber-panel/80 border border-cyber-border clip-corner p-3 flex-1 flex flex-col min-h-[180px] overflow-hidden backdrop-blur-sm">
                <div class="flex items-center justify-between border-b border-cyber-border/60 pb-1.5 mb-2">
                    <div class="flex items-center space-x-2 text-xs font-bold text-gray-200">
                        <i class="fa-solid fa-shield-cat text-cyber-red"></i>
                        <span>SOC_THREAT_STREAM</span>
                    </div>
                    <span class="flex h-2 w-2 relative">
                        <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-cyber-red opacity-75"></span>
                        <span class="relative inline-flex rounded-full h-2 w-2 bg-cyber-red"></span>
                    </span>
                </div>
                <div id="socLogStream" class="flex-1 font-mono text-[10px] space-y-1.5 overflow-y-auto pr-1 text-slate-400">
                    <!-- Logs injected dynamically -->
                </div>
            </div>
        </section>

        <!-- CENTER PANEL: TABBED WORKSPACE (CLI / LABS / GAMES) (6 Cols) -->
        <section class="lg:col-span-6 flex flex-col h-full bg-cyber-panel/80 border border-cyber-border clip-corner overflow-hidden shadow-2xl backdrop-blur-md">
            
            <!-- Navigation Workspace Tabs -->
            <div class="flex items-center bg-slate-950 border-b border-cyber-border text-xs overflow-x-auto">
                <button id="tabBtn-terminal" onclick="switchMainTab('terminal')" class="tab-btn px-4 py-2.5 flex items-center space-x-2 border-r border-cyber-border bg-cyber-panel text-cyber-green font-bold border-b-2 border-b-cyber-green">
                    <i class="fa-solid fa-terminal"></i>
                    <span>TERMINAL_CLI</span>
                </button>
                <button id="tabBtn-labs" onclick="switchMainTab('labs')" class="tab-btn px-4 py-2.5 flex items-center space-x-2 border-r border-cyber-border text-gray-400 hover:text-white hover:bg-slate-900 transition">
                    <i class="fa-solid fa-flask"></i>
                    <span>SOC_GOVTECH_LABS</span>
                </button>
                <button id="tabBtn-cyberdrive" onclick="switchMainTab('cyberdrive')" class="tab-btn px-4 py-2.5 flex items-center space-x-2 border-r border-cyber-border text-gray-400 hover:text-white hover:bg-slate-900 transition">
                    <i class="fa-solid fa-car-side text-cyber-amber"></i>
                    <span>CYBER_DRIVE</span>
                </button>
                <button id="tabBtn-hackgame" onclick="switchMainTab('hackgame')" class="tab-btn px-4 py-2.5 flex items-center space-x-2 text-gray-400 hover:text-white hover:bg-slate-900 transition">
                    <i class="fa-solid fa-user-secret text-cyber-cyan"></i>
                    <span>INCIDENT_DEFENSE</span>
                </button>
            </div>

            <!-- TAB 1: INTERACTIVE CLI TERMINAL MODULE -->
            <div id="tabContent-terminal" class="main-tab-content flex-1 flex flex-col p-3 overflow-hidden bg-slate-950/90 font-mono text-xs">
                <!-- Terminal Output Buffer -->
                <div id="terminalOutput" class="flex-1 overflow-y-auto space-y-2 pr-2 text-slate-300">
                    <div class="text-cyber-green">
                        <pre class="text-[9px] sm:text-[10px] leading-none text-cyber-green font-bold">
.______   .______  _______.  ______       _______.                   ______  
|   _  \  |   _  \|   ____| /  __  \     /       |                  /  __  \ 
|  |_)  | |  |_)  |  |__   |  |  |  |   |   (----`  ____   ____    |  |  |  |
|   _  <  |      /|   __|  |  |  |  |    \   \     /  _ \ /  _ \   |  |  |  |
|  |_)  | |  |\  \|  |____ |  `--'  |.----)   |   |  (_) |  (_) | _|  `--'  |
|______/  |__| \__|_______| \______/ |_______/     \___/ \___/ (_)\______/ 
                        </pre>
                        <p class="mt-2 text-cyber-cyan">SYSTEM BREL_OS [Version 7.0.2026] - ALL SUBSYSTEMS OPERATIONAL</p>
                        <p class="text-gray-400">Type <span class="text-cyber-green font-bold cursor-pointer" onclick="executeCommand('help')">'help'</span> for list of available cyber commands or click quick chips below.</p>
                    </div>
                </div>

                <!-- CLI Quick Trigger Chips -->
                <div class="flex flex-wrap gap-1.5 my-2 pt-2 border-t border-slate-800 text-[10px]">
                    <span class="text-gray-500 py-0.5">QUICK_RUN:</span>
                    <button onclick="executeCommand('whoami')" class="px-2 py-0.5 bg-slate-900 hover:bg-cyber-green/20 border border-slate-700 hover:border-cyber-green text-gray-300 rounded">whoami</button>
                    <button onclick="executeCommand('skills')" class="px-2 py-0.5 bg-slate-900 hover:bg-cyber-cyan/20 border border-slate-700 hover:border-cyber-cyan text-gray-300 rounded">skills</button>
                    <button onclick="executeCommand('projects')" class="px-2 py-0.5 bg-slate-900 hover:bg-cyber-amber/20 border border-slate-700 hover:border-cyber-amber text-gray-300 rounded">projects</button>
                    <button onclick="executeCommand('soc-lab')" class="px-2 py-0.5 bg-slate-900 hover:bg-cyber-red/20 border border-slate-700 hover:border-cyber-red text-gray-300 rounded">soc-lab</button>
                    <button onclick="executeCommand('play-cyber-drive')" class="px-2 py-0.5 bg-cyber-amber/20 border border-cyber-amber text-cyber-amber rounded font-bold">play-cyber-drive</button>
                    <button onclick="executeCommand('clear')" class="px-2 py-0.5 bg-slate-900 hover:bg-slate-700 text-gray-400 rounded">clear</button>
                </div>

                <!-- CLI Command Prompt Line -->
                <div class="flex items-center space-x-2 bg-slate-900/90 border border-slate-800 px-3 py-2 rounded shadow-inner">
                    <span class="text-cyber-green font-bold">brel@cyber-node-gab:~$</span>
                    <input id="cliInput" type="text" class="flex-1 bg-transparent text-white focus:outline-none font-mono text-xs caret-cyber-green" placeholder="type a command..." autocomplete="off">
                    <i class="fa-solid fa-angle-right text-cyber-green animate-pulse"></i>
                </div>
            </div>

            <!-- TAB 2: SOC & GOVTECH FEATURED LABS MODULE -->
            <div id="tabContent-labs" class="main-tab-content hidden flex-1 p-4 overflow-y-auto bg-slate-950/90 space-y-4">
                <div class="border-b border-cyber-border pb-2 flex items-center justify-between">
                    <h3 class="text-sm font-bold text-white flex items-center gap-2">
                        <i class="fa-solid fa-cubes text-cyber-cyan"></i>
                        <span>FEATURED_GOVTECH_&_SOC_INFRASTRUCTURE</span>
                    </h3>
                    <span class="text-[10px] bg-cyber-cyan/10 text-cyber-cyan px-2 py-0.5 rounded border border-cyber-cyan/30">ARCHITECTURES</span>
                </div>

                <!-- Project Card 1: MboloAlert -->
                <div class="bg-cyber-panel border border-cyber-border p-3.5 rounded clip-corner hover:border-cyber-cyan transition space-y-2">
                    <div class="flex justify-between items-start">
                        <div>
                            <h4 class="font-bold text-cyber-cyan text-sm flex items-center gap-2">
                                <i class="fa-solid fa-triangle-exclamation text-cyber-amber"></i>
                                MboloAlert - National GovTech Crisis Engine
                            </h4>
                            <p class="text-[11px] text-gray-400 mt-1">
                                Critical real-time public emergency response ecosystem engineered for Gabon & Central Africa. Connects citizens to first responders using automated multi-channel alert dispatching.
                            </p>
                        </div>
                        <span class="text-[10px] bg-cyber-amber/10 text-cyber-amber px-2 py-0.5 rounded border border-cyber-amber/30">CRITICAL_INFRA</span>
                    </div>
                    <!-- Architecture tags -->
                    <div class="flex flex-wrap gap-1.5 pt-1">
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">React Native</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">Node.js / Express</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">PostgreSQL</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">Docker</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">WebSockets</span>
                    </div>
                </div>

                <!-- Project Card 2: SOC Threat Analysis Sandbox -->
                <div class="bg-cyber-panel border border-cyber-border p-3.5 rounded clip-corner hover:border-cyber-green transition space-y-2">
                    <div class="flex justify-between items-start">
                        <div>
                            <h4 class="font-bold text-cyber-green text-sm flex items-center gap-2">
                                <i class="fa-solid fa-bug text-cyber-green"></i>
                                Automated SOC Threat Lab & CAPEv2 Sandbox
                            </h4>
                            <p class="text-[11px] text-gray-400 mt-1">
                                Autonomous malware detonation environment integrated with TheHive incident response platform, Cortex analyzer engines, and dynamic YARA signature matching.
                            </p>
                        </div>
                        <span class="text-[10px] bg-cyber-green/10 text-cyber-green px-2 py-0.5 rounded border border-cyber-green/30">CYBER_DEFENSE</span>
                    </div>
                    <div class="flex flex-wrap gap-1.5 pt-1">
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">TheHive 5</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">Cortex</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">CAPEv2</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">YARA Rules</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">Python Security Automation</span>
                    </div>
                </div>

                <!-- Project Card 3: Secure Microservices Gateway -->
                <div class="bg-cyber-panel border border-cyber-border p-3.5 rounded clip-corner hover:border-cyber-cyan transition space-y-2">
                    <div class="flex justify-between items-start">
                        <div>
                            <h4 class="font-bold text-white text-sm flex items-center gap-2">
                                <i class="fa-solid fa-network-wired text-cyber-cyan"></i>
                                Zero-Trust API Security & Identity Gateway
                            </h4>
                            <p class="text-[11px] text-gray-400 mt-1">
                                High-performance microservice gateway enforcing mTLS, rate limiting, JWT token introspection, and encrypted telemetry pipelines.
                            </p>
                        </div>
                        <span class="text-[10px] bg-slate-800 text-gray-300 px-2 py-0.5 rounded border border-slate-700">FULL-STACK</span>
                    </div>
                    <div class="flex flex-wrap gap-1.5 pt-1">
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">NestJS</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">Spring Boot</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">Redis</span>
                        <span class="px-2 py-0.5 text-[10px] bg-slate-900 border border-slate-700 text-slate-300 rounded">Kubernetes</span>
                    </div>
                </div>
            </div>

            <!-- TAB 3: CYBER DRIVE HIGHWAY GAME -->
            <div id="tabContent-cyberdrive" class="main-tab-content hidden flex-1 flex flex-col p-3 bg-slate-950 relative overflow-hidden">
                <div class="flex justify-between items-center mb-2 font-mono text-xs">
                    <div class="flex items-center space-x-3">
                        <span class="text-cyber-amber font-bold flex items-center gap-1">
                            <i class="fa-solid fa-trophy"></i> SCORE: <span id="driveScore">0</span>
                        </span>
                        <span class="text-cyber-cyan flex items-center gap-1">
                            <i class="fa-solid fa-gauge-high"></i> SPEED: <span id="driveSpeed">120</span> KM/H
                        </span>
                    </div>
                    <button id="driveStartBtn" onclick="startCyberDrive()" class="px-3 py-1 bg-cyber-amber hover:bg-amber-500 text-slate-950 font-bold rounded text-xs transition">
                        START DRIVE
                    </button>
                </div>

                <!-- Game Canvas Container -->
                <div class="relative flex-1 bg-slate-900 rounded border border-slate-800 overflow-hidden flex items-center justify-center">
                    <canvas id="driveCanvas" class="w-full h-full block"></canvas>

                    <!-- Touch Controls Overlay for Mobile -->
                    <div class="absolute bottom-3 left-0 right-0 flex justify-center gap-6 md:hidden z-20">
                        <button onclick="moveDriveCar('left')" class="w-12 h-12 bg-slate-800/80 border border-cyber-amber text-cyber-amber rounded-full text-xl flex items-center justify-center active:bg-cyber-amber active:text-black">
                            <i class="fa-solid fa-arrow-left"></i>
                        </button>
                        <button onclick="moveDriveCar('right')" class="w-12 h-12 bg-slate-800/80 border border-cyber-amber text-cyber-amber rounded-full text-xl flex items-center justify-center active:bg-cyber-amber active:text-black">
                            <i class="fa-solid fa-arrow-right"></i>
                        </button>
                    </div>
                </div>
                <p class="text-[10px] text-gray-500 text-center mt-2 font-mono">Use LEFT / RIGHT Arrow keys or A/D to dodge firewall obstacles and collect security patch nodes!</p>
            </div>

            <!-- TAB 4: HACKER IN ACTION / INCIDENT MITIGATION GAME -->
            <div id="tabContent-hackgame" class="main-tab-content hidden flex-1 flex flex-col p-3 bg-slate-950 relative overflow-hidden">
                <div class="flex justify-between items-center mb-2 font-mono text-xs">
                    <div class="flex items-center space-x-4">
                        <span class="text-cyber-red font-bold">INTEGRITY: <span id="firewallIntegrity">100</span>%</span>
                        <span class="text-cyber-green font-bold">MITIGATED: <span id="mitigatedCount">0</span></span>
                    </div>
                    <button id="hackStartBtn" onclick="startHackGame()" class="px-3 py-1 bg-cyber-green hover:bg-emerald-400 text-slate-950 font-bold rounded text-xs transition">
                        DEFEND SYSTEM
                    </button>
                </div>

                <div class="relative flex-1 bg-slate-900 rounded border border-slate-800 p-2 overflow-hidden flex flex-col">
                    <!-- Threat Matrix Screen -->
                    <div id="threatContainer" class="flex-1 grid grid-cols-2 sm:grid-cols-3 gap-2 overflow-y-auto p-2">
                        <!-- Threat Nodes spawning dynamically -->
                        <div class="text-center text-gray-500 text-xs my-auto col-span-full">
                            Click "DEFEND SYSTEM" to intercept real-time hostile IP attacks and apply security patches!
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- RIGHT PANEL: SKILLS MATRIX & SYSTEM STATS (3 Cols) -->
        <section class="lg:col-span-3 flex flex-col gap-3 h-full overflow-hidden">
            <!-- Tech Matrix Module -->
            <div class="bg-cyber-panel/80 border border-cyber-border clip-corner p-3.5 flex-1 flex flex-col overflow-hidden backdrop-blur-sm">
                <div class="flex items-center justify-between border-b border-cyber-border/60 pb-2 mb-2">
                    <div class="flex items-center space-x-2 text-xs font-bold text-gray-200">
                        <i class="fa-solid fa-layer-group text-cyber-green"></i>
                        <span>TECH_CAPABILITIES</span>
                    </div>
                    <span class="text-[10px] text-cyber-cyan font-mono">100% VERIFIED</span>
                </div>

                <!-- Categorized Skill Bars -->
                <div class="flex-1 overflow-y-auto space-y-3 pr-1 text-xs">
                    <!-- Category: Cyber & SOC -->
                    <div>
                        <span class="text-[10px] font-bold text-cyber-cyan tracking-wider">CYBERSECURITY & SOC</span>
                        <div class="space-y-1.5 mt-1">
                            <div>
                                <div class="flex justify-between text-[10px] mb-0.5">
                                    <span class="text-slate-300">TheHive / Cortex / CAPEv2</span>
                                    <span class="text-cyber-green">95%</span>
                                </div>
                                <div class="w-full bg-slate-900 h-1.5 rounded overflow-hidden">
                                    <div class="bg-cyber-green h-full w-[95%]"></div>
                                </div>
                            </div>
                            <div>
                                <div class="flex justify-between text-[10px] mb-0.5">
                                    <span class="text-slate-300">YARA Rules & Malware Forensics</span>
                                    <span class="text-cyber-green">90%</span>
                                </div>
                                <div class="w-full bg-slate-900 h-1.5 rounded overflow-hidden">
                                    <div class="bg-cyber-green h-full w-[90%]"></div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Category: Full-Stack Languages -->
                    <div>
                        <span class="text-[10px] font-bold text-cyber-green tracking-wider">CORE LANGUAGES</span>
                        <div class="space-y-1.5 mt-1">
                            <div>
                                <div class="flex justify-between text-[10px] mb-0.5">
                                    <span class="text-slate-300">TypeScript / JavaScript</span>
                                    <span class="text-cyber-cyan">95%</span>
                                </div>
                                <div class="w-full bg-slate-900 h-1.5 rounded overflow-hidden">
                                    <div class="bg-cyber-cyan h-full w-[95%]"></div>
                                </div>
                            </div>
                            <div>
                                <div class="flex justify-between text-[10px] mb-0.5">
                                    <span class="text-slate-300">Python (Security/Data)</span>
                                    <span class="text-cyber-cyan">92%</span>
                                </div>
                                <div class="w-full bg-slate-900 h-1.5 rounded overflow-hidden">
                                    <div class="bg-cyber-cyan h-full w-[92%]"></div>
                                </div>
                            </div>
                            <div>
                                <div class="flex justify-between text-[10px] mb-0.5">
                                    <span class="text-slate-300">Java / C++ / C#</span>
                                    <span class="text-cyber-cyan">88%</span>
                                </div>
                                <div class="w-full bg-slate-900 h-1.5 rounded overflow-hidden">
                                    <div class="bg-cyber-cyan h-full w-[88%]"></div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Category: Frameworks & Cloud -->
                    <div>
                        <span class="text-[10px] font-bold text-cyber-amber tracking-wider">FRAMEWORKS & DEVOPS</span>
                        <div class="space-y-1.5 mt-1">
                            <div>
                                <div class="flex justify-between text-[10px] mb-0.5">
                                    <span class="text-slate-300">React / Next.js / Angular</span>
                                    <span class="text-cyber-amber">94%</span>
                                </div>
                                <div class="w-full bg-slate-900 h-1.5 rounded overflow-hidden">
                                    <div class="bg-cyber-amber h-full w-[94%]"></div>
                                </div>
                            </div>
                            <div>
                                <div class="flex justify-between text-[10px] mb-0.5">
                                    <span class="text-slate-300">Node.js / NestJS / Django</span>
                                    <span class="text-cyber-amber">90%</span>
                                </div>
                                <div class="w-full bg-slate-900 h-1.5 rounded overflow-hidden">
                                    <div class="bg-cyber-amber h-full w-[90%]"></div>
                                </div>
                            </div>
                            <div>
                                <div class="flex justify-between text-[10px] mb-0.5">
                                    <span class="text-slate-300">Docker / Kubernetes / AWS</span>
                                    <span class="text-cyber-amber">85%</span>
                                </div>
                                <div class="w-full bg-slate-900 h-1.5 rounded overflow-hidden">
                                    <div class="bg-cyber-amber h-full w-[85%]"></div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Quick Radar / Terminal Telemetry Widget -->
            <div class="bg-cyber-panel/80 border border-cyber-border clip-corner p-3 flex items-center justify-between backdrop-blur-sm">
                <div class="space-y-1 text-xs">
                    <div class="text-gray-400">NODE STATUS</div>
                    <div class="text-cyber-green font-bold flex items-center gap-1.5">
                        <i class="fa-solid fa-lock text-cyber-green"></i> ZERO_TRUST_ACTIVE
                    </div>
                    <div class="text-[10px] text-gray-500">GABON_GOV_BACKBONE</div>
                </div>
                <!-- Radar Circle Visualizer -->
                <div class="relative w-12 h-12 rounded-full border border-cyber-green/40 bg-slate-950 flex items-center justify-center overflow-hidden">
                    <div class="absolute inset-0 border border-cyber-green/20 rounded-full scale-50"></div>
                    <div class="absolute w-full h-[1px] bg-cyber-green/30"></div>
                    <div class="absolute h-full w-[1px] bg-cyber-green/30"></div>
                    <div class="absolute w-full h-full bg-gradient-to-tr from-cyber-green/30 to-transparent radar-sweep origin-center rounded-full"></div>
                </div>
            </div>
        </section>
    </main>

    <script>
        // Web Audio API Synthesizer for Cyber SFX
        let audioCtx = null;
        let sfxEnabled = false;

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
        }

        function toggleAudio() {
            initAudio();
            sfxEnabled = !sfxEnabled;
            const icon = document.getElementById('sfxIcon');
            if (sfxEnabled) {
                icon.className = 'fa-solid fa-volume-high text-cyber-green';
                playBeep(880, 0.1, 'sine');
            } else {
                icon.className = 'fa-solid fa-volume-xmark text-cyber-red';
            }
        }

        function playBeep(freq = 600, duration = 0.05, type = 'sine') {
            if (!sfxEnabled || !audioCtx) return;
            try {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = type;
                osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
                gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + duration);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start();
                osc.stop(audioCtx.currentTime + duration);
            } catch(e){}
        }

        function toggleCRT() {
            document.body.classList.toggle('crt');
            playBeep(440, 0.08, 'triangle');
        }

        const matrixCanvas = document.getElementById('matrixCanvas');
        const mCtx = matrixCanvas.getContext('2d');

        function resizeMatrix() {
            matrixCanvas.width = window.innerWidth;
            matrixCanvas.height = window.innerHeight;
        }
        resizeMatrix();
        window.addEventListener('resize', resizeMatrix);

        const matrixChars = '01ABCDEFGHIJKLMNOPQRSTUVWXYZ$%#@&+=/*BREL7';
        const fontSize = 14;
        let columns = Math.floor(window.innerWidth / fontSize);
        let drops = Array(columns).fill(1);

        function drawMatrix() {
            mCtx.fillStyle = 'rgba(2, 6, 23, 0.1)';
            mCtx.fillRect(0, 0, matrixCanvas.width, matrixCanvas.height);
            mCtx.fillStyle = '#10b981';
            mCtx.font = fontSize + 'px monospace';

            for (let i = 0; i < drops.length; i++) {
                const text = matrixChars.charAt(Math.floor(Math.random() * matrixChars.length));
                mCtx.fillText(text, i * fontSize, drops[i] * fontSize);
                if (drops[i] * fontSize > matrixCanvas.height && Math.random() > 0.975) {
                    drops[i] = 0;
                }
                drops[i]++;
            }
        }

        function updateClock() {
            const now = new Date();
            document.getElementById('utcClock').innerText = now.toUTCString().split(' ')[4] + ' UTC';
        }
        setInterval(updateClock, 1000);
        updateClock();

        // CPU & RAM simulated fluctuation
        setInterval(() => {
            const cpu = Math.floor(25 + Math.random() * 30);
            const ram = (3.8 + Math.random() * 0.8).toFixed(1);
            document.getElementById('cpuMeter').style.width = cpu + '%';
            document.getElementById('cpuText').innerText = cpu + '%';
            document.getElementById('ramMeter').style.width = (ram / 8 * 100) + '%';
            document.getElementById('ramText').innerText = ram + 'GB';
        }, 3000);

        const socLogStream = document.getElementById('socLogStream');
        const threatLogs = [
            { level: 'INFO', text: 'CAPEv2 Sandbox: Analyzed SHA256 malware hash [CLEAN]' },
            { level: 'WARN', text: 'TheHive Alert: Anomalous SSH port sweep from 192.168.1.104' },
            { level: 'SECURE', text: 'mTLS Handshake verified: Node Libreville-01' },
            { level: 'ALERT', text: 'YARA Match: Rule [APT_GABON_PATTERN] triggered on sandbox detonation' },
            { level: 'INFO', text: 'MboloAlert Gateway: Health check passed across 12 region clusters' },
            { level: 'SECURE', text: 'Zero-Trust Policy Enforced: Token Introspection HTTP/2 OK' }
        ];

        function addSocLog() {
            const log = threatLogs[Math.floor(Math.random() * threatLogs.length)];
            const time = new Date().toTimeString().split(' ')[0];
            const div = document.createElement('div');
            
            let color = 'text-gray-400';
            if (log.level === 'WARN') color = 'text-cyber-amber';
            if (log.level === 'ALERT') color = 'text-cyber-red font-bold';
            if (log.level === 'SECURE') color = 'text-cyber-green';

            div.className = `${color} hover:bg-slate-900 p-0.5 rounded transition`;
            div.innerHTML = `<span class="text-slate-600">[${time}]</span> [${log.level}] ${log.text}`;
            socLogStream.appendChild(div);
            
            if (socLogStream.children.length > 25) {
                socLogStream.removeChild(socLogStream.firstChild);
            }
            socLogStream.scrollTop = socLogStream.scrollHeight;
        }
        setInterval(addSocLog, 2500);

        const cliInput = document.getElementById('cliInput');
        const terminalOutput = document.getElementById('terminalOutput');
        let commandHistory = [];
        let historyIndex = -1;

        const commands = {
            'help': `
<span class="text-cyber-cyan font-bold">AVAILABLE SYSTEM COMMANDS:</span>
  <span class="text-cyber-green">whoami</span>       - Operator identity & career background
  <span class="text-cyber-green">skills</span>       - Tech stack, languages & frameworks
  <span class="text-cyber-green">projects</span>     - GovTech & Cybersecurity engineering labs
  <span class="text-cyber-green">soc-lab</span>      - SOC threat hunting & sandbox tools
  <span class="text-cyber-green">play-cyber-drive</span> - Launch retro 2D Cyber Highway game
  <span class="text-cyber-green">play-hacker</span>  - Launch Incident Mitigation defense game
  <span class="text-cyber-green">contact</span>      - Get direct communication channels
  <span class="text-cyber-green">clear</span>        - Clear terminal buffer
`,
            'whoami': `
<span class="text-cyber-green font-bold">OPERATOR PROFILE: Brel MASSIMA</span>
Location: Libreville, Gabon 🇬🇦
Role: Full-Stack Developer & Cybersecurity Engineering Student
Focus: Building secure GovTech digital infrastructure for Gabon & Africa
Seeking: <span class="text-cyber-amber font-bold">PFE Internship in Cybersecurity (2026)</span>
Motto: "Building secure, scalable, and resilient digital architectures."
`,
            'skills': `
<span class="text-cyber-cyan font-bold">TECHNICAL MATRIX:</span>
• <span class="text-white">Languages:</span> TypeScript, JavaScript, Python, Java, C++, C#, PHP, SQL
• <span class="text-white">Frameworks:</span> React, Next.js, Angular, Vue.js, Node.js, NestJS, Django, Spring Boot
• <span class="text-white">Cybersecurity:</span> TheHive, Cortex, CAPEv2, YARA, Network Forensics, SOC Analysis
• <span class="text-white">Cloud & DevOps:</span> Docker, Kubernetes, AWS, PostgreSQL, MongoDB, Elasticsearch
`,
            'projects': `
<span class="text-cyber-amber font-bold">FEATURED PROJECTS:</span>
1. <span class="text-white font-bold">MboloAlert</span> - Emergency Response System for Gabon (React Native, Node.js, WebSockets)
2. <span class="text-white font-bold">SOC Threat Lab</span> - CAPEv2 Sandbox & YARA Incident Response Platform
3. <span class="text-white font-bold">Zero-Trust Identity Gateway</span> - High-performance microservice security mesh
`,
            'soc-lab': `
<span class="text-cyber-red font-bold">SOC ENVIRONMENT STATUS:</span>
• TheHive 5 Incident Platform: [ONLINE]
• Cortex Analyzers: [ACTIVE]
• CAPEv2 Sandbox Detonation: [READY]
• YARA Rule Engine: [UPDATED]
`,
            'contact': `
<span class="text-cyber-cyan font-bold">CONNECT WITH BREL MASSIMA:</span>
• Email: <a href="mailto:contact@brel7.dev" class="text-cyber-green underline">contact@brel7.dev</a>
• GitHub: <a href="https://github.com/Brel7" target="_blank" class="text-cyber-green underline">github.com/Brel7</a>
• Location: Libreville, Gabon
`
        };

        cliInput.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') {
                const inputVal = cliInput.value.trim();
                if (inputVal) {
                    processCommand(inputVal);
                    commandHistory.push(inputVal);
                    historyIndex = commandHistory.length;
                    cliInput.value = '';
                }
            } else if (e.key === 'ArrowUp') {
                if (historyIndex > 0) {
                    historyIndex--;
                    cliInput.value = commandHistory[historyIndex];
                }
            } else if (e.key === 'ArrowDown') {
                if (historyIndex < commandHistory.length - 1) {
                    historyIndex++;
                    cliInput.value = commandHistory[historyIndex];
                } else {
                    historyIndex = commandHistory.length;
                    cliInput.value = '';
                }
            }
        });

        function executeCommand(cmd) {
            switchMainTab('terminal');
            cliInput.value = cmd;
            processCommand(cmd);
            cliInput.value = '';
        }

        function processCommand(cmd) {
            playBeep(700, 0.04, 'square');
            
            // Render Command Line Input
            const cmdLine = document.createElement('div');
            cmdLine.innerHTML = `<span class="text-cyber-green font-bold">brel@cyber-node-gab:~$</span> <span class="text-white">${cmd}</span>`;
            terminalOutput.appendChild(cmdLine);

            const trimmed = cmd.toLowerCase().trim();

            if (trimmed === 'clear') {
                terminalOutput.innerHTML = '';
                return;
            } else if (trimmed === 'play-cyber-drive') {
                switchMainTab('cyberdrive');
                startCyberDrive();
                return;
            } else if (trimmed === 'play-hacker') {
                switchMainTab('hackgame');
                startHackGame();
                return;
            }

            const responseDiv = document.createElement('div');
            responseDiv.className = 'text-slate-300 ml-2 mb-2';

            if (commands[trimmed]) {
                responseDiv.innerHTML = commands[trimmed];
            } else {
                responseDiv.innerHTML = `<span class="text-cyber-red">Command not recognized: '${trimmed}'. Type <span class="text-cyber-green font-bold cursor-pointer" onclick="executeCommand('help')">'help'</span> for available tools.</span>`;
            }

            terminalOutput.appendChild(responseDiv);
            terminalOutput.scrollTop = terminalOutput.scrollHeight;
        }

        function switchMainTab(tabId) {
            playBeep(520, 0.05, 'sine');
            document.querySelectorAll('.tab-btn').forEach(btn => {
                btn.className = 'tab-btn px-4 py-2.5 flex items-center space-x-2 border-r border-cyber-border text-gray-400 hover:text-white hover:bg-slate-900 transition';
            });
            document.querySelectorAll('.main-tab-content').forEach(content => {
                content.classList.add('hidden');
            });

            const activeBtn = document.getElementById(`tabBtn-${tabId}`);
            if (activeBtn) {
                activeBtn.className = 'tab-btn px-4 py-2.5 flex items-center space-x-2 border-r border-cyber-border bg-cyber-panel text-cyber-green font-bold border-b-2 border-b-cyber-green';
            }
            const activeContent = document.getElementById(`tabContent-${tabId}`);
            if (activeContent) {
                activeContent.classList.remove('hidden');
                activeContent.classList.add('flex');
            }
        }

        let driveCanvas = document.getElementById('driveCanvas');
        let dCtx = driveCanvas.getContext('2d');
        let driveAnimationId = null;
        let driveActive = false;
        let driveScore = 0;
        let driveSpeed = 120;
        let playerCar = { x: 0, y: 0, width: 30, height: 50 };
        let obstacles = [];
        let patches = [];

        function resizeDriveCanvas() {
            if (driveCanvas.parentElement) {
                driveCanvas.width = driveCanvas.parentElement.clientWidth;
                driveCanvas.height = driveCanvas.parentElement.clientHeight;
                playerCar.x = driveCanvas.width / 2 - playerCar.width / 2;
                playerCar.y = driveCanvas.height - 70;
            }
        }

        function startCyberDrive() {
            resizeDriveCanvas();
            driveScore = 0;
            driveSpeed = 120;
            obstacles = [];
            patches = [];
            driveActive = true;
            document.getElementById('driveStartBtn').innerText = 'RESTART DRIVE';
            
            if (driveAnimationId) cancelAnimationFrame(driveAnimationId);
            gameLoopDrive();
        }

        function moveDriveCar(dir) {
            if (!driveActive) return;
            const step = 35;
            if (dir === 'left' && playerCar.x > 20) playerCar.x -= step;
            if (dir === 'right' && playerCar.x < driveCanvas.width - 50) playerCar.x += step;
            playBeep(300, 0.03, 'sawtooth');
        }

        window.addEventListener('keydown', (e) => {
            if (!driveActive) return;
            if (e.key === 'ArrowLeft' || e.key === 'a') moveDriveCar('left');
            if (e.key === 'ArrowRight' || e.key === 'd') moveDriveCar('right');
        });

        function gameLoopDrive() {
            if (!driveActive) return;
            driveScore++;
            driveSpeed = 120 + Math.floor(driveScore / 20);
            document.getElementById('driveScore').innerText = driveScore;
            document.getElementById('driveSpeed').innerText = driveSpeed;

            // Clear Screen
            dCtx.fillStyle = '#080d1a';
            dCtx.fillRect(0, 0, driveCanvas.width, driveCanvas.height);

            // Draw Road Grid Lines
            dCtx.strokeStyle = '#1e293b';
            dCtx.lineWidth = 2;
            const roadLeft = 20;
            const roadRight = driveCanvas.width - 20;
            
            dCtx.beginPath();
            dCtx.moveTo(roadLeft, 0); dCtx.lineTo(roadLeft, driveCanvas.height);
            dCtx.moveTo(roadRight, 0); dCtx.lineTo(roadRight, driveCanvas.height);
            dCtx.stroke();

            // Animated Center Dash Lines
            dCtx.setLineDash([20, 20]);
            dCtx.lineDashOffset = -driveScore * 8;
            dCtx.strokeStyle = '#06b6d4';
            dCtx.beginPath();
            dCtx.moveTo(driveCanvas.width / 2, 0);
            dCtx.lineTo(driveCanvas.width / 2, driveCanvas.height);
            dCtx.stroke();
            dCtx.setLineDash([]);

            // Spawn Obstacles (Malware Firewalls)
            if (Math.random() < 0.04) {
                obstacles.push({
                    x: roadLeft + Math.random() * (roadRight - roadLeft - 40),
                    y: -50,
                    width: 35,
                    height: 35
                });
            }

            // Spawn Patch Collectibles
            if (Math.random() < 0.02) {
                patches.push({
                    x: roadLeft + Math.random() * (roadRight - roadLeft - 30),
                    y: -40,
                    width: 20,
                    height: 20
                });
            }

            // Move and Render Player Cyber Car
            dCtx.fillStyle = '#00ff66';
            dCtx.shadowBlur = 10;
            dCtx.shadowColor = '#00ff66';
            dCtx.fillRect(playerCar.x, playerCar.y, playerCar.width, playerCar.height);
            dCtx.shadowBlur = 0;

            // Update & Draw Obstacles
            for (let i = obstacles.length - 1; i >= 0; i--) {
                const obs = obstacles[i];
                obs.y += 5 + (driveSpeed / 30);
                
                dCtx.fillStyle = '#ef4444';
                dCtx.fillRect(obs.x, obs.y, obs.width, obs.height);

                // Collision Detection
                if (playerCar.x < obs.x + obs.width &&
                    playerCar.x + playerCar.width > obs.x &&
                    playerCar.y < obs.y + obs.height &&
                    playerCar.y + playerCar.height > obs.y) {
                    
                    driveActive = false;
                    playBeep(150, 0.4, 'sawtooth');
                    
                    // Game Over Screen
                    dCtx.fillStyle = 'rgba(2, 6, 23, 0.85)';
                    dCtx.fillRect(0, 0, driveCanvas.width, driveCanvas.height);
                    dCtx.fillStyle = '#ef4444';
                    dCtx.font = 'bold 20px Fira Code';
                    dCtx.textAlign = 'center';
                    dCtx.fillText('FIREWALL COLLISION!', driveCanvas.width / 2, driveCanvas.height / 2 - 10);
                    dCtx.fillStyle = '#ffffff';
                    dCtx.font = '12px Fira Code';
                    dCtx.fillText(`FINAL DISTANCE: ${driveScore} KM`, driveCanvas.width / 2, driveCanvas.height / 2 + 15);
                    return;
                }

                if (obs.y > driveCanvas.height) obstacles.splice(i, 1);
            }

            // Update & Draw Patches
            for (let i = patches.length - 1; i >= 0; i--) {
                const patch = patches[i];
                patch.y += 4 + (driveSpeed / 40);

                dCtx.fillStyle = '#06b6d4';
                dCtx.beginPath();
                dCtx.arc(patch.x + 10, patch.y + 10, 10, 0, Math.PI * 2);
                dCtx.fill();

                if (playerCar.x < patch.x + patch.width &&
                    playerCar.x + playerCar.width > patch.x &&
                    playerCar.y < patch.y + patch.height &&
                    playerCar.y + playerCar.height > patch.y) {
                    
                    driveScore += 50;
                    playBeep(900, 0.1, 'sine');
                    patches.splice(i, 1);
                    continue;
                }

                if (patch.y > driveCanvas.height) patches.splice(i, 1);
            }

            driveAnimationId = requestAnimationFrame(gameLoopDrive);
        }

        let firewallIntegrity = 100;
        let mitigatedCount = 0;
        let hackGameActive = false;
        let hackInterval = null;

        function startHackGame() {
            firewallIntegrity = 100;
            mitigatedCount = 0;
            hackGameActive = true;
            document.getElementById('firewallIntegrity').innerText = 100;
            document.getElementById('mitigatedCount').innerText = 0;
            document.getElementById('hackStartBtn').innerText = 'RESTART DEFENSE';

            const container = document.getElementById('threatContainer');
            container.innerHTML = '';

            if (hackInterval) clearInterval(hackInterval);
            hackInterval = setInterval(spawnThreatNode, 1500);
        }

        function spawnThreatNode() {
            if (!hackGameActive) return;

            const container = document.getElementById('threatContainer');
            if (container.children.length > 8) {
                firewallIntegrity -= 10;
                document.getElementById('firewallIntegrity').innerText = firewallIntegrity;
                playBeep(200, 0.2, 'sawtooth');

                if (firewallIntegrity <= 0) {
                    hackGameActive = false;
                    clearInterval(hackInterval);
                    container.innerHTML = `
                        <div class="col-span-full text-center my-auto p-4 bg-cyber-red/20 border border-cyber-red rounded">
                            <h4 class="text-cyber-red font-bold text-base">SYSTEM BREACHED!</h4>
                            <p class="text-xs text-slate-300 mt-1">Total Intercepted Attacks: ${mitigatedCount}</p>
                        </div>
                    `;
                    return;
                }
            }

            const ips = ['192.168.1.45', '10.0.4.12', '172.16.0.89', '198.51.100.14'];
            const randomIp = ips[Math.floor(Math.random() * ips.length)];
            const card = document.createElement('div');
            card.className = 'bg-slate-900 border border-cyber-red/60 p-2 rounded flex flex-col justify-between text-[11px] animate-pulse';
            
            card.innerHTML = `
                <div class="text-cyber-red font-bold flex items-center justify-between">
                    <span>HOSTILE_ATTACK</span>
                    <i class="fa-solid fa-bug"></i>
                </div>
                <div class="text-gray-300 font-mono text-[10px] my-1">IP: ${randomIp}</div>
                <button onclick="mitigateThreat(this)" class="w-full py-1 bg-cyber-green/20 hover:bg-cyber-green text-cyber-green hover:text-black font-bold border border-cyber-green/50 rounded transition text-[10px]">
                    PATCH VULN
                </button>
            `;
            container.appendChild(card);
        }

        function mitigateThreat(btn) {
            if (!hackGameActive) return;
            playBeep(800, 0.08, 'sine');
            mitigatedCount++;
            document.getElementById('mitigatedCount').innerText = mitigatedCount;
            const card = btn.parentElement;
            card.remove();
        }

        let lastTime = performance.now();
        let frameCount = 0;

        function mainLoop(now) {
            frameCount++;
            if (now - lastTime >= 1000) {
                document.getElementById('fpsCounter').innerText = frameCount;
                frameCount = 0;
                lastTime = now;
            }
            drawMatrix();
            requestAnimationFrame(mainLoop);
        }

        window.onload = function() {
            /* Start global main animation loop */
            requestAnimationFrame(mainLoop);
        };
    </script>
</body>
</html>
