<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Darkroom Printing — Jonathan Ramirez</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Fraunces:opsz,wght@9..144,300;9..144,500;9..144,600&family=JetBrains+Mono:wght@400;500;700&display=swap');

  :root{
    --charcoal:#161311;
    --charcoal-2:#1f1b18;
    --paper:#efe8db;
    --paper-dim:#b3a996;
    --safelight:#c23b2d;
    --safelight-dim:#8a2a20;
    --amber:#d9a441;
  }

  *{ box-sizing:border-box; margin:0; padding:0; }

  html{ scroll-behavior:smooth; }

  body{
    background:var(--charcoal);
    color:var(--paper);
    font-family:'JetBrains Mono', monospace;
    -webkit-font-smoothing:antialiased;
    overflow-x:hidden;
  }

  @media (prefers-reduced-motion: reduce){
    *{ animation-duration:0.01ms !important; transition-duration:0.01ms !important; }
  }

  /* subtle film grain */
  body::before{
    content:"";
    position:fixed; inset:0;
    pointer-events:none;
    opacity:0.05;
    z-index:50;
    background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
  }

  .safelight-glow{
    position:fixed;
    top:-20%; right:-10%;
    width:60vw; height:60vw;
    background:radial-gradient(circle, rgba(194,59,45,0.18) 0%, rgba(194,59,45,0) 70%);
    pointer-events:none;
    z-index:0;
  }

  header{
    position:relative;
    z-index:1;
    padding:6vh 6vw 4vh;
    border-bottom:1px solid rgba(239,232,219,0.12);
  }

  .eyebrow{
    color:var(--safelight);
    font-size:0.75rem;
    letter-spacing:0.25em;
    text-transform:uppercase;
    margin-bottom:1.5rem;
    display:flex;
    align-items:center;
    gap:0.6rem;
  }
  .eyebrow::before{
    content:"";
    width:8px; height:8px;
    border-radius:50%;
    background:var(--safelight);
    box-shadow:0 0 12px 2px rgba(194,59,45,0.8);
    animation:pulse 2.4s ease-in-out infinite;
  }
  @keyframes pulse{
    0%,100%{ opacity:1; }
    50%{ opacity:0.35; }
  }

  h1{
    font-family:'Fraunces', serif;
    font-weight:600;
    font-style:italic;
    font-size:clamp(2.4rem, 7vw, 5rem);
    line-height:1.05;
    max-width:16ch;
    color:var(--paper);
    letter-spacing:-0.01em;
  }
  h1 em{
    font-style:normal;
    color:var(--safelight);
    font-family:'JetBrains Mono', monospace;
    font-weight:700;
    font-size:0.5em;
    display:block;
    letter-spacing:0.02em;
    margin-top:0.6em;
  }

  .sub{
    margin-top:2rem;
    max-width:42ch;
    color:var(--paper-dim);
    font-size:1rem;
    line-height:1.7;
  }

  main{ position:relative; z-index:1; }

  .strip{
    display:grid;
    grid-template-columns:repeat(auto-fit, minmax(230px,1fr));
    border-bottom:1px solid rgba(239,232,219,0.12);
  }
  .frame{
    padding:5vh 6vw;
    border-right:1px solid rgba(239,232,219,0.12);
    position:relative;
  }
  .frame:last-child{ border-right:none; }
  .frame .tag{
    font-size:0.7rem;
    color:var(--amber);
    letter-spacing:0.15em;
    text-transform:uppercase;
    margin-bottom:1rem;
  }
  .frame h3{
    font-family:'Fraunces', serif;
    font-weight:500;
    font-size:1.5rem;
    margin-bottom:0.8rem;
    color:var(--paper);
  }
  .frame p{
    color:var(--paper-dim);
    font-size:0.9rem;
    line-height:1.6;
  }

  .frame::after{
    content:attr(data-index);
    position:absolute;
    top:5vh; right:6vw;
    font-size:0.7rem;
    color:rgba(239,232,219,0.3);
  }

  section.detail{
    padding:8vh 6vw;
    display:grid;
    grid-template-columns:1fr;
    gap:3rem;
    border-bottom:1px solid rgba(239,232,219,0.12);
  }
  @media(min-width:800px){
    section.detail{ grid-template-columns:1fr 1fr; }
  }

  .detail h2{
    font-family:'Fraunces', serif;
    font-style:italic;
    font-weight:500;
    font-size:clamp(1.6rem,3vw,2.2rem);
    color:var(--paper);
    margin-bottom:1.2rem;
  }
  .detail p{
    color:var(--paper-dim);
    line-height:1.8;
    font-size:0.95rem;
  }

  .tray{
    background:var(--charcoal-2);
    border:1px solid rgba(239,232,219,0.12);
    padding:2rem;
    position:relative;
    overflow:hidden;
  }
  .tray::before{
    content:"";
    position:absolute; inset:0;
    background:linear-gradient(180deg, transparent 40%, rgba(194,59,45,0.08) 100%);
  }
  .tray .liquid-line{
    position:absolute;
    left:0; right:0; height:1px;
    background:rgba(194,59,45,0.4);
    animation:ripple 4s ease-in-out infinite;
  }
  @keyframes ripple{
    0%,100%{ top:30%; opacity:0.3; }
    50%{ top:70%; opacity:0.6; }
  }
  .tray ul{
    position:relative;
    list-style:none;
  }
  .tray li{
    display:flex;
    justify-content:space-between;
    padding:0.9rem 0;
    border-bottom:1px dashed rgba(239,232,219,0.15);
    font-size:0.85rem;
    color:var(--paper);
  }
  .tray li:last-child{ border-bottom:none; }
  .tray li span:last-child{ color:var(--amber); }

  footer{
    padding:8vh 6vw 6vh;
    position:relative;
    z-index:1;
    display:flex;
    flex-direction:column;
    align-items:flex-start;
    gap:2rem;
  }
  footer .call{
    font-family:'Fraunces', serif;
    font-style:italic;
    font-weight:500;
    font-size:clamp(1.8rem,4vw,2.6rem);
    max-width:20ch;
  }
  .contact-line{
    display:flex;
    flex-wrap:wrap;
    gap:0.5rem 1.5rem;
    font-size:1rem;
    color:var(--paper);
  }
  .contact-line a{
    color:var(--safelight);
    text-decoration:none;
    border-bottom:1px solid var(--safelight-dim);
  }
  .contact-line a:focus-visible, .frame:focus-visible{
    outline:2px solid var(--amber);
    outline-offset:3px;
  }

  .fmt{
    margin-top:1rem;
    display:inline-flex;
    align-items:center;
    gap:0.7rem;
    padding:0.6rem 1rem;
    border:1px solid var(--safelight-dim);
    color:var(--amber);
    font-size:0.75rem;
    letter-spacing:0.1em;
    text-transform:uppercase;
  }

  .reveal{
    opacity:0;
    transform:translateY(14px);
    transition:opacity 0.8s ease, transform 0.8s ease;
  }
  .reveal.in{
    opacity:1;
    transform:translateY(0);
  }
</style>
</head>
<body>

<div class="safelight-glow"></div>

<header>
  <div class="eyebrow">Now developing</div>
  <h1 class="reveal">Do you shoot<br>black &amp; white film?
    <em>Traditional darkroom printing, done by hand.</em>
  </h1>
  <p class="sub reveal">I hand-print silver gelatin enlargements from your black &amp; white negatives — up to 4x5 — the way it's been done since before software existed to do it for you.</p>
</header>

<main>
  <div class="strip">
    <div class="frame reveal" data-index="01" tabindex="0">
      <div class="tag">Format</div>
      <h3>Up to 4x5</h3>
      <p>35mm, medium format, and large format negatives up to 4x5, enlarged and printed in the darkroom.</p>
    </div>
    <div class="frame reveal" data-index="02" tabindex="0">
      <div class="tag">Process</div>
      <h3>Silver gelatin</h3>
      <p>Hand dodge-and-burn on every print. No scanning, no plug-ins — just paper, chemistry, and a loupe.</p>
    </div>
    <div class="frame reveal" data-index="03" tabindex="0">
      <div class="tag">Material</div>
      <h3>Fiber &amp; RC paper</h3>
      <p>Choose the paper stock and finish that suits the negative — glossy, matte, or archival fiber.</p>
    </div>
  </div>

  <section class="detail">
    <div class="reveal">
      <h2>Why a darkroom, still</h2>
      <p>A scan flattens a negative into pixels. A darkroom print holds the tonal range the film actually captured — the grain, the blacks, the way light falls off at the edges of the frame. If you shoot film, this is what it was made to become.</p>
    </div>
    <div class="tray reveal">
      <div class="liquid-line"></div>
      <ul>
        <li><span>Contact sheet</span><span>from your roll</span></li>
        <li><span>Test strips</span><span>dialed in by hand</span></li>
        <li><span>Final prints</span><span>your choice of size</span></li>
        <li><span>Turnaround</span><span>ask for current queue</span></li>
      </ul>
    </div>
  </section>
</main>

<footer>
  <div class="call reveal">Bring in a roll. Let's see what's on it.</div>
  <p class="sub reveal" style="margin-top:0;">Text or email me to get started — based in North Beach.</p>
  <div class="contact-line reveal">
    <span>Jonathan Ramirez</span>
    <a href="tel:17073669401">707-366-9401</a>
    <a href="mailto:ramirezjonathan295@gmail.com">ramirezjonathan295@gmail.com</a>
  </div>
  <div class="fmt reveal">Darkroom printing — 4x5 and under</div>
</footer>

<script>
  const els = document.querySelectorAll('.reveal');
  const io = new IntersectionObserver((entries)=>{
    entries.forEach(e=>{
      if(e.isIntersecting){
        e.target.classList.add('in');
        io.unobserve(e.target);
      }
    });
  }, {threshold:0.15});
  els.forEach(el=>io.observe(el));
</script>

</body>
</html>