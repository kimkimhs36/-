<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=5.0">
<title>TEAMBKIT — RTBTS · Where Sound Becomes Speed</title>
<meta name="description" content="TEAMBKIT · A culture of sound and speed.">

<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/SplitText.min.js"></script>
<script src="https://cdn.jsdelivr.net/gh/studio-freight/lenis@1.0.42/bundled/lenis.min.js"></script>
<script src="https://unpkg.com/three@0.128.0/build/three.min.js"></script>
<script src="https://unpkg.com/three@0.128.0/examples/js/loaders/GLTFLoader.js"></script>
<script src="https://unpkg.com/three@0.128.0/examples/js/loaders/DRACOLoader.js"></script>
<script src="https://unpkg.com/three@0.128.0/examples/js/postprocessing/EffectComposer.js"></script>
<script src="https://unpkg.com/three@0.128.0/examples/js/postprocessing/RenderPass.js"></script>
<script src="https://unpkg.com/three@0.128.0/examples/js/postprocessing/ShaderPass.js"></script>
<script src="https://unpkg.com/three@0.128.0/examples/js/postprocessing/UnrealBloomPass.js"></script>
<script src="https://unpkg.com/three@0.128.0/examples/js/shaders/CopyShader.js"></script>
<script src="https://unpkg.com/three@0.128.0/examples/js/shaders/LuminosityHighPassShader.js"></script>

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fontsource/instrument-serif@5.0.0/index.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fontsource/inter@5.0.0/900.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fontsource/inter@5.0.0/400.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fontsource/inter@5.0.0/300.css">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fontsource/jetbrains-mono@5.0.8/index.css">

<style>
  :root {
    --deep: #0f0720;
    --deep-2: #1a0f2e;
    --purple: #4a2b5c;
    --violet: #7c3aed;
    --peach: #FFB5A7;
    --peach-glow: #FF9E8A;
    --cream: #F5E6D3;
    --lavender: #E0AAFF;
    --accent: #ff6b9d;
  }
  
  * { margin: 0; padding: 0; box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  html { background: var(--deep); }
  body {
    background: var(--deep); color: var(--cream);
    font-family: 'Inter', sans-serif; font-weight: 400;
    overflow-x: hidden;
  }
  @media (hover: hover) { body { cursor: none; } }
  
  .sans { font-family: 'Inter', sans-serif; font-weight: 900; letter-spacing: -0.05em; }
  .sans-light { font-family: 'Inter', sans-serif; font-weight: 300; letter-spacing: -0.01em; }
  .serif { font-family: 'Instrument Serif', serif; font-style: italic; font-weight: 400; }
  .mono { font-family: 'JetBrains Mono', monospace; }

  /* ============================================
     WEBGL ANIMATED BACKGROUND (GLSL SHADER)
     ============================================ */
  #shader-bg {
    position: fixed; inset: 0; z-index: 0;
    width: 100%; height: 100%;
    pointer-events: none;
  }
  
  /* Cursor */
  .cursor-dot, .cursor-ring {
    position: fixed; pointer-events: none; z-index: 9999;
    transform: translate(-50%, -50%);
  }
  .cursor-dot {
    width: 6px; height: 6px; background: var(--peach); border-radius: 50%;
    box-shadow: 0 0 20px var(--peach), 0 0 40px var(--peach);
    mix-blend-mode: screen;
  }
  .cursor-ring {
    width: 40px; height: 40px; border: 1px solid var(--cream); border-radius: 50%;
    transition: all 0.35s cubic-bezier(0.2, 0.8, 0.2, 1);
    mix-blend-mode: difference;
  }
  .cursor-ring.hover { width: 90px; height: 90px; border-color: var(--peach); background: rgba(255,181,167,0.08); }
  
  /* Cursor trail */
  .trail {
    position: fixed; width: 4px; height: 4px; border-radius: 50%;
    background: var(--peach); pointer-events: none; z-index: 9998;
    transform: translate(-50%, -50%);
    mix-blend-mode: screen;
  }

  /* Mouse glow */
  .mouse-glow {
    position: fixed; width: 800px; height: 800px; border-radius: 50%;
    background: radial-gradient(circle, rgba(255,181,167,0.18) 0%, rgba(224,170,255,0.05) 40%, transparent 70%);
    pointer-events: none; z-index: 1;
    transform: translate(-50%, -50%);
    mix-blend-mode: screen;
  }

  /* Film grain */
  .grain {
    position: fixed; inset: 0; pointer-events: none; z-index: 9997; opacity: 0.08;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 400 400' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='3' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
    mix-blend-mode: overlay;
  }

  /* Scroll progress */
  .scroll-progress {
    position: fixed; top: 0; left: 0; height: 2px; z-index: 9996; width: 0%;
    background: linear-gradient(90deg, var(--peach), var(--lavender), var(--accent));
    box-shadow: 0 0 20px var(--peach);
  }

  /* ============================================
     LOADER
     ============================================ */
  .loader-3d {
    position: fixed; inset: 0; z-index: 10000;
    background: var(--deep);
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    transition: opacity 1.2s cubic-bezier(0.7, 0, 0.3, 1);
  }
  .loader-3d::before {
    content: ''; position: absolute; inset: 0;
    background: 
      radial-gradient(circle at 30% 70%, rgba(255,181,167,0.15) 0%, transparent 50%),
      radial-gradient(circle at 70% 30%, rgba(224,170,255,0.15) 0%, transparent 50%);
  }
  .loader-3d.hidden { opacity: 0; pointer-events: none; }
  .loader-num {
    font-family: 'Instrument Serif', serif; font-style: italic;
    font-size: clamp(100px, 25vw, 320px); line-height: 0.9; letter-spacing: -0.06em;
    color: var(--cream); position: relative;
  }
  .loader-dot { color: var(--peach); text-shadow: 0 0 30px var(--peach); }
  .loader-bar {
    width: 280px; height: 1px; background: rgba(245,230,211,0.15);
    margin-top: 3rem; position: relative; overflow: hidden;
  }
  .loader-fill {
    position: absolute; left: 0; top: 0; height: 100%;
    background: linear-gradient(90deg, var(--peach), var(--lavender), var(--accent));
    box-shadow: 0 0 15px var(--peach);
    width: 0%; transition: width 0.25s ease-out;
  }

  /* ============================================
     3D CANVAS
     ============================================ */
  #car-canvas {
    position: fixed; top: 0; left: 0;
    width: 100%; height: 100vh;
    display: block; z-index: 2;
    pointer-events: none;
  }

  /* Float labels */
  .float-label {
    position: fixed; font-family: 'JetBrains Mono', monospace;
    font-size: 10px; letter-spacing: 0.25em; color: var(--peach);
    opacity: 0; pointer-events: none; z-index: 10;
    text-shadow: 0 0 15px rgba(255,181,167,0.9);
    display: flex; align-items: center; gap: 12px;
    white-space: nowrap;
    text-transform: uppercase;
  }
  .float-label::before {
    content: ''; display: inline-block;
    width: 40px; height: 1px; background: var(--peach);
    box-shadow: 0 0 8px var(--peach);
  }
  .float-label::after {
    content: ''; position: absolute; right: -10px; top: 50%;
    width: 6px; height: 6px; border-radius: 50%;
    background: var(--peach); box-shadow: 0 0 15px var(--peach);
    transform: translateY(-50%);
    animation: pulse 2s infinite;
  }
  @keyframes pulse {
    0%, 100% { opacity: 1; transform: translateY(-50%) scale(1); }
    50% { opacity: 0.5; transform: translateY(-50%) scale(1.5); }
  }

  /* ============================================
     MENU (FULLSCREEN OVERLAY)
     ============================================ */
  .menu-trigger {
    position: fixed; bottom: 2rem; right: 2rem; z-index: 500;
    width: 68px; height: 68px; border-radius: 50%;
    background: var(--peach); border: none; cursor: pointer;
    transition: all 0.5s cubic-bezier(0.2, 0.8, 0.2, 1);
    box-shadow: 0 15px 40px rgba(255,181,167,0.4), 0 0 80px rgba(255,181,167,0.25);
    display: flex; align-items: center; justify-content: center;
  }
  .menu-trigger:hover { transform: scale(1.1) rotate(90deg); background: var(--cream); }
  .menu-trigger .bars { width: 22px; height: 14px; position: relative; }
  .menu-trigger .bars span {
    display: block; width: 100%; height: 1.5px; background: var(--deep);
    position: absolute; transition: all 0.4s cubic-bezier(0.7, 0, 0.3, 1);
  }
  .menu-trigger .bars span:nth-child(1) { top: 0; }
  .menu-trigger .bars span:nth-child(2) { top: 6px; }
  .menu-trigger .bars span:nth-child(3) { top: 12px; }
  .menu-trigger.active .bars span:nth-child(1) { top: 6px; transform: rotate(45deg); }
  .menu-trigger.active .bars span:nth-child(2) { opacity: 0; }
  .menu-trigger.active .bars span:nth-child(3) { top: 6px; transform: rotate(-45deg); }

  .fullscreen-menu {
    position: fixed; inset: 0; z-index: 400;
    background: rgba(15, 7, 32, 0.96);
    backdrop-filter: blur(40px) saturate(180%);
    -webkit-backdrop-filter: blur(40px) saturate(180%);
    display: flex; align-items: center;
    opacity: 0; pointer-events: none;
    transition: opacity 0.6s cubic-bezier(0.7, 0, 0.3, 1);
  }
  .fullscreen-menu.open { opacity: 1; pointer-events: all; }
  .fullscreen-menu::before {
    content: ''; position: absolute; inset: 0; pointer-events: none;
    background: 
      radial-gradient(circle at 20% 80%, rgba(255,181,167,0.2) 0%, transparent 50%),
      radial-gradient(circle at 80% 20%, rgba(224,170,255,0.18) 0%, transparent 50%);
  }
  .menu-list { list-style: none; width: 100%; max-width: 1400px; padding: 4rem 6vw; position: relative; }
  .menu-item {
    border-top: 1px solid rgba(245,230,211,0.15);
    padding: 2.5rem 0; cursor: pointer;
    display: flex; justify-content: space-between; align-items: center;
    transition: padding 0.5s cubic-bezier(0.2, 0.8, 0.2, 1);
    position: relative; overflow: hidden;
  }
  .menu-item:last-child { border-bottom: 1px solid rgba(245,230,211,0.15); }
  .menu-item:hover { padding-left: 3rem; }
  .menu-item::before {
    content: ''; position: absolute; left: 0; top: 50%;
    width: 0; height: 2px; background: var(--peach);
    transition: width 0.5s cubic-bezier(0.2, 0.8, 0.2, 1);
    box-shadow: 0 0 10px var(--peach);
  }
  .menu-item:hover::before { width: 1.5rem; }
  .menu-label {
    font-family: 'Inter', sans-serif; font-weight: 900;
    font-size: clamp(3rem, 10vw, 8rem); line-height: 0.9;
    letter-spacing: -0.05em; display: flex; gap: 0.1em;
  }
  .menu-label .sans-part, .menu-label .serif-part { 
    transition: all 0.6s cubic-bezier(0.2, 0.8, 0.2, 1); 
    display: inline-block; 
  }
  .menu-label .serif-part { 
    font-family: 'Instrument Serif', serif; 
    font-style: italic; 
    font-weight: 400; 
    color: var(--peach); 
  }
  .menu-item:hover .menu-label .sans-part { color: var(--peach); transform: translateX(15px); }
  .menu-item:hover .menu-label .serif-part { transform: translateX(-15px); }
  .menu-meta { display: flex; gap: 2rem; opacity: 0.5; transition: opacity 0.5s; }
  .menu-item:hover .menu-meta { opacity: 1; }

  /* ============================================
     GALLERY (TILT EFFECT)
     ============================================ */
  .gallery-card {
    position: relative; overflow: hidden;
    transition: transform 0.7s cubic-bezier(0.2, 0.8, 0.2, 1);
    background: rgba(36,22,56,0.4);
    transform-style: preserve-3d;
  }
  .gallery-card:hover { transform: translateY(-12px); }
  .gallery-card .card-img {
    transition: all 1.2s cubic-bezier(0.2, 0.8, 0.2, 1);
    filter: saturate(0.85) contrast(1.05);
    width: 100%; height: 100%; object-fit: cover;
    display: block;
  }
  .gallery-card:hover .card-img { transform: scale(1.08); filter: saturate(1.15) contrast(1.1); }
  .gallery-card::after {
    content: ''; position: absolute; inset: 0; pointer-events: none;
    background: linear-gradient(180deg, transparent 30%, rgba(15,7,32,0.95) 100%);
  }
  .gallery-card .card-content { position: absolute; inset: 0; z-index: 2; }

  /* ============================================
     MATCH
     ============================================ */
  .match-option {
    transition: all 0.6s cubic-bezier(0.2, 0.8, 0.2, 1);
    cursor: pointer; position: relative; overflow: hidden;
    background: rgba(36,22,56,0.5);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    border: 1px solid rgba(245,230,211,0.1);
  }
  .match-option::before {
    content: ''; position: absolute; inset: 0;
    background: linear-gradient(135deg, transparent 0%, rgba(255,181,167,0.15) 100%);
    opacity: 0; transition: opacity 0.5s;
    pointer-events: none;
  }
  .match-option:hover { transform: scale(1.015); border-color: var(--peach); }
  .match-option:hover::before { opacity: 1; }
  .match-option.voted { background: var(--peach); color: var(--deep); border-color: var(--peach); }
  .match-option.voted .match-meta { color: var(--deep); opacity: 0.7; }
  .match-option.voted::before { opacity: 0; }

  /* ============================================
     MARQUEE
     ============================================ */
  .marquee { overflow: hidden; white-space: nowrap; }
  .marquee-track { display: inline-flex; animation: marquee 45s linear infinite; }
  @keyframes marquee { from { transform: translateX(0); } to { transform: translateX(-50%); } }

  /* Magnetic button */
  .mag-btn { position: relative; overflow: hidden; display: inline-block; transition: transform 0.3s; }
  .mag-btn::before {
    content: ''; position: absolute; inset: 0; background: var(--peach);
    transform: translateY(100%);
    transition: transform 0.5s cubic-bezier(0.7, 0, 0.3, 1);
  }
  .mag-btn:hover::before { transform: translateY(0); }
  .mag-btn span { position: relative; z-index: 1; transition: color 0.5s; }
  .mag-btn:hover span { color: var(--deep); }

  /* Lenis */
  html.lenis, html.lenis body { height: auto; }
  .lenis.lenis-smooth { scroll-behavior: auto !important; }
  .lenis.lenis-stopped { overflow: hidden; }
  ::-webkit-scrollbar { width: 0; display: none; }
  .ul { border-bottom: 1px solid currentColor; padding-bottom: 2px; transition: opacity 0.3s; }
  .ul:hover { opacity: 0.6; }

  /* Side meta */
  .side-meta {
    position: fixed; z-index: 50; mix-blend-mode: difference;
    font-family: 'JetBrains Mono', monospace; font-size: 10px;
    letter-spacing: 0.4em; opacity: 0.8;
  }
  .side-left {
    left: 2rem; top: 50%; transform: translateY(-50%) rotate(-90deg);
    transform-origin: left center;
  }
  .side-right {
    right: 2rem; top: 50%; transform: translateY(-50%) rotate(90deg);
    transform-origin: right center;
  }

  /* Word reveal */
  .word-reveal { display: inline-block; overflow: hidden; vertical-align: top; }
  .word-reveal > span { display: inline-block; transform: translateY(110%); }

  @keyframes scrollDown {
    0% { transform: translateY(-100%); }
    100% { transform: translateY(200%); }
  }

  @media (max-width: 768px) {
    .cursor-dot, .cursor-ring, .mouse-glow, .trail, .side-meta { display: none; }
    .menu-trigger { width: 56px; height: 56px; bottom: 1.5rem; right: 1.5rem; }
    .float-label { display: none; }
    .menu-meta { display: none; }
    .loader-num { font-size: 30vw; }
  }
</style>
</head>
<body>

<!-- Loader -->
<div class="loader-3d" id="loader3d">
  <div class="w-full max-w-2xl px-6 relative">
    <div class="flex justify-between mono text-[10px] tracking-[0.5em] opacity-50 mb-8">
      <span>TEAMBKIT / MMXXVI</span>
      <span>RTBTS</span>
    </div>
    <div class="loader-num" id="loaderNum">0<span class="loader-dot">.</span></div>
    <div class="loader-bar">
      <div class="loader-fill" id="loaderFill"></div>
    </div>
    <div class="flex justify-between mono text-[10px] tracking-widest mt-6 opacity-50">
      <span>LOADING · FERRARI GLB · BLOOM · PBR</span>
      <span id="loaderStatus">INITIALIZING</span>
    </div>
  </div>
</div>

<!-- WebGL Shader Background -->
<canvas id="shader-bg"></canvas>

<!-- Cursor -->
<div class="cursor-ring" id="cursorRing"></div>
<div class="cursor-dot" id="cursorDot"></div>
<div class="mouse-glow" id="mouseGlow"></div>

<!-- Grain + Progress -->
<div class="grain"></div>
<div class="scroll-progress" id="scrollProgress"></div>

<!-- Side meta -->
<div class="side-meta side-left">
  <span id="sectionLabel">TEAMBKIT</span> · <span id="sectionIdx">01</span>/05
</div>
<div class="side-meta side-right" id="liveTime">--:--:-- KST</div>

<!-- Menu -->
<button class="menu-trigger" id="menuTrigger" aria-label="Menu">
  <div class="bars"><span></span><span></span><span></span></div>
</button>

<div class="fullscreen-menu" id="fullscreenMenu">
  <ul class="menu-list">
    <li class="menu-item" data-hover data-target="#machine">
      <div>
        <div class="mono text-[10px] tracking-[0.4em] opacity-40 mb-3">01 / THE MACHINE</div>
        <div class="menu-label"><span class="sans-part">MACH</span><span class="serif-part">ine</span></div>
      </div>
      <div class="menu-meta mono text-[10px] tracking-widest">
        <span>OEM+ · SLEEPER</span>
        <span>3D · BLOOM</span>
      </div>
    </li>
    <li class="menu-item" data-hover data-target="#gallery">
      <div>
        <div class="mono text-[10px] tracking-[0.4em] opacity-40 mb-3">02 / TRACK TO MACHINE</div>
        <div class="menu-label"><span class="sans-part">TRAC</span><span class="serif-part">k</span></div>
      </div>
      <div class="menu-meta mono text-[10px] tracking-widest">
        <span>5 WORKS</span>
        <span>CURATED</span>
      </div>
    </li>
    <li class="menu-item" data-hover data-target="#match">
      <div>
        <div class="mono text-[10px] tracking-[0.4em] opacity-40 mb-3">03 / THE MATCH</div>
        <div class="menu-label"><span class="sans-part">MAT</span><span class="serif-part">ch</span></div>
      </div>
      <div class="menu-meta mono text-[10px] tracking-widest">
        <span>WEEK 01</span>
        <span>LIVE VOTE</span>
      </div>
    </li>
    <li class="menu-item" data-hover data-target="#manifesto">
      <div>
        <div class="mono text-[10px] tracking-[0.4em] opacity-40 mb-3">04 / MANIFESTO</div>
        <div class="menu-label"><span class="sans-part">RT</span><span class="serif-part">bts</span></div>
      </div>
      <div class="menu-meta mono text-[10px] tracking-widest">
        <span>RACE TO BE THE STAR</span>
        <span>CREED</span>
      </div>
    </li>
    <li class="menu-item" data-hover data-target="#proof">
      <div>
        <div class="mono text-[10px] tracking-[0.4em] opacity-40 mb-3">05 / BKIT PROOF</div>
        <div class="menu-label"><span class="sans-part">PRO</span><span class="serif-part">of</span></div>
      </div>
      <div class="menu-meta mono text-[10px] tracking-widest">
        <span>COMING 2026</span>
        <span>QR · NFT</span>
      </div>
    </li>
  </ul>
</div>

<!-- ===== HERO ===== -->
<section id="hero" class="relative min-h-screen flex flex-col justify-between pt-20 pb-12 px-6 md:px-12 overflow-hidden" data-section="TEAMBKIT" data-idx="01" style="z-index: 3;">
  <div class="relative z-10 h-12 flex justify-between items-center mono text-[10px] tracking-[0.4em] opacity-70">
    <span>MMXXVI · EST. SEOUL</span>
    <span>N 37°32′14″ E 127°02′56″</span>
  </div>
  
  <div class="relative z-10 flex-1 flex flex-col justify-center items-center text-center py-10">
    <div class="mono text-[10px] tracking-[0.6em] opacity-70 mb-10 overflow-hidden">
      <span id="heroTag">A CULTURE OF SOUND AND SPEED</span>
    </div>
    
    <div class="overflow-hidden">
      <h1 class="sans text-[clamp(4rem,19vw,24rem)] leading-[0.82] tracking-[-0.055em]" id="heroBrand">TEAMBKIT</h1>
    </div>
    
    <div class="mt-8 md:mt-14 flex items-center gap-4 md:gap-8">
      <div class="h-px w-12 md:w-24" style="background: linear-gradient(90deg, transparent, var(--peach)); box-shadow: 0 0 10px var(--peach);"></div>
      <div class="serif text-xl md:text-3xl opacity-95 tracking-wide" id="heroSub">TRACK TO MACHINE</div>
      <div class="h-px w-12 md:w-24" style="background: linear-gradient(90deg, var(--peach), transparent); box-shadow: 0 0 10px var(--peach);"></div>
    </div>
    
    <div class="mt-10 md:mt-14 sans-light text-sm md:text-base opacity-60 tracking-wide max-w-md">
      Where music meets machine.<br>
      <span class="serif text-base md:text-lg">차를 타는 방식이 아니라, 살아가는 방식.</span>
    </div>
  </div>
  
  <div class="relative z-10 flex justify-between items-end mono text-[10px] tracking-[0.3em]">
    <div>
      <div class="opacity-40 mb-2">SCROLL ↓</div>
      <div class="w-px h-14 relative overflow-hidden" style="background: rgba(245,230,211,0.2);">
        <div class="absolute top-0 left-0 w-full h-1/2" style="background: linear-gradient(180deg, var(--peach), var(--lavender)); box-shadow: 0 0 10px var(--peach); animation: scrollDown 2s infinite;"></div>
      </div>
    </div>
    <div class="text-right">
      <div class="opacity-40 mb-1">CHAPTER</div>
      <div>01 / 05</div>
    </div>
  </div>
</section>

<!-- ===== THE MACHINE (3D Ferrari) ===== -->
<section id="machine" class="relative" style="height: 500vh; z-index: 3;" data-section="THE MACHINE" data-idx="02">
  <canvas id="car-canvas"></canvas>
  
  <div class="absolute top-12 left-6 md:left-12 z-20">
    <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-3">[ 01 / THE MACHINE ]</div>
    <div class="serif text-2xl md:text-4xl opacity-95">OEM+ · SLEEPER</div>
  </div>
  
  <div class="float-label" id="labelWheel" style="top: 70%; left: 18%;">BBS CI-R · 19" FORGED</div>
  <div class="float-label" id="labelBrake" style="top: 62%; left: 24%;">BREMBO GT · 6-PISTON</div>
  <div class="float-label" id="labelBody" style="top: 25%; left: 52%;">STOCK OUTSIDE.</div>
  <div class="float-label" id="labelEngine" style="top: 40%; right: 12%;">FASTER INSIDE.</div>
  <div class="float-label" id="labelExhaust" style="bottom: 28%; right: 18%;">AKRAPOVIČ · TITANIUM</div>
  
  <div class="absolute bottom-12 right-6 md:right-12 z-20 text-right max-w-sm">
    <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-3">BKIT · 001 / 2026</div>
    <div class="serif text-3xl md:text-5xl opacity-95" id="machineCaption">Stock Outside.</div>
  </div>
</section>

<!-- ===== MARQUEE ===== -->
<section class="py-12 border-y border-[var(--cream)]/10 relative z-10 bg-[var(--deep)]/70 backdrop-blur-md" style="z-index: 3;">
  <div class="marquee">
    <div class="marquee-track serif text-[clamp(2.5rem,8vw,6rem)] tracking-[-0.02em]">
      <span class="mx-10">TRACK TO MACHINE</span>
      <span class="mx-10" style="color:var(--peach)">✦</span>
      <span class="mx-10">RTBTS</span>
      <span class="mx-10" style="color:var(--lavender)">✦</span>
      <span class="mx-10 sans-light">SLEEPER</span>
      <span class="mx-10" style="color:var(--peach)">✦</span>
      <span class="mx-10">OEM+</span>
      <span class="mx-10" style="color:var(--accent)">✦</span>
      <span class="mx-10 sans-light">BKIT PROOF</span>
      <span class="mx-10" style="color:var(--peach)">✦</span>
      <span class="mx-10">TRACK TO MACHINE</span>
      <span class="mx-10" style="color:var(--peach)">✦</span>
      <span class="mx-10">RTBTS</span>
      <span class="mx-10" style="color:var(--lavender)">✦</span>
      <span class="mx-10 sans-light">SLEEPER</span>
      <span class="mx-10" style="color:var(--peach)">✦</span>
      <span class="mx-10">OEM+</span>
      <span class="mx-10" style="color:var(--accent)">✦</span>
      <span class="mx-10 sans-light">BKIT PROOF</span>
      <span class="mx-10" style="color:var(--peach)">✦</span>
    </div>
  </div>
</section>

<!-- ===== GALLERY ===== -->
<section id="gallery" class="py-32 md:py-48 px-6 md:px-12 relative z-10" data-section="TRACK TO MACHINE" data-idx="03" style="z-index: 3; background: var(--deep);">
  <div class="max-w-[1500px] mx-auto">
    <div class="flex flex-col md:flex-row md:items-end justify-between mb-20 gap-8">
      <div>
        <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-5">[ 02 / TRACK TO MACHINE ]</div>
        <h2 class="leading-[0.88] tracking-[-0.03em]">
          <span class="sans text-[clamp(2.5rem,8vw,7rem)]">TRACK</span><br>
          <span class="serif text-[clamp(2.5rem,8vw,7rem)]" style="color:var(--peach)">to Machine.</span>
        </h2>
      </div>
      <p class="text-sm opacity-70 max-w-sm serif italic text-lg leading-relaxed">
        한 곡의 노래가 한 대의 차를 만든다.<br>
        소리와 속도의 번역.
      </p>
    </div>

    <div class="grid md:grid-cols-12 gap-4 md:gap-6">
      <div class="md:col-span-8 md:row-span-2 gallery-card aspect-[4/5] md:aspect-[5/4] relative" data-hover data-tilt>
        <img src="https://images.unsplash.com/photo-1544636331-e26879cd4d9b?w=1400&q=90" class="card-img" alt="Track 01">
        <div class="card-content p-8 md:p-12 flex flex-col justify-end">
          <div class="mono text-[10px] tracking-[0.4em] mb-4" style="color:var(--peach)">TRACK 01 / 2026</div>
          <h3 class="serif text-[clamp(2.5rem,6vw,5.5rem)] leading-none mb-3">Abracadabra</h3>
          <p class="mono text-xs opacity-90 tracking-[0.2em]">LADY GAGA × TOYOTA GR86</p>
        </div>
      </div>

      <div class="md:col-span-4 gallery-card aspect-square relative" data-hover data-tilt>
        <img src="https://images.unsplash.com/photo-1555215695-3004980ad54e?w=800&q=90" class="card-img" alt="Track 02">
        <div class="card-content p-6 flex flex-col justify-end">
          <div class="mono text-[9px] tracking-[0.4em] mb-3" style="color:var(--peach)">TRACK 02</div>
          <h3 class="serif text-2xl leading-none mb-2">Die With A Smile</h3>
          <p class="mono text-[10px] opacity-80 tracking-wider">BMW M3 E46</p>
        </div>
      </div>

      <div class="md:col-span-4 gallery-card aspect-square relative" data-hover data-tilt>
        <img src="https://images.unsplash.com/photo-1611821064430-0d4024199b52?w=800&q=90" class="card-img" alt="Track 03">
        <div class="card-content p-6 flex flex-col justify-end">
          <div class="mono text-[9px] tracking-[0.4em] mb-3" style="color:var(--lavender)">TRACK 03</div>
          <h3 class="serif text-2xl leading-none mb-2">The Perfect Girl</h3>
          <p class="mono text-[10px] opacity-80 tracking-wider">NISSAN 180SX</p>
        </div>
      </div>

      <div class="md:col-span-6 gallery-card aspect-[16/10] relative" data-hover data-tilt>
        <img src="https://images.unsplash.com/photo-1503376780353-7e6692767b70?w=1200&q=90" class="card-img" alt="Track 04">
        <div class="card-content p-7 flex flex-col justify-end">
          <div class="mono text-[10px] tracking-[0.4em] mb-3" style="color:var(--peach)">TRACK 04</div>
          <h3 class="serif text-3xl md:text-4xl leading-none mb-2">Nightcall</h3>
          <p class="mono text-xs opacity-90 tracking-wider">KAVINSKY × AUDI RS6 C6</p>
        </div>
      </div>

      <div class="md:col-span-6 gallery-card aspect-[16/10] relative" data-hover data-tilt>
        <img src="https://images.unsplash.com/photo-1494905998402-8f93d1c5b0ad?w=1200&q=90" class="card-img" alt="Track 05">
        <div class="card-content p-7 flex flex-col justify-end">
          <div class="mono text-[10px] tracking-[0.4em] mb-3" style="color:var(--lavender)">TRACK 05</div>
          <h3 class="serif text-3xl md:text-4xl leading-none mb-2">Midnight City</h3>
          <p class="mono text-xs opacity-90 tracking-wider">M83 × GENESIS G70</p>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- ===== MATCH ===== -->
<section id="match" class="py-32 md:py-48 px-6 md:px-12 relative z-10" data-section="THE MATCH" data-idx="04" style="z-index: 3; background: var(--deep);">
  <div class="max-w-[1300px] mx-auto">
    <div class="flex flex-col md:flex-row md:items-end justify-between mb-20 gap-8">
      <div>
        <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-5">[ 03 / THE MATCH · WEEK 01 ]</div>
        <h2 class="leading-[0.88] tracking-[-0.03em]">
          <span class="sans text-[clamp(2.5rem,8vw,7rem)]">THE</span><br>
          <span class="serif text-[clamp(2.5rem,8vw,7rem)]" style="color:var(--peach)">Match.</span>
        </h2>
      </div>
      <div class="mono text-[10px] tracking-widest opacity-80 text-right">
        <div>VOTES · <span style="color:var(--peach)">1,247</span></div>
        <div class="mt-1">CLOSES · 06.23.26</div>
      </div>
    </div>

    <div class="mb-14 border-t border-b border-[var(--cream)]/15 py-10 flex flex-col md:flex-row justify-between items-start md:items-center gap-6">
      <div>
        <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-4">NOW PLAYING</div>
        <div class="serif text-4xl md:text-6xl leading-tight">Blinding Lights</div>
        <div class="mono text-xs opacity-60 mt-3 tracking-widest">THE WEEKND · 2019</div>
      </div>
      <button class="mag-btn border border-[var(--cream)]/30 px-7 py-4 mono text-[10px] tracking-[0.3em]" data-hover data-mag>
        <span>▶ PREVIEW 30s</span>
      </button>
    </div>

    <div class="grid md:grid-cols-2 gap-5 md:gap-8" id="matchOptions">
      <div class="match-option p-0 overflow-hidden" data-hover data-choice="A">
        <div class="aspect-[4/3] overflow-hidden relative">
          <img src="https://images.unsplash.com/photo-1555215695-3004980ad54e?w=1000&q=90" class="w-full h-full object-cover grayscale transition-all duration-700" alt="A">
          <div class="absolute top-5 left-5 mono text-[10px] tracking-[0.4em] px-3 py-1.5" style="background:rgba(15,7,32,0.85); backdrop-filter: blur(12px); color:var(--peach);">OPTION A</div>
        </div>
        <div class="p-7 md:p-10">
          <div class="flex justify-between items-start mb-5">
            <div class="mono text-[10px] tracking-[0.4em] opacity-60">M3 E46 · 2003</div>
            <div class="mono text-[10px] tracking-[0.4em] match-percent" style="color:var(--peach)">55%</div>
          </div>
          <h3 class="serif text-3xl md:text-5xl mb-4 leading-tight">BMW M3 E46</h3>
          <p class="mono text-xs opacity-70 match-meta tracking-wider">SILVERSTONE · 3.2L I6 · 338HP</p>
        </div>
      </div>

      <div class="match-option p-0 overflow-hidden" data-hover data-choice="B">
        <div class="aspect-[4/3] overflow-hidden relative">
          <img src="https://images.unsplash.com/photo-1611821064430-0d4024199b52?w=1000&q=90" class="w-full h-full object-cover grayscale transition-all duration-700" alt="B">
          <div class="absolute top-5 left-5 mono text-[10px] tracking-[0.4em] px-3 py-1.5" style="background:rgba(15,7,32,0.85); backdrop-filter: blur(12px); color:var(--lavender);">OPTION B</div>
        </div>
        <div class="p-7 md:p-10">
          <div class="flex justify-between items-start mb-5">
            <div class="mono text-[10px] tracking-[0.4em] opacity-60">SKYLINE R34 · 1999</div>
            <div class="mono text-[10px] tracking-[0.4em] match-percent" style="color:var(--peach)">45%</div>
          </div>
          <h3 class="serif text-3xl md:text-5xl mb-4 leading-tight">Nissan Skyline R34</h3>
          <p class="mono text-xs opacity-70 match-meta tracking-wider">BAYSIDE BLUE · RB26DETT · 280HP</p>
        </div>
      </div>
    </div>

    <div class="mt-14 flex flex-col md:flex-row justify-between items-start md:items-center gap-4 mono text-[10px] tracking-[0.4em] opacity-80">
      <div>NEXT WEEK · ABBA × ALFA ROMEO GTV</div>
      <a href="#" class="ul" data-hover style="color:var(--peach)">CAST YOUR VOTE →</a>
    </div>
  </div>
</section>

<!-- ===== MANIFESTO ===== -->
<section id="manifesto" class="py-32 md:py-48 px-6 md:px-12 relative z-10 overflow-hidden" data-section="RTBTS" data-idx="05" style="z-index: 3; background: var(--deep);">
  <div class="max-w-[1500px] mx-auto text-center relative">
    <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-10">[ 04 / MANIFESTO ]</div>
    
    <div class="overflow-hidden mb-8">
      <h2 class="sans text-[clamp(5rem,20vw,26rem)] leading-[0.82] tracking-[-0.055em]" id="rtbtsText">RTBTS</h2>
    </div>
    
    <div class="flex items-center justify-center gap-5 mb-24">
      <div class="h-px w-16 md:w-24" style="background: linear-gradient(90deg, transparent, var(--peach)); box-shadow: 0 0 10px var(--peach);"></div>
      <p class="serif text-xl md:text-3xl opacity-95 tracking-wide">Race To Be The Star.</p>
      <div class="h-px w-16 md:w-24" style="background: linear-gradient(90deg, var(--peach), transparent); box-shadow: 0 0 10px var(--peach);"></div>
    </div>
    
    <div class="grid md:grid-cols-3 gap-8 md:gap-14 max-w-5xl mx-auto text-left">
      <div class="border-t pt-8" style="border-color: rgba(255,181,167,0.3);">
        <div class="mono text-[10px] tracking-[0.4em] mb-5" style="color:var(--peach)">01 / SLEEPER</div>
        <h3 class="serif text-3xl md:text-4xl mb-5 leading-tight">겉은 조용하게.</h3>
        <p class="text-sm leading-relaxed opacity-70">순정의 미학을 해치지 않는다. 로고 하나 더 달지 않는다. 아는 사람만 알아보는 조용한 자신감.</p>
      </div>
      <div class="border-t pt-8" style="border-color: rgba(224,170,255,0.3);">
        <div class="mono text-[10px] tracking-[0.4em] mb-5" style="color:var(--lavender)">02 / OEM+</div>
        <h3 class="serif text-3xl md:text-4xl mb-5 leading-tight">속은 정확하게.</h3>
        <p class="text-sm leading-relaxed opacity-70">공장에서 나온 것 같은 완성도. 순정 품질을 뛰어넘는 파츠. 모든 튜닝은 원래 거기 있었던 것처럼.</p>
      </div>
      <div class="border-t pt-8" style="border-color: rgba(245,230,211,0.3);">
        <div class="mono text-[10px] tracking-[0.4em] mb-5 opacity-80">03 / BKIT PROOF</div>
        <h3 class="serif text-3xl md:text-4xl mb-5 leading-tight">증명은 QR로.</h3>
        <p class="text-sm leading-relaxed opacity-70">말보다 데이터. 주장보다 검증. 차주의 취향을 디지털 자산으로 남긴다.</p>
      </div>
    </div>
  </div>
</section>

<!-- ===== BKIT PROOF ===== -->
<section id="proof" class="py-32 md:py-48 px-6 md:px-12 relative z-10" data-section="BKIT PROOF" data-idx="06" style="z-index: 3; background: var(--deep);">
  <div class="max-w-[1200px] mx-auto text-center relative">
    <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-10">[ 05 / NEXT CHAPTER ]</div>
    
    <h2 class="leading-[0.88] tracking-[-0.04em] mb-8">
      <span class="sans text-[clamp(3.5rem,11vw,10rem)]">BKIT</span><br>
      <span class="serif text-[clamp(3.5rem,11vw,10rem)]" style="color:var(--peach)">Proof.</span>
    </h2>
    
    <div class="mono text-[10px] tracking-[0.5em] opacity-70 mb-24">COMING · 2026 · SEOUL</div>

    <div class="grid md:grid-cols-3 gap-8 md:gap-10 max-w-4xl mx-auto mb-20">
      <div class="border-t pt-8 text-left" style="border-color: rgba(255,181,167,0.3);">
        <div class="mono text-[10px] tracking-[0.4em] mb-4" style="color:var(--peach)">PROOF 01</div>
        <h3 class="serif text-2xl md:text-3xl mb-4">Register</h3>
        <p class="text-xs opacity-70 leading-relaxed">차의 모든 디테일을 기록. 휠, 캘리퍼, 배기까지.</p>
      </div>
      <div class="border-t pt-8 text-left" style="border-color: rgba(224,170,255,0.3);">
        <div class="mono text-[10px] tracking-[0.4em] mb-4" style="color:var(--lavender)">PROOF 02</div>
        <h3 class="serif text-2xl md:text-3xl mb-4">Verify</h3>
        <p class="text-xs opacity-70 leading-relaxed">커뮤니티가 검증하는 빌드. 차덕들의 법정.</p>
      </div>
      <div class="border-t pt-8 text-left" style="border-color: rgba(245,230,211,0.3);">
        <div class="mono text-[10px] tracking-[0.4em] mb-4 opacity-80">PROOF 03</div>
        <h3 class="serif text-2xl md:text-3xl mb-4">Display</h3>
        <p class="text-xs opacity-70 leading-relaxed">QR 한 장의 디지털 증명서. 차에 붙이는 자부심.</p>
      </div>
    </div>

    <form class="max-w-md mx-auto" onsubmit="event.preventDefault(); this.querySelector('input').value=''; this.querySelector('button').textContent='✓ REGISTERED';">
      <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-5">NOTIFY · WHEN LAUNCHES</div>
      <div class="flex border-b pb-4" style="border-color: rgba(245,230,211,0.3);">
        <input type="email" placeholder="your@email.com" required class="bg-transparent flex-1 text-sm mono outline-none placeholder:opacity-40">
        <button class="mono text-sm tracking-widest" type="submit" data-hover style="color:var(--peach)">→</button>
      </div>
    </form>
  </div>
</section>

<!-- ===== FOOTER ===== -->
<footer class="py-20 px-6 md:px-12 border-t border-[var(--cream)]/10 relative z-10" style="z-index: 3; background: var(--deep);">
  <div class="max-w-[1500px] mx-auto">
    <div class="mb-20 text-center">
      <div class="sans text-[clamp(5rem,18vw,18rem)] tracking-[-0.055em] leading-[0.82]">
        TEAMBKIT<span style="color:var(--peach)">.</span>
      </div>
    </div>
    
    <div class="grid md:grid-cols-12 gap-10 pb-12 border-b border-[var(--cream)]/10">
      <div class="md:col-span-4">
        <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-5">WHERE MUSIC MEETS MACHINE</div>
        <p class="serif italic text-2xl leading-relaxed max-w-xs opacity-90">
          차를 타는 방식이 아니라,<br>살아가는 방식.
        </p>
      </div>
      <div class="md:col-span-2">
        <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-5">NAV</div>
        <ul class="space-y-3 text-sm">
          <li><a href="#machine" class="ul" data-hover>Machine</a></li>
          <li><a href="#gallery" class="ul" data-hover>Track</a></li>
          <li><a href="#match" class="ul" data-hover>Match</a></li>
          <li><a href="#manifesto" class="ul" data-hover>RTBTS</a></li>
          <li><a href="#proof" class="ul" data-hover>Proof</a></li>
        </ul>
      </div>
      <div class="md:col-span-2">
        <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-5">SOCIAL</div>
        <ul class="space-y-3 text-sm">
          <li><a href="#" class="ul" data-hover>Instagram</a></li>
          <li><a href="#" class="ul" data-hover>YouTube</a></li>
          <li><a href="#" class="ul" data-hover>Discord</a></li>
          <li><a href="#" class="ul" data-hover>Twitter</a></li>
        </ul>
      </div>
      <div class="md:col-span-4">
        <div class="mono text-[10px] tracking-[0.4em] opacity-60 mb-5">SEOUL · KR</div>
        <p class="text-xs opacity-60 leading-relaxed mono tracking-wider">
          N 37°32′14″ · E 127°02′56″<br>
          MMXXVI · CAR LIFE CULTURE<br>
          RACE TO BE THE STAR.
        </p>
      </div>
    </div>

    <div class="pt-10 flex flex-col md:flex-row justify-between gap-5 mono text-[10px] tracking-[0.4em] opacity-60">
      <div>© 2026 TEAMBKIT · ALL RIGHTS RESERVED</div>
      <div class="flex gap-8">
        <a href="#" class="ul" data-hover>TERMS</a>
        <a href="#" class="ul" data-hover>PRIVACY</a>
        <a href="#" class="ul" data-hover>CONTACT</a>
      </div>
    </div>
  </div>
</footer>

<script>
  // ==========================================================
  // WEBGL ANIMATED GRADIENT BACKGROUND (GLSL SHADER)
  // ==========================================================
  const shaderCanvas = document.getElementById('shader-bg');
  const shaderScene = new THREE.Scene();
  const shaderCamera = new THREE.OrthographicCamera(-1, 1, 1, -1, 0, 1);
  const shaderRenderer = new THREE.WebGLRenderer({ canvas: shaderCanvas, alpha: true, antialias: false });
  shaderRenderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5));
  shaderRenderer.setSize(window.innerWidth, window.innerHeight);
  
  const shaderUniforms = {
    uTime: { value: 0 },
    uMouse: { value: new THREE.Vector2(0.5, 0.5) },
    uScroll: { value: 0 },
    uResolution: { value: new THREE.Vector2(window.innerWidth, window.innerHeight) }
  };
  
  const shaderMaterial = new THREE.ShaderMaterial({
    uniforms: shaderUniforms,
    vertexShader: `
      void main() {
        gl_Position = vec4(position, 1.0);
      }
    `,
    fragmentShader: `
      uniform float uTime;
      uniform vec2 uMouse;
      uniform float uScroll;
      uniform vec2 uResolution;
      
      // Noise functions
      float hash(vec2 p) {
        return fract(sin(dot(p, vec2(127.1, 311.7))) * 43758.5453);
      }
      
      float noise(vec2 p) {
        vec2 i = floor(p);
        vec2 f = fract(p);
        float a = hash(i);
        float b = hash(i + vec2(1.0, 0.0));
        float c = hash(i + vec2(0.0, 1.0));
        float d = hash(i + vec2(1.0, 1.0));
        vec2 u = f * f * (3.0 - 2.0 * f);
        return mix(a, b, u.x) + (c - a) * u.y * (1.0 - u.x) + (d - b) * u.x * u.y;
      }
      
      float fbm(vec2 p) {
        float v = 0.0;
        float a = 0.5;
        for (int i = 0; i < 5; i++) {
          v += a * noise(p);
          p *= 2.0;
          a *= 0.5;
        }
        return v;
      }
      
      void main() {
        vec2 uv = gl_FragCoord.xy / uResolution.xy;
        vec2 p = uv - 0.5;
        
        // Scroll-based color shift
        float scrollOffset = uScroll * 0.5;
        
        // Multi-layer animated gradient with FBM noise
        float n1 = fbm(p * 2.5 + uTime * 0.08 + scrollOffset);
        float n2 = fbm(p * 4.0 - uTime * 0.12 + vec2(scrollOffset, 0.0));
        float n3 = fbm(p * 6.0 + uTime * 0.05);
        
        // Mouse interaction
        float mouseDist = distance(uv, uMouse);
        float mouseInfluence = smoothstep(0.6, 0.0, mouseDist) * 0.3;
        
        // Color palette
        vec3 deepPurple = vec3(0.06, 0.03, 0.13);
        vec3 purple = vec3(0.29, 0.17, 0.36);
        vec3 peach = vec3(1.0, 0.71, 0.65);
        vec3 lavender = vec3(0.88, 0.67, 1.0);
        vec3 accent = vec3(1.0, 0.42, 0.62);
        
        // Base color mixing
        vec3 col = deepPurple;
        col = mix(col, purple, smoothstep(0.2, 0.6, n1 + scrollOffset * 0.3));
        col = mix(col, peach, smoothstep(0.5, 0.8, n2) * 0.4);
        col = mix(col, lavender, smoothstep(0.6, 0.9, n3) * 0.35);
        col = mix(col, accent, mouseInfluence);
        
        // Radial vignette
        float vignette = 1.0 - smoothstep(0.4, 1.4, length(p) * 1.3);
        col *= vignette;
        
        // Subtle glow at mouse position
        col += peach * mouseInfluence * 0.5;
        
        gl_FragColor = vec4(col, 1.0);
      }
    `
  });
  
  const shaderQuad = new THREE.Mesh(new THREE.PlaneGeometry(2, 2), shaderMaterial);
  shaderScene.add(shaderQuad);
  
  // ==========================================================
  // THREE.JS 3D CAR SCENE
  // ==========================================================
  const canvas = document.getElementById('car-canvas');
  const scene = new THREE.Scene();
  scene.fog = new THREE.Fog(0x0f0720, 12, 35);
  
  const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 100);
  camera.position.set(6, 2.5, 8);
  camera.lookAt(0, 0, 0);
  
  const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5));
  renderer.setClearColor(0x000000, 0);
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  renderer.outputEncoding = THREE.sRGBEncoding;
  renderer.toneMapping = THREE.ACESFilmicToneMapping;
  renderer.toneMappingExposure = 1.2;
  
  // Post-processing: Bloom
  const renderScene = new THREE.RenderPass(scene, camera);
  const bloomPass = new THREE.UnrealBloomPass(
    new THREE.Vector2(window.innerWidth, window.innerHeight),
    1.2, 0.5, 0.25
  );
  bloomPass.threshold = 0.15;
  bloomPass.strength = 1.3;
  bloomPass.radius = 0.5;
  
  const composer = new THREE.EffectComposer(renderer);
  composer.addPass(renderScene);
  composer.addPass(bloomPass);
  
  // Lighting
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
  scene.add(ambientLight);
  
  const dirLight = new THREE.DirectionalLight(0xffb5a7, 2.5);
  dirLight.position.set(5, 8, 5);
  dirLight.castShadow = true;
  dirLight.shadow.mapSize.width = 2048;
  dirLight.shadow.mapSize.height = 2048;
  dirLight.shadow.camera.near = 0.5;
  dirLight.shadow.camera.far = 30;
  dirLight.shadow.camera.left = -10;
  dirLight.shadow.camera.right = 10;
  dirLight.shadow.camera.top = 10;
  dirLight.shadow.camera.bottom = -10;
  dirLight.shadow.bias = -0.0001;
  scene.add(dirLight);
  
  const pointLight1 = new THREE.PointLight(0xe0aaff, 3, 25);
  pointLight1.position.set(-6, 3, 3);
  scene.add(pointLight1);
  
  const pointLight2 = new THREE.PointLight(0xffb5a7, 3, 25);
  pointLight2.position.set(6, -2, 4);
  scene.add(pointLight2);
  
  const rimLight = new THREE.PointLight(0xff6b9d, 4, 18);
  rimLight.position.set(0, 7, -7);
  scene.add(rimLight);
  
  const bottomLight = new THREE.PointLight(0x7c3aed, 2, 15);
  bottomLight.position.set(0, -2, 0);
  scene.add(bottomLight);
  
  // Reflective ground
  const groundGeo = new THREE.PlaneGeometry(100, 100);
  const groundMat = new THREE.MeshStandardMaterial({
    color: 0x0f0720, metalness: 0.85, roughness: 0.25
  });
  const ground = new THREE.Mesh(groundGeo, groundMat);
  ground.rotation.x = -Math.PI / 2;
  ground.position.y = -0.5;
  ground.receiveShadow = true;
  scene.add(ground);
  
  // Floating particles
  const particleCount = 400;
  const particleGeo = new THREE.BufferGeometry();
  const particlePositions = new Float32Array(particleCount * 3);
  for (let i = 0; i < particleCount; i++) {
    particlePositions[i * 3] = (Math.random() - 0.5) * 30;
    particlePositions[i * 3 + 1] = Math.random() * 15 - 2;
    particlePositions[i * 3 + 2] = (Math.random() - 0.5) * 30;
  }
  particleGeo.setAttribute('position', new THREE.BufferAttribute(particlePositions, 3));
  const particleMat = new THREE.PointsMaterial({
    color: 0xffb5a7, size: 0.04, transparent: true, opacity: 0.6,
    blending: THREE.AdditiveBlending
  });
  const particles = new THREE.Points(particleGeo, particleMat);
  scene.add(particles);
  
  // Car Group
  const carGroup = new THREE.Group();
  scene.add(carGroup);
  
  // Load Ferrari GLB
  const loader = new THREE.GLTFLoader();
  const dracoLoader = new THREE.DRACOLoader();
  dracoLoader.setDecoderPath('https://unpkg.com/three@0.128.0/examples/js/libs/draco/');
  loader.setDRACOLoader(dracoLoader);
  
  let carModel = null;
  const loaderFill = document.getElementById('loaderFill');
  const loaderNum = document.getElementById('loaderNum');
  const loaderStatus = document.getElementById('loaderStatus');
  
  loaderFill.style.width = '15%';
  loaderNum.innerHTML = '15<span class="loader-dot">.</span>';
  loaderStatus.textContent = 'LOADING MODEL';
  
  loader.load(
    'https://threejs.org/examples/models/gltf/ferrari.glb',
    function (gltf) {
      carModel = gltf.scene;
      
      // Center and scale
      const box = new THREE.Box3().setFromObject(carModel);
      const center = box.getCenter(new THREE.Vector3());
      const size = box.getSize(new THREE.Vector3());
      const maxDim = Math.max(size.x, size.y, size.z);
      const scale = 4.5 / maxDim;
      carModel.scale.setScalar(scale);
      carModel.position.x = -center.x * scale;
      carModel.position.z = -center.z * scale;
      carModel.position.y = -box.min.y * scale - 0.5;
      
      // Enhance materials
      carModel.traverse((child) => {
        if (child.isMesh) {
          child.castShadow = true;
          child.receiveShadow = true;
          if (child.material) {
            child.material.envMapIntensity = 1.5;
            if (child.material.color && child.material.metalness > 0.5) {
              child.material.color.setRGB(0.25, 0.15, 0.35);
              child.material.metalness = 0.95;
              child.material.roughness = 0.1;
              child.material.clearcoat = 1.0;
              child.material.clearcoatRoughness = 0.05;
            }
          }
        }
      });
      
      carGroup.add(carModel);
      
      loaderFill.style.width = '100%';
      loaderNum.innerHTML = '100<span class="loader-dot">.</span>';
      loaderStatus.textContent = 'READY';
      
      setTimeout(() => {
        document.getElementById('loader3d').classList.add('hidden');
        setTimeout(() => {
          document.getElementById('loader3d').style.display = 'none';
          initSite();
        }, 1000);
      }, 600);
    },
    function (xhr) {
      if (xhr.lengthComputable) {
        const percent = Math.round((xhr.loaded / xhr.total) * 85 + 15);
        loaderFill.style.width = percent + '%';
        loaderNum.innerHTML = percent + '<span class="loader-dot">.</span>';
      }
    },
    function (error) {
      console.error('Error:', error);
      createFallbackCar();
    }
  );
  
  function createFallbackCar() {
    const bodyMat = new THREE.MeshPhysicalMaterial({
      color: 0x4a2b5c, metalness: 0.9, roughness: 0.1,
      clearcoat: 1.0, clearcoatRoughness: 0.05
    });
    const bodyGeo = new THREE.CapsuleGeometry(0.8, 3, 16, 32);
    const body = new THREE.Mesh(bodyGeo, bodyMat);
    body.rotation.z = Math.PI / 2;
    body.scale.set(1, 0.5, 1);
    body.castShadow = true;
    carGroup.add(body);
    document.getElementById('loader3d').classList.add('hidden');
    setTimeout(initSite, 500);
  }
  
  // Animation loop
  let time = 0;
  let mouseX = 0.5, mouseY = 0.5;
  function animate() {
    time += 0.01;
    
    // Shader animation
    shaderUniforms.uTime.value = time * 3;
    shaderUniforms.uMouse.value.set(mouseX, 1 - mouseY);
    shaderUniforms.uScroll.value = window.scrollY / (document.body.scrollHeight - window.innerHeight);
    shaderRenderer.render(shaderScene, shaderCamera);
    
    // Car subtle float + rotate
    if (carModel) {
      carGroup.position.y = Math.sin(time * 1.5) * 0.03;
    }
    
    // Particles drift
    particles.rotation.y = time * 0.02;
    particles.rotation.x = Math.sin(time * 0.5) * 0.05;
    
    composer.render();
    requestAnimationFrame(animate);
  }
  animate();
  
  window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
    composer.setSize(window.innerWidth, window.innerHeight);
    shaderRenderer.setSize(window.innerWidth, window.innerHeight);
    shaderUniforms.uResolution.value.set(window.innerWidth, window.innerHeight);
  });

  // ==========================================================
  // LENIS SMOOTH SCROLL
  // ==========================================================
  const lenis = new Lenis({
    duration: 1.4,
    easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
    smoothWheel: true,
  });
  function raf(time) { lenis.raf(time); requestAnimationFrame(raf); }
  requestAnimationFrame(raf);
  gsap.registerPlugin(ScrollTrigger);
  lenis.on('scroll', ScrollTrigger.update);
  gsap.ticker.add((time) => { lenis.raf(time * 1000); });
  gsap.ticker.lagSmoothing(0);

  // ==========================================================
  // CURSOR + TRAIL
  // ==========================================================
  const dot = document.getElementById('cursorDot');
  const ring = document.getElementById('cursorRing');
  const mouseGlow = document.getElementById('mouseGlow');
  let mx = window.innerWidth/2, my = window.innerHeight/2, rx = mx, ry = my;
  const trails = [];
  for (let i = 0; i < 15; i++) {
    const t = document.createElement('div');
    t.className = 'trail';
    t.style.opacity = (1 - i / 15) * 0.5;
    t.style.width = (4 - i * 0.2) + 'px';
    t.style.height = (4 - i * 0.2) + 'px';
    document.body.appendChild(t);
    trails.push({ el: t, x: mx, y: my });
  }
  
  window.addEventListener('mousemove', e => {
    mx = e.clientX; my = e.clientY;
    mouseX = e.clientX / window.innerWidth;
    mouseY = e.clientY / window.innerHeight;
    dot.style.left = mx + 'px'; dot.style.top = my + 'px';
    mouseGlow.style.left = mx + 'px'; mouseGlow.style.top = my + 'px';
  });
  
  function animateCursor() {
    rx += (mx - rx) * 0.15; ry += (my - ry) * 0.15;
    ring.style.left = rx + 'px'; ring.style.top = ry + 'px';
    
    // Trail follow
    let prevX = mx, prevY = my;
    trails.forEach((t, i) => {
      t.x += (prevX - t.x) * (0.25 - i * 0.01);
      t.y += (prevY - t.y) * (0.25 - i * 0.01);
      t.el.style.left = t.x + 'px';
      t.el.style.top = t.y + 'px';
      prevX = t.x; prevY = t.y;
    });
    
    requestAnimationFrame(animateCursor);
  }
  animateCursor();
  
  document.querySelectorAll('[data-hover]').forEach(el => {
    el.addEventListener('mouseenter', () => ring.classList.add('hover'));
    el.addEventListener('mouseleave', () => ring.classList.remove('hover'));
  });

  // ==========================================================
  // MAGNETIC BUTTONS
  // ==========================================================
  document.querySelectorAll('[data-mag]').forEach(btn => {
    btn.addEventListener('mousemove', e => {
      const rect = btn.getBoundingClientRect();
      const x = e.clientX - rect.left - rect.width / 2;
      const y = e.clientY - rect.top - rect.height / 2;
      btn.style.transform = `translate(${x*0.35}px, ${y*0.35}px)`;
    });
    btn.addEventListener('mouseleave', () => { btn.style.transform = ''; });
  });

  // ==========================================================
  // TILT EFFECT ON GALLERY CARDS
  // ==========================================================
  document.querySelectorAll('[data-tilt]').forEach(card => {
    card.addEventListener('mousemove', e => {
      const rect = card.getBoundingClientRect();
      const x = (e.clientX - rect.left) / rect.width - 0.5;
      const y = (e.clientY - rect.top) / rect.height - 0.5;
      card.style.transform = `translateY(-12px) rotateY(${x * 8}deg) rotateX(${-y * 8}deg)`;
    });
    card.addEventListener('mouseleave', () => { card.style.transform = ''; });
  });

  // ==========================================================
  // LIVE TIME
  // ==========================================================
  function updateTime() {
    const now = new Date();
    const hh = String(now.getHours()).padStart(2,'0');
    const mm = String(now.getMinutes()).padStart(2,'0');
    const ss = String(now.getSeconds()).padStart(2,'0');
    document.getElementById('liveTime').textContent = `${hh}:${mm}:${ss} KST`;
  }
  setInterval(updateTime, 1000); updateTime();

  // ==========================================================
  // INIT SITE (after loader)
  // ==========================================================
  function initSite() {
    // Hero brand reveal
    gsap.from('#heroBrand', { y: '20%', opacity: 0, duration: 2.2, ease: 'power4.out', delay: 0.1 });
    gsap.from('#heroSub', { y: '25px', opacity: 0, duration: 1.6, ease: 'power3.out', delay: 1 });
    gsap.from('#heroTag', { y: '20px', opacity: 0, duration: 1.4, ease: 'power3.out', delay: 0.6 });

    // Hero parallax
    const heroSection = document.getElementById('hero');
    const heroBrand = document.getElementById('heroBrand');
    heroSection.addEventListener('mousemove', (e) => {
      const rect = heroSection.getBoundingClientRect();
      const x = (e.clientX - rect.left) / rect.width - 0.5;
      const y = (e.clientY - rect.top) / rect.height - 0.5;
      gsap.to(heroBrand, { x: x * 30, y: y * 15, duration: 1, ease: 'power2.out' });
    });

    // ===== THE MACHINE SCROLL (5 PHASES) =====
    const caption = document.getElementById('machineCaption');
    
    const machineTl = gsap.timeline({
      scrollTrigger: {
        trigger: '#machine',
        start: 'top top',
        end: 'bottom bottom',
        scrub: 1.5,
        onUpdate: (self) => {
          const p = self.progress;
          if (p < 0.2) caption.textContent = 'Stock Outside.';
          else if (p < 0.4) caption.textContent = 'Faster Inside.';
          else if (p < 0.6) caption.textContent = 'OEM+ Perfected.';
          else if (p < 0.8) caption.textContent = 'Engineered to Perfection.';
          else caption.textContent = 'Race To Be The Star.';
        }
      }
    });
    
    // Phase 1 (0-20%): Front view zoom
    machineTl.to(carGroup.rotation, { y: Math.PI * 0.3, duration: 1, ease: 'none' }, 0);
    machineTl.to(camera.position, { x: 3, y: 1.2, z: 5, duration: 1, ease: 'none' }, 0);
    machineTl.to('#labelWheel', { opacity: 1, duration: 0.15, ease: 'none' }, 0.08);
    machineTl.to('#labelBrake', { opacity: 1, duration: 0.15, ease: 'none' }, 0.1);
    machineTl.to('#labelWheel', { opacity: 0, duration: 0.1, ease: 'none' }, 0.25);
    machineTl.to('#labelBrake', { opacity: 0, duration: 0.1, ease: 'none' }, 0.25);
    
    // Phase 2 (20-40%): Side profile
    machineTl.to(carGroup.rotation, { y: Math.PI * 0.65, duration: 1, ease: 'none' }, 0.25);
    machineTl.to(camera.position, { x: 0, y: 1.8, z: 7.5, duration: 1, ease: 'none' }, 0.25);
    machineTl.to('#labelBody', { opacity: 1, duration: 0.15, ease: 'none' }, 0.32);
    machineTl.to('#labelBody', { opacity: 0, duration: 0.1, ease: 'none' }, 0.5);
    
    // Phase 3 (40-60%): Rear view
    machineTl.to(carGroup.rotation, { y: Math.PI * 1.1, duration: 1, ease: 'none' }, 0.5);
    machineTl.to(camera.position, { x: -3, y: 1.5, z: 5, duration: 1, ease: 'none' }, 0.5);
    machineTl.to('#labelExhaust', { opacity: 1, duration: 0.15, ease: 'none' }, 0.57);
    machineTl.to('#labelExhaust', { opacity: 0, duration: 0.1, ease: 'none' }, 0.75);
    
    // Phase 4 (60-80%): Top view
    machineTl.to(carGroup.rotation, { y: Math.PI * 1.4, duration: 1, ease: 'none' }, 0.75);
    machineTl.to(camera.position, { x: 0, y: 6, z: 3, duration: 1, ease: 'none' }, 0.75);
    
    // Phase 5 (80-100%): Final dramatic 3/4 angle
    machineTl.to(carGroup.rotation, { y: Math.PI * 1.75, duration: 1, ease: 'none' }, 0.85);
    machineTl.to(camera.position, { x: 5, y: 2.8, z: 6, duration: 1, ease: 'none' }, 0.85);
    machineTl.to('#labelEngine', { opacity: 1, duration: 0.15, ease: 'none' }, 0.9);
    machineTl.to('#labelEngine', { opacity: 0, duration: 0.1, ease: 'none' }, 0.99);

    // Gallery cards reveal
    gsap.utils.toArray('.gallery-card').forEach((card, i) => {
      gsap.from(card, {
        y: 120, opacity: 0, duration: 1.3, delay: i * 0.08,
        ease: 'power3.out',
        scrollTrigger: { trigger: card, start: 'top 88%' }
      });
    });

    // Match options
    gsap.utils.toArray('.match-option').forEach((opt, i) => {
      gsap.from(opt, {
        y: 90, opacity: 0, duration: 1.3, delay: i * 0.15,
        ease: 'power3.out',
        scrollTrigger: { trigger: opt, start: 'top 85%' }
      });
    });

    // Manifesto RTBTS
    gsap.from('#rtbtsText', {
      y: '40%', opacity: 0, duration: 2.2, ease: 'power4.out',
      scrollTrigger: { trigger: '#rtbtsText', start: 'top 75%' }
    });
    gsap.utils.toArray('#manifesto .border-t').forEach((el, i) => {
      gsap.from(el, {
        y: 80, opacity: 0, duration: 1.2, delay: i * 0.18,
        ease: 'power3.out',
        scrollTrigger: { trigger: el, start: 'top 88%' }
      });
    });

    // Proof
    gsap.utils.toArray('#proof .border-t').forEach((el, i) => {
      gsap.from(el, {
        y: 60, opacity: 0, duration: 1.1, delay: i * 0.15,
        ease: 'power3.out',
        scrollTrigger: { trigger: el, start: 'top 88%' }
      });
    });

    // Scroll progress + section
    window.addEventListener('scroll', () => {
      const h = document.documentElement;
      const pct = (h.scrollTop / (h.scrollHeight - h.clientHeight)) * 100;
      document.getElementById('scrollProgress').style.width = pct + '%';
      
      let current = 'TEAMBKIT', idx = '01';
      document.querySelectorAll('[data-section]').forEach(el => {
        const rect = el.getBoundingClientRect();
        if (rect.top < window.innerHeight / 2) {
          current = el.dataset.section;
          idx = el.dataset.idx || '01';
        }
      });
      document.getElementById('sectionLabel').textContent = current;
      document.getElementById('sectionIdx').textContent = idx;
    });
    
    ScrollTrigger.refresh();
  }

  // ===== MENU =====
  const menuTrigger = document.getElementById('menuTrigger');
  const fullscreenMenu = document.getElementById('fullscreenMenu');
  let menuOpen = false;
  
  menuTrigger.addEventListener('click', () => {
    menuOpen = !menuOpen;
    menuTrigger.classList.toggle('active');
    fullscreenMenu.classList.toggle('open');
    if (menuOpen) lenis.stop(); else lenis.start();
  });
  
  document.querySelectorAll('.menu-item').forEach(item => {
    item.addEventListener('click', () => {
      const target = item.dataset.target;
      if (target) {
        menuOpen = false;
        menuTrigger.classList.remove('active');
        fullscreenMenu.classList.remove('open');
        lenis.start();
        setTimeout(() => {
          const el = document.querySelector(target);
          if (el) lenis.scrollTo(el, { offset: 0, duration: 2 });
        }, 400);
      }
    });
  });

  // ===== MATCH VOTING =====
  document.querySelectorAll('.match-option').forEach(opt => {
    opt.addEventListener('click', () => {
      const voted = opt.dataset.choice;
      const currentA = 687 + (voted === 'A' ? 1 : 0);
      const currentB = 560 + (voted === 'B' ? 1 : 0);
      const total = currentA + currentB;
      document.querySelectorAll('.match-percent')[0].textContent = Math.round((currentA / total) * 100) + '%';
      document.querySelectorAll('.match-percent')[1].textContent = Math.round((currentB / total) * 100) + '%';
      document.querySelectorAll('.match-option').forEach(o => {
        o.classList.add('voted');
        o.querySelector('img').classList.remove('grayscale');
      });
    });
  });
</script>
</body>
</html>
