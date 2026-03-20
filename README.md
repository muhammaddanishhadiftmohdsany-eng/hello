<!DOCTYPE html>
<html lang="ms">
<head>
    <meta charset="UTF-8">
    <title>K-Pop Line Distribution - 3:5 Frame Format</title>
    <style>
        :root {
            --bar-color-JAENA: #ff007f;    /* Pink */
            --bar-color-CHEONGHO: #0077ff;  /* Biru */
            --bar-color-ARIN: #ff0000;      /* Merah */
            --bar-color-ROTZU: #ffcc00;    /* Kuning */
            --bar-color-RIS: #00ffcc;      /* Turquoise */
            --bar-color-SEOLA: #a020f0;    /* Ungu */
            --bar-color-RAEJIN: #ff8c00;   /* Jingga */
            --bar-color-BOTH: #ffffff;
            --frame-glow: rgba(255,255,255,0.5); /* Default color */
        }

        body, html { 
            margin: 0; padding: 0; background: #1a1a1a; 
            font-family: 'Arial', sans-serif; height: 100vh;
            display: flex; flex-direction: column; overflow: hidden;
            justify-content: center; align-items: center;
        }

        .video-container {
            width: 100vw;
            height: 56.25vw;
            max-height: calc(100vh - 120px);
            max-width: calc((100vh - 120px) * 1.777); 
            background: white; position: relative;
            overflow: hidden; display: flex;
            box-shadow: 0 0 30px rgba(0,0,0,0.5);
        }

        .left-panel {
            width: 37%; min-width: 37%; 
            height: 100%; background: #000;
            position: relative; display: flex; 
            flex-direction: column; justify-content: center; 
            align-items: center; z-index: 10; flex-shrink: 0;
            border-right: 2px solid rgba(255,255,255,0.1);
        }

        .frame-3-5 {
            position: relative;
            width: 90%; 
            max-height: 90%;
            aspect-ratio: 3/5;
            border: 5px solid var(--frame-glow);
            padding: 10px;
            display: flex;
            flex-direction: column;
            justify-content: flex-end;
            padding-bottom: 40px;
            transition: border-color 0.3s ease, box-shadow 0.3s ease;
            box-shadow: 0 0 15px var(--frame-glow);
            box-sizing: border-box; 
        }

        .frame-3-5.glow-active {
            box-shadow: 0 0 40px var(--frame-glow), inset 0 0 20px var(--frame-glow);
        }

        #bg-video { position: absolute; top:0; left:0; width:100%; height:100%; object-fit: cover; opacity: 0.6; z-index: 1; }

        .rank-container { position: relative; z-index: 5; width: 100%; height: 90%; }

        .member-row {
            position: absolute; width: 100%; transition: all 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            height: 70px; 
            display: flex; align-items: center;
            z-index: 6; 
        }

        .member-row.finished {
            opacity: 0.5;
            filter: grayscale(1);
        }

        /* --- PERUBAHAN PROFILE IMG: LEBIH SMOOTH & POP UP BESAR --- */
        .profile-img {
            width: 55px; height: 55px; 
            background: transparent;
            z-index: 7; flex-shrink: 0;
            background-size: contain; background-repeat: no-repeat; background-position: center bottom;
            /* Cubic-bezier ditingkatkan untuk kesan bounce yang lebih organik */
            transition: all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
            border-radius: 50%;
            border: 2px solid transparent;
            transform: scale(1) translateX(0);
        }

        .active-member .profile-img { 
            /* Memberi kesan terkeluar ke kiri sedikit dan membesar */
            transform: scale(1.45) translateX(-8px) translateY(-5px);
            border: 3px solid var(--current-color);
            box-shadow: 0 0 20px var(--current-color), inset 0 0 10px var(--current-color);
            filter: drop-shadow(0 0 12px var(--current-color));
            z-index: 20;
        }

        /* -------------------------------------------------- */

        .active-member {
            z-index: 100 !important;
        }

        .bar-outer { flex-grow: 1; margin-left: 15px; position: relative; display: flex; flex-direction: column; justify-content: center; }
        
        .member-name { 
            font-size: 1.1rem; 
            font-weight: 900; color: white; 
            text-transform: uppercase;
            text-shadow: 0 0 8px var(--current-color); 
            margin-bottom: 4px;
            transition: all 0.4s ease;
            text-align: left;
        }

        .active-member .member-name { 
            text-shadow: 0 0 15px var(--current-color), 0 0 30px var(--current-color); 
            transform: scale(1.05);
        }

        .bar-bg { 
            width: 100%; height: 16px; 
            background: transparent; 
            border-radius: 10px;
            overflow: visible; position: relative;
        }

        .bar-fill { 
            height: 100%; width: 0%; transition: width 0.1s linear; 
            border-radius: 10px; position: relative;
            box-shadow: 0 0 10px var(--current-color); 
        }

        .active-member .bar-fill {
            box-shadow: 0 0 25px var(--current-color), 0 0 45px var(--current-color); 
            filter: brightness(1.2);
        }

        .duration-label { 
            position: absolute; right: 0; top: -22px; 
            color: white; font-size: 1.1rem; font-weight: 900; 
            font-family: 'Arial Black', sans-serif; z-index: 2;
            text-shadow: 0 0 12px var(--current-color), 0 0 5px rgba(0,0,0,0.8);
            filter: drop-shadow(0 0 2px var(--current-color));
            transition: all 0.4s ease;
        }

        .active-member .duration-label {
            text-shadow: 0 0 20px var(--current-color), 0 0 10px var(--current-color);
            transform: scale(1.2);
            filter: brightness(1.5);
        }

        .right-panel {
            flex-grow: 1; height: 100%; background: #fdfdfd;
            display: flex; flex-direction: column-reverse;
            padding: 40px; box-sizing: border-box; overflow: hidden;
            position: relative;
        }

        .chat-wrapper { display: flex; align-items: flex-end; margin: 12px 0; animation: slideUp 0.4s ease-out; }
        .chat-profile-container { position: relative; width: 60px; height: 45px; margin-right: 12px; flex-shrink: 0; }
        .chat-p-img { width: 45px; height: 45px; border-radius: 50%; background-size: cover; background-position: center; border: 2px solid #fff; box-shadow: 0 2px 5px rgba(0,0,0,0.2); position: absolute; left: 0; top: 0; }
        
        .img-stack-0 { z-index: 3; left: 0px; }
        .img-stack-1 { z-index: 2; left: 15px; }
        .img-stack-2 { z-index: 1; left: 30px; }

        .chat-content { display: flex; flex-direction: column; max-width: 85%; }
        .chat-singer-name { font-size: 0.8rem; font-weight: 800; margin-bottom: 4px; color: #555; text-transform: uppercase; margin-left: 10px; }
        .chat-bubble {
            padding: 14px 22px; border-radius: 20px; font-size: 1.8rem; font-weight: 700;
            box-shadow: 0 3px 8px rgba(0,0,0,0.08); border-bottom-left-radius: 4px;
        }

        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        .bubble-JAENA { background: var(--bar-color-JAENA); color: white; }
        .bubble-CHEONGHO { background: var(--bar-color-CHEONGHO); color: white; }
        .bubble-ARIN { background: var(--bar-color-ARIN); color: white; }
        .bubble-ROTZU { background: var(--bar-color-ROTZU); color: white; }
        .bubble-RIS { background: var(--bar-color-RIS); color: black; }
        .bubble-SEOLA { background: var(--bar-color-SEOLA); color: white; }
        .bubble-RAEJIN { background: var(--bar-color-RAEJIN); color: white; }
        .bubble-BOTH { background: var(--bar-color-BOTH); color: black; }

        .adlib-container {
            position: absolute; bottom: 40px; right: 40px;
            display: flex; flex-direction: column-reverse;
            align-items: flex-end; z-index: 100; pointer-events: none;
        }

        .adlib-item { 
            margin-bottom: 12px; padding: 10px 18px; border-radius: 15px;
            border-bottom-right-radius: 2px; backdrop-filter: blur(8px);
            background: rgba(255, 255, 255, 0.2); border: 1px solid rgba(255, 255, 255, 0.3);
            animation: adlibRise 3s forwards ease-out; text-align: right;
        }

        @keyframes adlibRise { 
            0% { opacity: 0; transform: translateY(30px); } 
            15% { opacity: 1; transform: translateY(0); }
            80% { opacity: 1; }
            100% { opacity: 0; transform: translateY(-100px); } 
        }

        .adlib-singer { font-size: 0.7rem; font-weight: 900; text-transform: uppercase; display: block; margin-bottom: 2px; }
        .adlib-text { font-size: 1.2rem; font-weight: 800; }

        .dashboard {
            width: 100%; background: #f4f4f4; display: flex; flex-wrap: wrap; 
            justify-content: center; align-items: center; gap: 10px;
            padding: 10px; border-top: 3px solid #ddd; z-index: 1000;
        }
        .dash-group { display: flex; gap: 8px; align-items: center; background: #e8e8e8; padding: 6px 12px; border-radius: 6px; font-size: 11px; }
        button { padding: 8px 14px; font-weight: bold; cursor: pointer; background: #444; color: white; border: none; border-radius: 4px; }
        .timer-display { font-family: monospace; font-size: 1.8rem; font-weight: bold; color: #e67e22; min-width: 100px; text-align: center; }
    </style>
</head>
<body>

<div class="video-container">
    <div class="left-panel">
        <video id="bg-video" loop muted></video>
        <div class="frame-3-5" id="main-frame">
            <div class="rank-container" id="rank-box"></div>
        </div>
    </div>
    <div class="right-panel" id="lyric-box"></div>
    <div class="adlib-container" id="adlib-box"></div>
</div>

<div class="dashboard">
    <div class="dash-group"><b>MEDIA:</b> V:<input type="file" id="v-file"> A:<input type="file" id="a-file"></div>
    <div class="dash-group" id="profile-inputs"></div>
    <div class="dash-group"><button onclick="rewind()">-5s</button><button id="playBtn" onclick="togglePlay()" style="background:#2ecc71">PLAY</button><button onclick="forward()">+5s</button></div>
    <div class="dash-group"><span>OFFSET:</span><input type="number" id="offset" value="0" step="0.1" style="width:40px"></div>
    <div class="timer-display" id="timer">0.0s</div>
</div>

<audio id="main-audio"></audio>

<script>
    const audio = document.getElementById('main-audio');
    const video = document.getElementById('bg-video');
    const lyricBox = document.getElementById('lyric-box');
    const adlibBox = document.getElementById('adlib-box');
    const rankBox = document.getElementById('rank-box');
    const playBtn = document.getElementById('playBtn');
    const offsetInp = document.getElementById('offset');
    const profInputsContainer = document.getElementById('profile-inputs');
    const mainFrame = document.getElementById('main-frame');

    const members = ["JAENA", "CHEONGHO", "ARIN", "ROTZU", "RIS", "SEOLA", "RAEJIN"];
    const profileUrls = {};

    members.forEach((m, i) => {
        rankBox.innerHTML += `
            <div class="member-row" id="row-${m}" style="top: ${i * 75}px; --current-color: var(--bar-color-${m});">
                <div class="profile-img" id="img-${m}"></div>
                <div class="bar-outer">
                    <div class="member-name">${m}</div>
                    <div class="bar-bg">
                        <div id="fill-${m}" class="bar-fill" style="background: var(--bar-color-${m});"></div>
                        <div class="duration-label" id="time-${m}">0.0</div>
                    </div>
                </div>
            </div>`;
        profInputsContainer.innerHTML += `${m[0].toUpperCase()}:<input type="file" onchange="upProf(this, '${m}')" style="width:60px"> `;
    });

    const lyricsData = [
        { start: 6.6, end: 8.2, singer: "JAENA", text: "SLAY", type: "adlib" },
        { start: 9.5, end: 9.8, singer: "CHEONGHO", text: "Yeah", type: "adlib" },
        { start: 10.3, end: 11.0, singer: "CHEONGHO", text: "Yeah", type: "adlib" },
        { start: 11.8, end: 12.4, singer: "CHEONGHO", text: "OK", type: "adlib" },
        { start: 12.5, end: 15.7, singer: "ARIN", text: "Get out your seats now" },
        { start: 15.8, end: 22.1, singer: "ARIN", text: "jeo eodum So damn dangerous mushimhage gireul bikyeo All right" },
        { start: 22.3, end: 25.8, singer: "ROTZU", text: "yeogi uju soge Steady\ngitbal deureo Ready" },
        { start: 25.9, end: 27.8, singer: "JAENA", text: "Oh oh oh oh", type: "adlib" },
        { start: 27.2, end: 29.0, singer: "RIS", text: "Makin’ them Makin’ them Makin’ them wow" },
        { start: 29.1, end: 30.7, singer: "JAENA", text: "Oh oh oh oh", type: "adlib" },
        { start: 30.3, end: 32.1, singer: "RIS", text: "Comin’ in Comin’ in Comin’ in Loud" },
        { start: 31.7, end: 34.7, singer: "JAENA", text: "kkeuteomneun Dark Night" },
        { start: 35.0, end: 38.8, singer: "JAENA", text: "chacha alge dwel Damage Baby, I’m a savage" },
        { start: 38.7, end: 44.7, singer: "SEOLA", text: "Me and my girls We about to blow your mind" },
        { start: 44.0, end: 45.1, singer: "RAEJIN", text: "Every time" },
        { start: 45.2, end: 48.5, singer: "RAEJIN", text: "Yeah yeah jeongbokhae Here Yeah yeah shijakhae Deal" },
        { start: 48.7, end: 51.7, singer: "RAEJIN", text: "Further mijie sege\nbalkhyeojulge Chandelier" },
        { start: 51.5, end: 60.8, singer: "BOTH", text: "Na na na na na na na\nAll my girls slay\nNa na na na na na na", parts: ["SEOLA", "ARIN"] },
        { start: 61.1, end: 64.3, singer: "ARIN", text: "Eyes on us nobody does it better" },
        { start: 64.3, end: 70.8, singer: "CHEONGHO", text: "Yeah Light it ups all my girls\nFeeling on top of the world\nThe way we move it side to side We shining like diamond and pearls" },
        { start: 70.7, end: 73.6, singer: "BOTH", text: "Na na na na na na na", parts: ["SEOLA", "ARIN"] },
        { start: 73.9, end: 75.9, singer: "JAENA", text: "We all bout to show you how" },
        { start: 75.9, end: 77.2, singer: "BOTH", text: "All my girls slay", parts: ["SEOLA", "ARIN"] },
        { start: 77.4, end: 80.5, singer: "ROTZU", text: "Slay Slay Crazy Sexy Cool" },
        { start: 80.5, end: 83.8, singer: "RIS", text: "Slay Slay We born to break the rules" },
        { start: 83.8, end: 86.9, singer: "ROTZU", text: "Slay Slay We got that attitude" },
        { start: 86.9, end: 89.8, singer: "RIS", text: "Slay Slay Everyday" },
        { start: 90.1, end: 93.1, singer: "RAEJIN", text: "nan gotonge kkeuteseo saeropge sarana\nigeoshi namane Key" },
        { start: 93.1, end: 93.4, singer: "RAEJIN", text: "(Key)", type: "adlib" },
        { start: 93.3, end: 96.2, singer: "RAEJIN", text: "jeo pamyeore kkeuteseo tteugeopge saraga igeoshi namane Free" },
        { start: 96.2, end: 96.4, singer: "RAEJIN", text: "(Free)", type: "adlib" },
        { start: 96.6, end: 102.7, singer: "RIS", text: "You know that? I’m the crown So don’t you waste my time I’m keeping it Real I’m keeping it Real I’m keeping it’s killer tonight" },
        { start: 102.7, end: 108.7, singer: "ARIN", text: "Me and my girls We about to blow your mind" },
        { start: 107.9, end: 109.0, singer: "RAEJIN", text: "Every time" },
        { start: 109.1, end: 112.4, singer: "RAEJIN", text: "Yeah yeah jeongbokhae Here Yeah yeah shijakhae Deal" },
        { start: 112.7, end: 115.6, singer: "RAEJIN", text: "Further mijie sege\nbalkhyeojulge Chandelier" },
        { start: 114.6, end: 120.1, singer: "SEOLA", text: "Chandelier", type: "adlib" },
        { start: 115.5, end: 124.7, singer: "BOTH", text: "Na na na na na na na\nAll my girls slay\nNa na na na na na na", parts: ["SEOLA", "ARIN"] },
        { start: 125.1, end: 128.3, singer: "SEOLA", text: "Eyes on us nobody does it better" },
        { start: 128.3, end: 134.7, singer: "RIS", text: "Yeah Light it ups all my girls\nFeeling on top of the world\nThe way we move it side to side We shining like diamond and pearls" },
        { start: 134.7, end: 137.6, singer: "BOTH", text: "Na na na na na na na", parts: ["SEOLA", "ARIN"] },
        { start: 137.8, end: 139.9, singer: "JAENA", text: "We all bout to show you how" },
        { start: 139.9, end: 141.3, singer: "BOTH", text: "All my girls slay", parts: ["SEOLA", "ARIN"] },
        { start: 141.3, end: 144.6, singer: "CHEONGHO", text: "Slay Slay Crazy Sexy Cool" },
        { start: 144.6, end: 147.7, singer: "ROTZU", text: "Slay Slay We born to break the rules" },
        { start: 147.7, end: 151.0, singer: "CHEONGHO", text: "Slay Slay We got that attitude" },
        { start: 151.0, end: 152.3, singer: "ROTZU", text: "Slay Slay" },
        { start: 152.3, end: 154.9, singer: "SEOLA", text: "Find me jiteun angae sok" },
        { start: 155.3, end: 158.2, singer: "SEOLA", text: "nan ara igeoshi got Good thing" },
        { start: 158.5, end: 162.9, singer: "SEOLA", text: "nan weonhae Time for the chance namane Pace" },
        { start: 163.3, end: 167.3, singer: "SEOLA", text: "deo nopi ollagago shipeo meolli naldorok" },
        { start: 167.2, end: 174.1, singer: "ARIN", text: "Gotta go, I’m doing what I want\nI’m never gonna stop naega kkumkkun sege Slay" },
        { start: 174.6, end: 175.6, singer: "ARIN", text: "Slay" },
        { start: 176.3, end: 177.3, singer: "ARIN", text: "Slay" },
        { start: 173.7, end: 174.8, singer: "JAENA", text: "yeah", type: "adlib" },
        { start: 175.6, end: 176.3, singer: "JAENA", text: "yeah", type: "adlib" },
        { start: 177.2, end: 177.9, singer: "JAENA", text: "yeah", type: "adlib" },
        { start: 178.2, end: 184.4, singer: "SEOLA", text: "We came here to SLAY" },
        { start: 179.6, end: 188.7, singer: "BOTH", text: "Na na na na na na na All my girls slay\nNa na na na na na na", parts: ["SEOLA", "ARIN"] },
        { start: 189.1, end: 192.3, singer: "JAENA", text: "Eyes on us nobody does it better" },
        { start: 192.4, end: 198.7, singer: "ARIN", text: "Yeah Light it ups all my girls Feeling on top of the world\nThe way we move it side to side We shining like diamond and pearls" },
        { start: 198.7, end: 201.6, singer: "BOTH", text: "Na na na na na na na", parts: ["SEOLA", "ARIN"] },
        { start: 201.8, end: 203.9, singer: "CHEONGHO", text: "We all bout to show you how" },
        { start: 203.9, end: 205.2, singer: "BOTH", text: "All my girls slay", parts: ["SEOLA", "ARIN"] },
        { start: 205.4, end: 208.5, singer: "ROTZU", text: "Slay Slay Crazy Sexy Cool" },
        { start: 208.5, end: 211.7, singer: "RIS", text: "Slay Slay We born to break the rules" },
        { start: 211.7, end: 214.9, singer: "ROTZU", text: "Slay Slay We got that attitude" },
        { start: 214.9, end: 218.0, singer: "RIS", text: "Slay Slay Everyday!" }
    ];

    const lastPartTime = {};
    members.forEach(m => {
        const memberLines = lyricsData.filter(l => l.singer === m || (l.parts && l.parts.includes(m)));
        if(memberLines.length > 0) {
            lastPartTime[m] = Math.max(...memberLines.map(l => l.end));
        }
    });

    let activeLines = new Set();
    const MAX_VAL = 75.1; 

    document.getElementById('v-file').onchange = e => video.src = URL.createObjectURL(e.target.files[0]);
    document.getElementById('a-file').onchange = e => audio.src = URL.createObjectURL(e.target.files[0]);
    
    function upProf(input, m) { 
        if (input.files[0]) {
            const url = URL.createObjectURL(input.files[0]);
            profileUrls[m] = url;
            document.getElementById(`img-${m}`).style.backgroundImage = `url('${url}')`;
        }
    }

    function togglePlay() { audio.paused ? (audio.play(), video.play(), playBtn.innerText="PAUSE", update()) : (audio.pause(), video.pause(), playBtn.innerText="PLAY"); }
    function rewind() { audio.currentTime -= 5; sync(); }
    function forward() { audio.currentTime += 5; sync(); }
    function sync() { video.currentTime = audio.currentTime + parseFloat(offsetInp.value); lyricBox.innerHTML=''; adlibBox.innerHTML=''; activeLines.clear(); }

    function update() {
        if (audio.paused) return;
        let cur = audio.currentTime;
        document.getElementById('timer').innerText = cur.toFixed(1) + "s";
        let totals = {}; members.forEach(m => totals[m] = 0);
        let active = {};
        let currentActiveSinger = null;

        lyricsData.forEach((line, i) => {
            if (cur >= line.start && !activeLines.has(i)) {
                activeLines.add(i);
                if (line.type === "adlib") {
                    const d = document.createElement('div');
                    d.className = `adlib-item adlib-${line.singer}`;
                    d.innerHTML = `<span class="adlib-singer" style="color:var(--bar-color-${line.singer}); font-weight:900;">${line.singer}</span><span class="adlib-text" style="font-weight:900;">${line.text}</span>`;
                    adlibBox.prepend(d);
                    setTimeout(() => { if(d.parentNode) adlibBox.removeChild(d); }, 3000);
                } else {
                    const wrapper = document.createElement('div');
                    wrapper.className = 'chat-wrapper';
                    let profileHTML = '';
                    if (line.singer === "BOTH") {
                        line.parts.forEach((p_name, idx) => {
                            const url = profileUrls[p_name] || "";
                            profileHTML += `<div class="chat-p-img img-stack-${idx}" style="background-image: url('${url}'); background-color: #333;"></div>`;
                        });
                    } else {
                        const imgUrl = profileUrls[line.singer] || "";
                        profileHTML = `<div class="chat-p-img" style="background-image: url('${imgUrl}'); background-color: #333;"></div>`;
                    }
                    const singerName = line.singer === "BOTH" ? (line.parts.join(" & ")) : line.singer;
                    wrapper.innerHTML = `<div class="chat-profile-container">${profileHTML}</div><div class="chat-content"><span class="chat-singer-name">${singerName}</span><div class="chat-bubble bubble-${line.singer}">${line.text.replace(/\n/g, '<br>')}</div></div>`;
                    lyricBox.prepend(wrapper);
                    if(lyricBox.children.length > 5) lyricBox.removeChild(lyricBox.lastChild);
                }
            }
            if (cur > line.start && cur <= line.end) {
                currentActiveSinger = line.singer; 
            }
            if (cur > line.start) {
                let p = Math.min(cur, line.end) - line.start;
                if (p > 0) {
                    if (line.singer === "BOTH") {
                        line.parts.forEach(p_name => { if(totals[p_name] !== undefined) totals[p_name] += p; if(cur<=line.end) active[p_name]=true; });
                    } else {
                        if(totals[line.singer] !== undefined) totals[line.singer] += p;
                        if(cur<=line.end) active[line.singer] = true;
                    }
                }
            }
        });

        if (currentActiveSinger) {
            let glowColor = currentActiveSinger === "BOTH" ? "white" : `var(--bar-color-${currentActiveSinger})`;
            document.documentElement.style.setProperty('--frame-glow', glowColor);
            mainFrame.classList.add('glow-active');
        } else {
            mainFrame.classList.remove('glow-active');
            document.documentElement.style.setProperty('--frame-glow', 'rgba(255,255,255,0.1)');
        }

        let sorted = [...members].sort((a,b) => totals[b] - totals[a]);
        members.forEach(m => {
            const fill = document.getElementById(`fill-${m}`);
            const timeLbl = document.getElementById(`time-${m}`);
            fill.style.width = Math.min((totals[m]/MAX_VAL)*100, 100) + "%";
            timeLbl.innerText = totals[m].toFixed(1);
            
            const row = document.getElementById(`row-${m}`);
            row.style.top = (sorted.indexOf(m) * 75) + "px"; 
            
            if (active[m]) {
                row.classList.add('active-member');
                row.classList.remove('finished');
            } else {
                row.classList.remove('active-member');
                if (cur > lastPartTime[m]) {
                    row.classList.add('finished');
                } else {
                    row.classList.remove('finished');
                }
            }
        });
        requestAnimationFrame(update);
    }
</script>
</body>
</html>
