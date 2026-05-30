<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>World Clock</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;700;800&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{--bg:#0a0a0a;--surface:#111;--border:#1e1e1e;--text:#f0ede8;--muted:#4a4a4a}
body{background:var(--bg);color:var(--text);font-family:'Syne',sans-serif;min-height:100vh;padding:2rem 1rem}
body::before{content:'';position:fixed;inset:0;background:radial-gradient(ellipse 100% 40% at 50% 0%,#0d1220 0%,transparent 60%);pointer-events:none;z-index:0}
.container{position:relative;z-index:1;width:100%;max-width:1200px;margin:0 auto}
header{display:flex;align-items:center;justify-content:space-between;margin-bottom:2rem;border-bottom:1px solid var(--border);padding-bottom:1.25rem}
.brand{font-size:11px;font-family:'Space Mono',monospace;letter-spacing:0.2em;color:var(--muted);text-transform:uppercase}
.local-block{text-align:right}
.local-label{font-size:10px;font-family:'Space Mono',monospace;color:var(--muted);letter-spacing:0.1em;margin-bottom:2px}
.local-time-val{font-family:'Space Mono',monospace;font-size:15px;font-weight:700;color:var(--text)}

/* ── Lang selector panel ── */
.lang-panel{background:var(--surface);border:0.5px solid var(--border);border-radius:10px;padding:1.25rem 1.4rem;margin-bottom:1.5rem}
.lang-panel-top{display:flex;align-items:center;justify-content:space-between;margin-bottom:1rem}
.lang-panel-title{font-size:10px;letter-spacing:0.18em;text-transform:uppercase;color:var(--muted);font-family:'Space Mono',monospace}
.lang-active-badge{font-size:10px;font-family:'Space Mono',monospace;padding:3px 9px;border-radius:4px;background:#1a1a2e;color:#74b9ff;border:0.5px solid #74b9ff33}
.lang-search-wrap{position:relative;margin-bottom:0.9rem}
.lang-search-wrap input{width:100%;background:#0e0e0e;border:0.5px solid var(--border);border-radius:6px;padding:8px 12px 8px 34px;color:var(--text);font-family:'Space Mono',monospace;font-size:11px;outline:none;transition:border-color 0.2s}
.lang-search-wrap input:focus{border-color:#333}
.lang-search-wrap input::placeholder{color:var(--muted)}
.lang-search-icon{position:absolute;left:11px;top:50%;transform:translateY(-50%);color:var(--muted);font-size:13px;pointer-events:none}
.lang-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(140px,1fr));gap:4px;max-height:220px;overflow-y:auto}
.lang-grid::-webkit-scrollbar{width:3px}
.lang-grid::-webkit-scrollbar-track{background:transparent}
.lang-grid::-webkit-scrollbar-thumb{background:#2a2a2a;border-radius:2px}
.lang-btn{display:flex;align-items:center;gap:7px;padding:7px 10px;border-radius:5px;border:0.5px solid transparent;background:transparent;color:var(--muted);font-family:'Space Mono',monospace;font-size:10px;cursor:pointer;transition:all 0.15s;text-align:left;width:100%}
.lang-btn:hover{background:#1a1a1a;color:var(--text);border-color:var(--border)}
.lang-btn.active{background:#1a1a2e;color:#74b9ff;border-color:#74b9ff44}
.lang-btn .lb-flag{font-size:16px;line-height:1;flex-shrink:0}
.lang-btn .lb-name{font-size:9px;line-height:1.3;flex:1;min-width:0;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.lang-btn .lb-lang{font-size:8px;color:var(--muted);margin-top:1px}
.lang-btn.active .lb-lang{color:#74b9ff88}
.lang-no-results{text-align:center;padding:1.5rem;color:var(--muted);font-family:'Space Mono',monospace;font-size:10px;display:none}

/* ── City search ── */
.search-bar{margin-bottom:2rem;position:relative}
.search-bar input{width:100%;background:#111;border:0.5px solid var(--border);border-radius:8px;padding:10px 16px 10px 38px;color:var(--text);font-family:'Space Mono',monospace;font-size:12px;outline:none;transition:border-color 0.2s}
.search-bar input:focus{border-color:#333}
.search-bar input::placeholder{color:var(--muted)}
.search-icon{position:absolute;left:12px;top:50%;transform:translateY(-50%);color:var(--muted);font-size:14px;pointer-events:none}

/* ── Cards ── */
.region-label{font-size:10px;letter-spacing:0.2em;text-transform:uppercase;color:var(--muted);font-family:'Space Mono',monospace;margin:2rem 0 0.75rem;padding-left:2px}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:2px;margin-bottom:2px}
.card{background:var(--surface);padding:1.25rem 1.2rem 1rem;position:relative;overflow:hidden;transition:background 0.2s;cursor:default}
.card::after{content:'';position:absolute;bottom:0;left:0;right:0;height:2px;background:var(--accent);transform:scaleX(0);transform-origin:left;transition:transform 0.35s cubic-bezier(0.16,1,0.3,1)}
.card:hover{background:#151515}
.card:hover::after{transform:scaleX(1)}
.card-top{display:flex;align-items:center;justify-content:space-between;margin-bottom:0.85rem}
.city-name{font-size:10px;letter-spacing:0.16em;text-transform:uppercase;color:var(--muted);font-weight:700}
.dot{width:5px;height:5px;border-radius:50%;background:var(--accent);animation:pulse 2.5s ease-in-out infinite}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:0.25;transform:scale(0.5)}}
.flag-wrap{display:inline-block;font-size:22px;line-height:1;margin-bottom:0.5rem;animation:waveflag 3s ease-in-out infinite;animation-play-state:paused;transform-origin:left center}
@keyframes waveflag{0%,100%{transform:rotate(0deg) scale(1)}20%{transform:rotate(4deg) scale(1.08)}40%{transform:rotate(-3deg) scale(1.04)}60%{transform:rotate(3deg) scale(1.07)}80%{transform:rotate(-2deg) scale(1.03)}}
.time-display{font-family:'Space Mono',monospace;font-size:clamp(20px,3.5vw,28px);font-weight:700;color:var(--text);letter-spacing:-0.02em;line-height:1;margin-bottom:0.35rem}
.time-display .sec{color:var(--accent);font-size:0.58em;vertical-align:super}
.greg-date{font-size:10px;color:var(--muted);font-family:'Space Mono',monospace;margin-bottom:0.6rem}
.weather-row{display:flex;align-items:center;gap:6px;margin-bottom:0.6rem}
.weather-temp{font-size:15px;font-weight:700;font-family:'Space Mono',monospace;color:var(--accent)}
.weather-icon{font-size:14px;line-height:1}
.weather-desc{font-size:9px;color:var(--muted);font-family:'Space Mono',monospace;letter-spacing:0.04em}
.cal-block{background:color-mix(in srgb,var(--accent) 7%,transparent);border:0.5px solid color-mix(in srgb,var(--accent) 20%,transparent);border-radius:5px;padding:6px 8px;margin-bottom:0.6rem}
.cal-system-name{font-size:8px;letter-spacing:0.14em;text-transform:uppercase;color:var(--accent);font-family:'Space Mono',monospace;margin-bottom:2px;opacity:0.75}
.cal-date-val{font-size:11px;font-weight:700;color:var(--text);font-family:'Space Mono',monospace;letter-spacing:0.01em}
.cal-secondary{font-size:9px;color:var(--muted);font-family:'Space Mono',monospace;margin-top:2px}

/* selected lang highlight */
.card.lang-selected{outline:1.5px solid #74b9ff44}
.card.lang-selected .flag-wrap{animation-play-state:running}

.card-footer{display:flex;align-items:center;justify-content:space-between}
.offset-tag{font-size:9px;font-family:'Space Mono',monospace;color:var(--accent);background:color-mix(in srgb,var(--accent) 10%,transparent);padding:2px 6px;border-radius:3px}
.day-progress{height:1px;background:var(--border);margin-top:0.75rem;overflow:hidden}
.day-progress-inner{height:100%;background:var(--accent);opacity:0.4;transition:width 1s linear}
.no-results{text-align:center;padding:3rem;color:var(--muted);font-family:'Space Mono',monospace;font-size:12px;display:none}
footer{margin-top:2.5rem;text-align:center;font-size:10px;font-family:'Space Mono',monospace;color:var(--muted);letter-spacing:0.1em;display:flex;align-items:center;justify-content:center;gap:7px}
.live-dot{width:5px;height:5px;border-radius:50%;background:#b8ff4a;animation:pulse 2s infinite}
@media(max-width:600px){.grid{grid-template-columns:1fr 1fr}.lang-grid{grid-template-columns:repeat(auto-fill,minmax(120px,1fr))}}
@media(max-width:380px){.grid{grid-template-columns:1fr}}
</style>
</head>
<body>
<div class="container">
  <header>
    <div class="brand">&#x25CF; World Clock</div>
    <div class="local-block">
      <div class="local-label">Your local time</div>
      <div class="local-time-val" id="local-now">--:--:--</div>
    </div>
  </header>

  <!-- Language Selector -->
  <div class="lang-panel">
    <div class="lang-panel-top">
      <div class="lang-panel-title">🌐 &nbsp;Select your country language</div>
      <div class="lang-active-badge" id="lang-badge">English (Default)</div>
    </div>
    <div class="lang-search-wrap">
      <span class="lang-search-icon">⌕</span>
      <input type="text" id="lang-search" placeholder="Search country or language..." autocomplete="off">
    </div>
    <div class="lang-grid" id="lang-grid"></div>
    <div class="lang-no-results" id="lang-no-results">No match found</div>
  </div>

  <!-- City Search -->
  <div class="search-bar">
    <span class="search-icon">⌕</span>
    <input type="text" id="search" placeholder="Search city or country..." autocomplete="off">
  </div>

  <div id="regions-container"></div>
  <div class="no-results" id="no-results">No cities found</div>
  <footer><div class="live-dot"></div>Live &mdash; weather refreshes every 15 min</footer>
</div>

<script>
/* ── Calendar converters ── */
function toJalali(gy,gm,gd){
  const g=[0,31,59,90,120,151,181,212,243,273,304,334];
  let y2=gm>2?gy+1:gy,days=355666+(365*gy)+Math.floor((y2+3)/4)-Math.floor((y2+99)/100)+Math.floor((y2+399)/400)+gd+g[gm-1];
  let jy=-1595+(33*Math.floor(days/12053));days%=12053;jy+=4*Math.floor(days/1461);days%=1461;
  if(days>365){jy+=Math.floor((days-1)/365);days=(days-1)%365;}
  let jm=days<186?1+Math.floor(days/31):7+Math.floor((days-186)/30);
  let jd=1+(days<186?days%31:(days-186)%30);return{jy,jm,jd};
}
const jM=['فروردین','اردیبهشت','خرداد','تیر','مرداد','شهریور','مهر','آبان','آذر','دی','بهمن','اسفند'];
function toJapaneseEra(d){if(d>=new Date('2019-05-01'))return{kanji:'令和',name:'Reiwa',year:d.getFullYear()-2018};if(d>=new Date('1989-01-08'))return{kanji:'平成',name:'Heisei',year:d.getFullYear()-1988};return{kanji:'昭和',name:'Showa',year:d.getFullYear()-1925};}
function toHijri(date){const JD=Math.floor(date.getTime()/86400000+2440587.5);let l=JD-1948440+10632,n=Math.floor((l-1)/10631);l=l-10631*n+354;const j=Math.floor((10985-l)/5316)*Math.floor(50*l/17719)+Math.floor(l/5670)*Math.floor(43*l/15238);l=l-Math.floor((30-j)/15)*Math.floor(17719*j/50)-Math.floor(j/16)*Math.floor(15238*j/43)+29;const mo=Math.floor(24*l/709),day=l-Math.floor(709*mo/24),year=30*n+j-30;const mn=['Muharram','Safar',"Rabi' I","Rabi' II",'Jumada I','Jumada II','Rajab',"Sha'ban",'Ramadan','Shawwal',"Dhu al-Qi'dah",'Dhu al-Hijjah'];return{day,month:mo,year,monthName:mn[mo-1]};}
function toEthiopian(date){const gY=date.getFullYear(),gM=date.getMonth()+1,gD=date.getDate();const JDN=Math.floor(365.25*(gY+4716))+Math.floor(30.6001*(gM+1))+gD-1524;const r=(JDN-1723856)%1461;const n=(r%365)+365*(Math.floor(r/365)==4?1:0);const eY=4*Math.floor((JDN-1723856)/1461)+Math.floor(r/365)-(Math.floor(r/365)==4?1:0);const eM=Math.floor(n/30)+1,eD=n%30+1;const mn=['Meskerem','Tikimt','Hidar','Tahsas','Tir','Yekatit','Megabit','Miyazya','Ginbot','Sene','Hamle','Nehase','Pagume'];return{year:eY,month:eM,day:eD,monthName:mn[eM-1]||'Pagume'};}
function toThaiBuddhist(d){return{year:d.getFullYear()+543};}
function wIcon(c){if(c===0)return'☀️';if(c<=2)return'🌤️';if(c<=3)return'☁️';if(c<=48)return'🌫️';if(c<=57)return'🌦️';if(c<=67)return'🌧️';if(c<=77)return'❄️';if(c<=82)return'🌦️';if(c<=99)return'⛈️';return'🌡️';}
function wDesc(c){if(c===0)return'Clear';if(c<=2)return'Partly cloudy';if(c<=3)return'Overcast';if(c<=48)return'Foggy';if(c<=57)return'Drizzle';if(c<=67)return'Rainy';if(c<=77)return'Snowy';if(c<=82)return'Showers';if(c<=99)return'Thunderstorm';return'N/A';}

/* ── Language options (country → locale code + display name) ── */
const langOptions=[
  {id:'en',    flag:'🇺🇸', country:'United States', lang:'English',      locale:'en-US'},
  {id:'fa',    flag:'🇮🇷', country:'Iran',           lang:'فارسی',        locale:'fa-IR'},
  {id:'ar',    flag:'🇸🇦', country:'Saudi Arabia',   lang:'العربية',      locale:'ar-SA'},
  {id:'ar_ae', flag:'🇦🇪', country:'UAE',            lang:'العربية (إمارات)',locale:'ar-AE'},
  {id:'ar_eg', flag:'🇪🇬', country:'Egypt',          lang:'العربية (مصر)',locale:'ar-EG'},
  {id:'tr',    flag:'🇹🇷', country:'Turkey',         lang:'Türkçe',       locale:'tr-TR'},
  {id:'ur',    flag:'🇵🇰', country:'Pakistan',       lang:'اردو',         locale:'ur-PK'},
  {id:'hi',    flag:'🇮🇳', country:'India',          lang:'हिन्दी',       locale:'hi-IN'},
  {id:'bn',    flag:'🇧🇩', country:'Bangladesh',     lang:'বাংলা',        locale:'bn-BD'},
  {id:'th',    flag:'🇹🇭', country:'Thailand',       lang:'ไทย',          locale:'th-TH'},
  {id:'zh',    flag:'🇨🇳', country:'China',          lang:'中文',         locale:'zh-CN'},
  {id:'ja',    flag:'🇯🇵', country:'Japan',          lang:'日本語',       locale:'ja-JP'},
  {id:'ko',    flag:'🇰🇷', country:'South Korea',    lang:'한국어',       locale:'ko-KR'},
  {id:'id',    flag:'🇮🇩', country:'Indonesia',      lang:'Bahasa Indonesia',locale:'id-ID'},
  {id:'tl',    flag:'🇵🇭', country:'Philippines',    lang:'Filipino',     locale:'fil-PH'},
  {id:'ms',    flag:'🇸🇬', country:'Singapore',      lang:'English (SG)', locale:'en-SG'},
  {id:'gb',    flag:'🇬🇧', country:'United Kingdom', lang:'English (UK)', locale:'en-GB'},
  {id:'fr',    flag:'🇫🇷', country:'France',         lang:'Français',     locale:'fr-FR'},
  {id:'de',    flag:'🇩🇪', country:'Germany',        lang:'Deutsch',      locale:'de-DE'},
  {id:'es',    flag:'🇪🇸', country:'Spain',          lang:'Español',      locale:'es-ES'},
  {id:'it',    flag:'🇮🇹', country:'Italy',          lang:'Italiano',     locale:'it-IT'},
  {id:'nl',    flag:'🇳🇱', country:'Netherlands',    lang:'Nederlands',   locale:'nl-NL'},
  {id:'ru',    flag:'🇷🇺', country:'Russia',         lang:'Русский',      locale:'ru-RU'},
  {id:'sv',    flag:'🇸🇪', country:'Sweden',         lang:'Svenska',      locale:'sv-SE'},
  {id:'pl',    flag:'🇵🇱', country:'Poland',         lang:'Polski',       locale:'pl-PL'},
  {id:'el',    flag:'🇬🇷', country:'Greece',         lang:'Ελληνικά',     locale:'el-GR'},
  {id:'ca',    flag:'🇨🇦', country:'Canada',         lang:'English (CA)', locale:'en-CA'},
  {id:'mx',    flag:'🇲🇽', country:'Mexico',         lang:'Español (MX)', locale:'es-MX'},
  {id:'co',    flag:'🇨🇴', country:'Colombia',       lang:'Español (CO)', locale:'es-CO'},
  {id:'pt',    flag:'🇧🇷', country:'Brazil',         lang:'Português',    locale:'pt-BR'},
  {id:'es_ar', flag:'🇦🇷', country:'Argentina',      lang:'Español (AR)', locale:'es-AR'},
  {id:'es_cl', flag:'🇨🇱', country:'Chile',          lang:'Español (CL)', locale:'es-CL'},
  {id:'sw',    flag:'🇰🇪', country:'Kenya',          lang:'English (KE)', locale:'en-KE'},
  {id:'am',    flag:'🇪🇹', country:'Ethiopia',       lang:'አማርኛ',         locale:'am-ET'},
  {id:'zu',    flag:'🇿🇦', country:'South Africa',   lang:'English (ZA)', locale:'en-ZA'},
  {id:'en_au', flag:'🇦🇺', country:'Australia',      lang:'English (AU)', locale:'en-AU'},
  {id:'en_nz', flag:'🇳🇿', country:'New Zealand',    lang:'English (NZ)', locale:'en-NZ'},
];

let selectedLocale='en-US';
let selectedLangId='en';

/* ── City data ── */
const regions=[
  {name:'Middle East & Asia',cities:[
    {id:'tehran',    label:'Tehran',       country:'Iran',         flag:'🇮🇷', zone:'Asia/Tehran',         accent:'#e8603c', lat:35.69, lon:51.39,
     calFn:d=>{const j=toJalali(d.getFullYear(),d.getMonth()+1,d.getDate());return{sys:'Solar Hijri · شمسی',pri:`${j.jd} ${jM[j.jm-1]} ${j.jy}`,sec:`سال ${j.jy}`};}},
    {id:'dubai',     label:'Dubai',        country:'UAE',          flag:'🇦🇪', zone:'Asia/Dubai',           accent:'#9e4aff', lat:25.20, lon:55.27,
     calFn:d=>{const h=toHijri(d);return{sys:'Islamic Hijri · هجري',pri:`${h.day} ${h.monthName} ${h.year} AH`,sec:`Gregorian: ${d.toLocaleDateString('en-US',{day:'numeric',month:'short',year:'numeric'})}`};}},
    {id:'riyadh',    label:'Riyadh',       country:'Saudi Arabia', flag:'🇸🇦', zone:'Asia/Riyadh',          accent:'#3dba6e', lat:24.68, lon:46.72,
     calFn:d=>{const h=toHijri(d);return{sys:'Islamic Hijri · هجري',pri:`${h.day} ${h.monthName} ${h.year} AH`,sec:`Gregorian: ${d.toLocaleDateString('en-US',{day:'numeric',month:'short',year:'numeric'})}`};}},
    {id:'istanbul',  label:'Istanbul',     country:'Turkey',       flag:'🇹🇷', zone:'Europe/Istanbul',      accent:'#ff6b6b', lat:41.01, lon:28.95,
     calFn:d=>({sys:'Gregorian · Miladi',pri:d.toLocaleDateString('tr-TR',{weekday:'long',day:'numeric',month:'long'}),sec:`${d.getFullYear()}`})},
    {id:'karachi',   label:'Karachi',      country:'Pakistan',     flag:'🇵🇰', zone:'Asia/Karachi',         accent:'#5cdb95', lat:24.86, lon:67.01,
     calFn:d=>{const h=toHijri(d);return{sys:'Islamic Hijri',pri:`${h.day} ${h.monthName} ${h.year} AH`,sec:d.toLocaleDateString('ur-PK',{day:'numeric',month:'long',year:'numeric'})};}},
    {id:'delhi',     label:'New Delhi',    country:'India',        flag:'🇮🇳', zone:'Asia/Kolkata',         accent:'#ff9f43', lat:28.61, lon:77.20,
     calFn:d=>({sys:'Gregorian (official)',pri:d.toLocaleDateString('hi-IN',{weekday:'long',day:'numeric',month:'long'}),sec:`Saka ${d.getFullYear()-78}`})},
    {id:'dhaka',     label:'Dhaka',        country:'Bangladesh',   flag:'🇧🇩', zone:'Asia/Dhaka',           accent:'#ff4757', lat:23.81, lon:90.41,
     calFn:d=>({sys:'Bengali Calendar',pri:d.toLocaleDateString('bn-BD',{day:'numeric',month:'long',year:'numeric'}),sec:`Gregorian ${d.getFullYear()}`})},
    {id:'bangkok',   label:'Bangkok',      country:'Thailand',     flag:'🇹🇭', zone:'Asia/Bangkok',         accent:'#ffd32a', lat:13.75, lon:100.52,
     calFn:d=>{const t=toThaiBuddhist(d);return{sys:'Thai Buddhist · พุทธศักราช',pri:d.toLocaleDateString('th-TH',{weekday:'long',day:'numeric',month:'long'}),sec:`พ.ศ. ${t.year}`};}},
    {id:'beijing',   label:'Beijing',      country:'China',        flag:'🇨🇳', zone:'Asia/Shanghai',        accent:'#ff4757', lat:39.91, lon:116.39,
     calFn:d=>({sys:'Gregorian (official)',pri:d.toLocaleDateString('zh-CN',{year:'numeric',month:'long',day:'numeric'}),sec:`公元 ${d.getFullYear()} 年`})},
    {id:'tokyo',     label:'Tokyo',        country:'Japan',        flag:'🇯🇵', zone:'Asia/Tokyo',           accent:'#ff4a9e', lat:35.68, lon:139.69,
     calFn:d=>{const e=toJapaneseEra(d);return{sys:`Imperial · ${e.kanji}`,pri:`${e.kanji}${e.year}年 ${d.getMonth()+1}月 ${d.getDate()}日`,sec:`${e.name} ${e.year} (${d.getFullYear()})`};}},
    {id:'seoul',     label:'Seoul',        country:'South Korea',  flag:'🇰🇷', zone:'Asia/Seoul',           accent:'#5352ed', lat:37.57, lon:126.98,
     calFn:d=>({sys:'Gregorian · 단기',pri:d.toLocaleDateString('ko-KR',{year:'numeric',month:'long',day:'numeric'}),sec:`단기 ${d.getFullYear()+2333}`})},
    {id:'singapore', label:'Singapore',   country:'Singapore',    flag:'🇸🇬', zone:'Asia/Singapore',       accent:'#ff4757', lat:1.35, lon:103.82,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-SG',{weekday:'long',day:'numeric',month:'long',year:'numeric'}),sec:`Year ${d.getFullYear()}`})},
    {id:'jakarta',   label:'Jakarta',      country:'Indonesia',    flag:'🇮🇩', zone:'Asia/Jakarta',         accent:'#ff6348', lat:-6.21, lon:106.85,
     calFn:d=>({sys:'Gregorian · Masehi',pri:d.toLocaleDateString('id-ID',{weekday:'long',day:'numeric',month:'long'}),sec:`Tahun ${d.getFullYear()}`})},
    {id:'manila',    label:'Manila',       country:'Philippines',  flag:'🇵🇭', zone:'Asia/Manila',          accent:'#ffa502', lat:14.60, lon:120.98,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-PH',{weekday:'long',month:'long',day:'numeric'}),sec:`Year ${d.getFullYear()}`})},
  ]},
  {name:'Europe',cities:[
    {id:'london',    label:'London',       country:'UK',           flag:'🇬🇧', zone:'Europe/London',        accent:'#b8ff4a', lat:51.51, lon:-0.13,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-GB',{weekday:'long',day:'numeric',month:'long'}),sec:`Anno Domini ${d.getFullYear()}`})},
    {id:'paris',     label:'Paris',        country:'France',       flag:'🇫🇷', zone:'Europe/Paris',         accent:'#74b9ff', lat:48.86, lon:2.35,
     calFn:d=>({sys:'Grégorien',pri:d.toLocaleDateString('fr-FR',{weekday:'long',day:'numeric',month:'long'}),sec:`An ${d.getFullYear()}`})},
    {id:'berlin',    label:'Berlin',       country:'Germany',      flag:'🇩🇪', zone:'Europe/Berlin',        accent:'#ffcc4a', lat:52.52, lon:13.40,
     calFn:d=>({sys:'Gregorianisch',pri:d.toLocaleDateString('de-DE',{weekday:'long',day:'numeric',month:'long'}),sec:`Jahr ${d.getFullYear()}`})},
    {id:'madrid',    label:'Madrid',       country:'Spain',        flag:'🇪🇸', zone:'Europe/Madrid',        accent:'#fd79a8', lat:40.42, lon:-3.70,
     calFn:d=>({sys:'Gregoriano',pri:d.toLocaleDateString('es-ES',{weekday:'long',day:'numeric',month:'long'}),sec:`Año ${d.getFullYear()}`})},
    {id:'rome',      label:'Rome',         country:'Italy',        flag:'🇮🇹', zone:'Europe/Rome',          accent:'#55efc4', lat:41.90, lon:12.50,
     calFn:d=>({sys:'Gregoriano',pri:d.toLocaleDateString('it-IT',{weekday:'long',day:'numeric',month:'long'}),sec:`Anno ${d.getFullYear()}`})},
    {id:'amsterdam', label:'Amsterdam',   country:'Netherlands',  flag:'🇳🇱', zone:'Europe/Amsterdam',     accent:'#ff7675', lat:52.37, lon:4.90,
     calFn:d=>({sys:'Gregoriaans',pri:d.toLocaleDateString('nl-NL',{weekday:'long',day:'numeric',month:'long'}),sec:`Jaar ${d.getFullYear()}`})},
    {id:'moscow',    label:'Moscow',       country:'Russia',       flag:'🇷🇺', zone:'Europe/Moscow',        accent:'#a29bfe', lat:55.75, lon:37.62,
     calFn:d=>({sys:'Gregorian · Григорианский',pri:d.toLocaleDateString('ru-RU',{weekday:'long',day:'numeric',month:'long'}),sec:`${d.getFullYear()} г.`})},
    {id:'stockholm', label:'Stockholm',   country:'Sweden',       flag:'🇸🇪', zone:'Europe/Stockholm',     accent:'#fdcb6e', lat:59.33, lon:18.07,
     calFn:d=>({sys:'Gregoriansk',pri:d.toLocaleDateString('sv-SE',{weekday:'long',day:'numeric',month:'long'}),sec:`År ${d.getFullYear()}`})},
    {id:'warsaw',    label:'Warsaw',       country:'Poland',       flag:'🇵🇱', zone:'Europe/Warsaw',        accent:'#e17055', lat:52.23, lon:21.01,
     calFn:d=>({sys:'Gregoriański',pri:d.toLocaleDateString('pl-PL',{weekday:'long',day:'numeric',month:'long'}),sec:`Rok ${d.getFullYear()}`})},
    {id:'zurich',    label:'Zurich',       country:'Switzerland',  flag:'🇨🇭', zone:'Europe/Zurich',        accent:'#d63031', lat:47.38, lon:8.54,
     calFn:d=>({sys:'Gregorianisch',pri:d.toLocaleDateString('de-CH',{weekday:'long',day:'numeric',month:'long'}),sec:`Jahr ${d.getFullYear()}`})},
    {id:'athens',    label:'Athens',       country:'Greece',       flag:'🇬🇷', zone:'Europe/Athens',        accent:'#0984e3', lat:37.98, lon:23.73,
     calFn:d=>({sys:'Γρηγοριανό',pri:d.toLocaleDateString('el-GR',{weekday:'long',day:'numeric',month:'long'}),sec:`Έτος ${d.getFullYear()}`})},
  ]}, {name:'Americas',cities:[
    {id:'ny',          label:'New York',    country:'USA',          flag:'🇺🇸', zone:'America/New_York',     accent:'#4a9eff', lat:40.71, lon:-74.01,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-US',{weekday:'long',month:'long',day:'numeric'}),sec:`Year ${d.getFullYear()}`})},
    {id:'la',          label:'Los Angeles', country:'USA',          flag:'🇺🇸', zone:'America/Los_Angeles',  accent:'#fdcb6e', lat:34.05, lon:-118.24,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-US',{weekday:'long',month:'long',day:'numeric'}),sec:`Year ${d.getFullYear()}`})},
    {id:'toronto',     label:'Toronto',     country:'Canada',       flag:'🇨🇦', zone:'America/Toronto',      accent:'#ff7675', lat:43.65, lon:-79.38,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-CA',{weekday:'long',month:'long',day:'numeric'}),sec:`Year ${d.getFullYear()}`})},
    {id:'mexico',      label:'Mexico City', country:'Mexico',       flag:'🇲🇽', zone:'America/Mexico_City',  accent:'#55efc4', lat:19.43, lon:-99.13,
     calFn:d=>({sys:'Gregoriano',pri:d.toLocaleDateString('es-MX',{weekday:'long',day:'numeric',month:'long'}),sec:`Año ${d.getFullYear()}`})},
    {id:'bogota',      label:'Bogotá',      country:'Colombia',     flag:'🇨🇴', zone:'America/Bogota',       accent:'#ffeaa7', lat:4.71, lon:-74.07,
     calFn:d=>({sys:'Gregoriano',pri:d.toLocaleDateString('es-CO',{weekday:'long',day:'numeric',month:'long'}),sec:`Año ${d.getFullYear()}`})},
    {id:'lima',        label:'Lima',        country:'Peru',         flag:'🇵🇪', zone:'America/Lima',         accent:'#e17055', lat:-12.04, lon:-77.03,
     calFn:d=>({sys:'Gregoriano',pri:d.toLocaleDateString('es-PE',{weekday:'long',day:'numeric',month:'long'}),sec:`Año ${d.getFullYear()}`})},
    {id:'saopaulo',    label:'São Paulo',   country:'Brazil',       flag:'🇧🇷', zone:'America/Sao_Paulo',    accent:'#6c5ce7', lat:-23.55, lon:-46.63,
     calFn:d=>({sys:'Gregoriano',pri:d.toLocaleDateString('pt-BR',{weekday:'long',day:'numeric',month:'long'}),sec:`Ano ${d.getFullYear()}`})},
    {id:'buenosaires', label:'Buenos Aires',country:'Argentina',    flag:'🇦🇷', zone:'America/Argentina/Buenos_Aires',accent:'#74b9ff',lat:-34.60,lon:-58.38,
     calFn:d=>({sys:'Gregoriano',pri:d.toLocaleDateString('es-AR',{weekday:'long',day:'numeric',month:'long'}),sec:`Año ${d.getFullYear()}`})},
    {id:'santiago',    label:'Santiago',    country:'Chile',        flag:'🇨🇱', zone:'America/Santiago',     accent:'#fd79a8', lat:-33.46, lon:-70.65,
     calFn:d=>({sys:'Gregoriano',pri:d.toLocaleDateString('es-CL',{weekday:'long',day:'numeric',month:'long'}),sec:`Año ${d.getFullYear()}`})},
  ]},
  {name:'Africa & Oceania',cities:[
    {id:'cairo',         label:'Cairo',        country:'Egypt',       flag:'🇪🇬', zone:'Africa/Cairo',         accent:'#fdcb6e', lat:30.04, lon:31.24,
     calFn:d=>{const h=toHijri(d);return{sys:'Islamic Hijri · هجري',pri:`${h.day} ${h.monthName} ${h.year} AH`,sec:d.toLocaleDateString('ar-EG',{day:'numeric',month:'long',year:'numeric'})};}},
    {id:'lagos',         label:'Lagos',        country:'Nigeria',     flag:'🇳🇬', zone:'Africa/Lagos',         accent:'#55efc4', lat:6.52, lon:3.38,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-NG',{weekday:'long',month:'long',day:'numeric'}),sec:`Year ${d.getFullYear()}`})},
    {id:'nairobi',       label:'Nairobi',      country:'Kenya',       flag:'🇰🇪', zone:'Africa/Nairobi',       accent:'#e17055', lat:-1.29, lon:36.82,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-KE',{weekday:'long',month:'long',day:'numeric'}),sec:`Year ${d.getFullYear()}`})},
    {id:'addis',         label:'Addis Ababa',  country:'Ethiopia',    flag:'🇪🇹', zone:'Africa/Addis_Ababa',   accent:'#ff9f43', lat:9.03, lon:38.74,
     calFn:d=>{const e=toEthiopian(d);return{sys:'Ethiopian Calendar · ዓ.ም.',pri:`${e.day} ${e.monthName} ${e.year}`,sec:`Gregorian: ${d.toLocaleDateString('en-US',{day:'numeric',month:'short',year:'numeric'})}`};}},
    {id:'johannesburg',  label:'Johannesburg', country:'S. Africa',   flag:'🇿🇦', zone:'Africa/Johannesburg',  accent:'#74b9ff', lat:-26.20, lon:28.04,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-ZA',{weekday:'long',day:'numeric',month:'long'}),sec:`Year ${d.getFullYear()}`})},
    {id:'sydney',        label:'Sydney',       country:'Australia',   flag:'🇦🇺', zone:'Australia/Sydney',     accent:'#ff6b6b', lat:-33.87, lon:151.21,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-AU',{weekday:'long',day:'numeric',month:'long'}),sec:`Year ${d.getFullYear()}`})},
    {id:'auckland',      label:'Auckland',     country:'New Zealand', flag:'🇳🇿', zone:'Pacific/Auckland',     accent:'#a29bfe', lat:-36.86, lon:174.77,
     calFn:d=>({sys:'Gregorian Calendar',pri:d.toLocaleDateString('en-NZ',{weekday:'long',day:'numeric',month:'long'}),sec:`Year ${d.getFullYear()}`})},
  ]},
];
const allCities=regions.flatMap(r=>r.cities);
function getOffset(zone){const now=new Date(),z=new Date(now.toLocaleString('en-US',{timeZone:zone})),u=new Date(now.toLocaleString('en-US',{timeZone:'UTC'}));const diff=(z-u)/3600000,sign=diff>=0?'+':'-',h=Math.floor(Math.abs(diff)),m=Math.round((Math.abs(diff)-h)*60);return`UTC${sign}${h}${m?`:${String(m).padStart(2,'0')}`:''}`;}
function pad(n){return String(n).padStart(2,'0');}

/* ── Build language selector ── */
function buildLangGrid(filter=''){
  const grid=document.getElementById('lang-grid');
  const noRes=document.getElementById('lang-no-results');
  const q=filter.toLowerCase().trim();
  const filtered=langOptions.filter(l=>!q||l.country.toLowerCase().includes(q)||l.lang.toLowerCase().includes(q));
  grid.innerHTML='';
  if(!filtered.length){noRes.style.display='block';return;}
  noRes.style.display='none';
  filtered.forEach(l=>{
    const btn=document.createElement('button');
    btn.className='lang-btn'+(l.id===selectedLangId?' active':'');
    btn.dataset.id=l.id;
    btn.innerHTML=`<span class="lb-flag">${l.flag}</span><div><div class="lb-name">${l.country}</div><div class="lb-lang">${l.lang}</div></div>`;
    btn.addEventListener('click',()=>selectLang(l));
    grid.appendChild(btn);
  });
}function selectLang(l){
  selectedLocale=l.locale;
  selectedLangId=l.id;
  document.getElementById('lang-badge').textContent=`${l.flag} ${l.lang}`;
  document.querySelectorAll('.lang-btn').forEach(b=>b.classList.toggle('active',b.dataset.id===l.id));
  // highlight matching card
  allCities.forEach(c=>{
    const card=document.getElementById('card-'+c.id);
    if(card)card.classList.remove('lang-selected');
  });
  // find city matching this lang country
  const matchCity=allCities.find(c=>c.country===l.country||c.country.includes(l.country.split(' ')[0]));
  if(matchCity){
    const card=document.getElementById('card-'+matchCity.id);
    if(card){card.classList.add('lang-selected');setTimeout(()=>card.scrollIntoView({behavior:'smooth',block:'center'}),100);}
  }
  // re-render dates in selected locale
  update();
}

document.getElementById('lang-search').addEventListener('input',function(){buildLangGrid(this.value);});

/* ── Build city cards ── */
function buildAll(){
  const container=document.getElementById('regions-container');
  regions.forEach(region=>{
    const sec=document.createElement('div');
    sec.className='region-section';
    sec.dataset.region=region.name;
    sec.innerHTML=`<div class="region-label">${region.name}</div>`;
    const grid=document.createElement('div');grid.className='grid';
    region.cities.forEach(c=>{
      grid.innerHTML+=`
        <div class="card" id="card-${c.id}" style="--accent:${c.accent}">
          <div class="card-top"><div class="city-name">${c.country}</div><div class="dot"></div></div>
          <div class="flag-wrap" id="flag-${c.id}">${c.flag}</div>
          <div style="font-size:12px;font-weight:700;color:var(--text);margin-bottom:0.5rem">${c.label}</div>
          <div class="time-display" id="t-${c.id}">--:--<span class="sec" id="s-${c.id}">00</span></div>
          <div class="greg-date" id="gd-${c.id}">---</div>
          <div class="weather-row">
            <span class="weather-icon" id="wi-${c.id}">🌡️</span>
            <span class="weather-temp" id="wt-${c.id}">--°C</span>
            <span class="weather-desc" id="wd-${c.id}">...</span>
          </div>
          <div class="cal-block">
            <div class="cal-system-name" id="csn-${c.id}"></div>
            <div class="cal-date-val" id="cdv-${c.id}"></div>
            <div class="cal-secondary" id="cds-${c.id}"></div>
          </div>
          <div class="card-footer"><div class="offset-tag">${getOffset(c.zone)}</div></div>
          <div class="day-progress"><div class="day-progress-inner" id="bar-${c.id}" style="width:0%"></div></div>
        </div>`;
    });
    sec.appendChild(grid);container.appendChild(sec);
  });
}/* ── Weather ── */
async function fetchWeather(){
  const lats=allCities.map(c=>c.lat).join(','),lons=allCities.map(c=>c.lon).join(',');
  try{
    const url=`https://api.open-meteo.com/v1/forecast?latitude=${lats}&longitude=${lons}&current=temperature_2m,weather_code&timezone=auto`;
    const res=await fetch(url);const data=await res.json();const arr=Array.isArray(data)?data:[data];
    arr.forEach((r,i)=>{const c=allCities[i];if(!c)return;const temp=Math.round(r.current.temperature_2m),wmo=r.current.weather_code;
      const wt=document.getElementById('wt-'+c.id),wi=document.getElementById('wi-'+c.id),wd=document.getElementById('wd-'+c.id);
      if(wt)wt.textContent=`${temp}°C`;if(wi)wi.textContent=wIcon(wmo);if(wd)wd.textContent=wDesc(wmo);});
  }catch(e){allCities.forEach(c=>{const el=document.getElementById('wd-'+c.id);if(el)el.textContent='N/A';});}
}

/* ── Clock update (uses selectedLocale for greg date display) ── */
function update(){
  const now=new Date();
  document.getElementById('local-now').textContent=now.toLocaleTimeString(selectedLocale,{hour12:false});
  allCities.forEach(c=>{
    const d=new Date(now.toLocaleString('en-US',{timeZone:c.zone}));
    const h=pad(d.getHours()),m=pad(d.getMinutes()),s=pad(d.getSeconds());
    const pct=((d.getHours()*3600+d.getMinutes()*60+d.getSeconds())/86400*100).toFixed(3);
    const t=document.getElementById('t-'+c.id);
    if(t){t.childNodes[0].nodeValue=`${h}:${m}`;document.getElementById('s-'+c.id).textContent=s;}
    const gd=document.getElementById('gd-'+c.id);
    if(gd)gd.textContent=d.toLocaleDateString(selectedLocale,{timeZone:c.zone,weekday:'short',month:'short',day:'numeric',year:'numeric'});
    const cal=c.calFn(d);
    const csn=document.getElementById('csn-'+c.id);
    if(csn){csn.textContent=cal.sys;document.getElementById('cdv-'+c.id).textContent=cal.pri;document.getElementById('cds-'+c.id).textContent=cal.sec||'';}
    const bar=document.getElementById('bar-'+c.id);
    if(bar)bar.style.width=pct+'%';
  });
}
/* ── City search ── */
document.getElementById('search').addEventListener('input',function(){
  const q=this.value.toLowerCase().trim();let anyVisible=false;
  document.querySelectorAll('.region-section').forEach(sec=>{
    let secVisible=false;
    sec.querySelectorAll('.card').forEach(card=>{
      const id=card.id.replace('card-','');const city=allCities.find(c=>c.id===id);if(!city)return;
      const match=!q||city.label.toLowerCase().includes(q)||city.country.toLowerCase().includes(q);
      card.style.display=match?'':'none';if(match){secVisible=true;anyVisible=true;}
    });
    sec.style.display=secVisible?'':'none';
  });
  document.getElementById('no-results').style.display=anyVisible?'none':'block';
});

/* ── Flag hover ── */
document.addEventListener('mouseover',e=>{const card=e.target.closest('.card');if(card){const f=card.querySelector('.flag-wrap');if(f)f.style.animationPlayState='running';}});
document.addEventListener('mouseout',e=>{const card=e.target.closest('.card');if(card){const f=card.querySelector('.flag-wrap');if(f)f.style.animationPlayState='paused';}});

buildAll();
buildLangGrid();
update();
setInterval(update,1000);
fetchWeather();
setInterval(fetchWeather,15*60*1000);
</script>
</body>
</html>
