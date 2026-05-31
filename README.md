# EG1.0
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>英语学习网站 - 与小动物一起学英语</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#4F46E5', secondary: '#EC4899', accent: '#10B981',
                        neutral: '#F3F4F6', 'neutral-dark': '#6B7280',
                    },
                    animation: {
                        'bounce-slow': 'bounce 2s infinite',
                        'pulse-fast': 'pulse 0.5s infinite',
                        'spin-slow': 'spin 1s linear infinite',
                    },
                }
            }
        }
    </script>
    <script src="https://aka.ms/csspeech/jsbrowserpackageraw"></script>
    <style type="text/tailwindcss">
        @layer utilities {
            .text-shadow { text-shadow: 2px 2px 4px rgba(0,0,0,0.2); }
            .animal-card { @apply bg-white rounded-2xl shadow-lg p-4 cursor-pointer transform transition-all hover:scale-105 hover:shadow-xl; }
            .btn-primary { @apply bg-[#4F46E5] text-white px-6 py-3 rounded-lg font-semibold hover:bg-opacity-90 transition-all shadow-md; }
            .btn-secondary { @apply bg-[#EC4899] text-white px-6 py-3 rounded-lg font-semibold hover:bg-opacity-90 transition-all shadow-md; }
            .btn-accent { @apply bg-[#10B981] text-white px-6 py-3 rounded-lg font-semibold hover:bg-opacity-90 transition-all shadow-md; }
            .input-primary { @apply border-2 border-gray-300 rounded-lg px-4 py-3 focus:outline-none focus:border-[#4F46E5] transition-all; }
            .card { @apply bg-white rounded-2xl shadow-lg p-6; }
        }
        .recording-wave { display:flex; align-items:center; justify-content:center; gap:4px; height:40px; }
        .recording-wave span { display:block; width:4px; height:100%; background-color:#EF4444; border-radius:2px; animation:wave 0.8s infinite ease-in-out; }
        .recording-wave span:nth-child(1){animation-delay:0.0s} .recording-wave span:nth-child(2){animation-delay:0.1s} .recording-wave span:nth-child(3){animation-delay:0.2s} .recording-wave span:nth-child(4){animation-delay:0.3s} .recording-wave span:nth-child(5){animation-delay:0.4s}
        @keyframes wave { 0%,100%{height:10px} 50%{height:40px} }
        @keyframes fire { 0%,100%{transform:scale(1) translateY(0);} 50%{transform:scale(1.3) translateY(-10px);} }
        .combo-fire { animation: fire 0.3s ease-in-out infinite; display: inline-block; }
        @keyframes pulse-bg { 0%,100%{box-shadow: 0 0 5px rgba(255,255,255,0.5);} 50%{box-shadow: 0 0 20px rgba(255,255,255,0.9);} }
        .pulse-block { animation: pulse-bg 0.8s ease-in-out infinite; }
        .confetti { position: fixed; top: -10px; animation: confetti-fall linear forwards; z-index: 9999; pointer-events: none; }
        @keyframes confetti-fall { to { transform: translateY(100vh) rotate(720deg); opacity: 0; } }
        #game-container::-webkit-scrollbar { height: 8px; }
        #game-container::-webkit-scrollbar-track { background: rgba(0,0,0,0.05); border-radius: 10px; }
        #game-container::-webkit-scrollbar-thumb { background: #c0c0c0; border-radius: 10px; }
    </style>
</head>
<body class="bg-gradient-to-br from-blue-50 to-purple-50 min-h-screen">
<div id="app" class="container mx-auto px-4 py-8">
    <!-- 欢迎屏幕 -->
    <div id="welcome-screen" class="flex flex-col items-center justify-center min-h-[80vh]">
        <h1 class="text-5xl md:text-6xl font-bold text-center mb-8 text-[#4F46E5] text-shadow">与小动物一起学英语</h1>
        <p class="text-xl text-center mb-12 max-w-2xl text-gray-500">选择你喜欢的小动物伙伴，开始你的英语学习之旅吧！</p>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-8 w-full max-w-5xl">
            <div class="animal-card" role="button" tabindex="0" data-animal="cat">
                <img src="https://cdn-icons-png.flaticon.com/512/616/616430.png" alt="小猫语语" class="w-full h-48 object-contain mb-4">
                <h3 class="text-xl font-bold text-center text-[#4F46E5]">语语</h3>
            </div>
            <div class="animal-card" data-animal="dog">
                <img src="https://cdn-icons-png.flaticon.com/512/616/616408.png" alt="小狗旺旺" class="w-full h-48 object-contain mb-4">
                <h3 class="text-xl font-bold text-center text-[#4F46E5]">旺旺</h3>
            </div>
            <div class="animal-card" data-animal="rabbit">
                <img src="https://cdn-icons-png.flaticon.com/512/616/616412.png" alt="小兔跳跳" class="w-full h-48 object-contain mb-4">
                <h3 class="text-xl font-bold text-center text-[#4F46E5]">跳跳</h3>
            </div>
            <div class="animal-card" data-animal="panda">
                <img src="https://cdn-icons-png.flaticon.com/512/616/616434.png" alt="熊猫盼盼" class="w-full h-48 object-contain mb-4">
                <h3 class="text-xl font-bold text-center text-[#4F46E5]">盼盼</h3>
            </div>
        </div>
    </div>

    <!-- 学习屏幕 -->
    <div id="learning-screen" class="hidden">
        <div class="flex justify-between items-center mb-8">
            <button id="back-btn" class="flex items-center text-[#4F46E5] font-semibold"><i class="fa fa-arrow-left mr-2"></i> 返回选择</button>
            <h2 class="text-2xl font-bold text-[#4F46E5]">英语学习</h2>
            <div class="w-20"></div>
        </div>
        <div class="flex flex-col lg:flex-row gap-8">
            <div class="lg:w-1/3 flex flex-col items-center">
                <div id="selected-animal" class="w-64 h-64 mb-6 animate-bounce-slow"></div>
                <div id="animal-speech-bubble" class="bg-white rounded-2xl p-4 shadow-lg mb-6 max-w-xs text-center" role="status" aria-live="polite">你好！我是你的英语学习伙伴。</div>
                <button id="listen-btn" class="btn-primary flex items-center"><i class="fa fa-volume-up mr-2"></i> 听发音</button>
            </div>
            <div class="lg:w-2/3">
                <!-- 单词查询 -->
                <div class="card mb-8">
                    <h3 class="text-xl font-bold mb-4 text-[#4F46E5]">单词学习</h3>
                    <div class="flex gap-4 mb-6">
                        <input id="word-input" type="text" class="input-primary flex-grow" placeholder="输入英语单词查询...">
                        <button id="search-btn" class="btn-primary"><i class="fa fa-search mr-2"></i> 查询</button>
                    </div>
                    <div id="word-result" class="hidden">
                        <h4 id="word-title" class="text-2xl font-bold text-[#4F46E5]"></h4>
                        <div id="word-meanings"></div>
                    </div>
                </div>
                <!-- 发音练习 -->
                <div class="card mb-8">
                    <h3 class="text-xl font-bold mb-4 text-[#4F46E5]">发音练习</h3>
                    <div class="mb-4"><input id="practice-word-input" type="text" class="input-primary w-full" placeholder="在这里输入要练习的单词..."></div>
                    <div class="flex gap-4 mb-4">
                        <button id="start-recording" class="btn-secondary flex items-center"><i class="fa fa-microphone mr-2"></i> 开始录音</button>
                        <button id="stop-recording" class="btn-secondary flex items-center hidden"><i class="fa fa-stop mr-2"></i> 停止录音</button>
                    </div>
                    <div id="recording-status" class="mt-4 hidden"><div class="flex items-center"><div class="recording-wave mr-3"><span></span><span></span><span></span><span></span><span></span></div><span>正在聆听…</span></div></div>
                    <div id="pronunciation-result" class="mt-4 hidden">
                        <h4 class="font-semibold mb-2">发音评分：<span id="pronunciation-score"></span><small id="score-source"></small></h4>
                        <div class="w-full bg-gray-200 rounded-full h-2.5"><div id="score-bar" class="bg-[#10B981] h-2.5 rounded-full" style="width:0%"></div></div>
                        <p id="pronunciation-feedback" class="mt-2 text-sm"></p>
                        <p id="pass-status" class="mt-2 text-lg font-bold"></p>
                    </div>
                </div>
                <!-- 闯关游戏入口 -->
                <div class="card">
                    <h3 class="text-xl font-bold mb-4 text-[#4F46E5]">听力闯关</h3>
                    <p class="mb-4 text-gray-500">点击下方按钮进入跳一跳闯关游戏！连续答对有惊喜哦~</p>
                    <button id="open-game-btn" class="btn-accent text-xl py-4 px-8 w-full md:w-auto">🎮 开始闯关游戏</button>
                </div>
                <!-- 错题集 -->
                <div id="wrong-words-card" class="card mt-8 hidden">
                    <h3 class="text-xl font-bold mb-4 text-[#EC4899]">错题集 <span id="wrong-count"></span></h3>
                    <ul id="wrong-words-list" class="list-disc pl-6 space-y-2"></ul>
                    <button id="clear-wrong-words" class="btn-secondary mt-4">清空错题集</button>
                </div>
            </div>
        </div>
    </div>
</div>

<!-- 游戏弹窗 -->
<div id="game-modal" class="fixed inset-0 bg-black bg-opacity-70 hidden flex items-center justify-center z-50">
    <div class="bg-gradient-to-br from-purple-100 to-pink-100 rounded-2xl shadow-2xl w-full max-w-2xl mx-4 p-6 relative overflow-hidden">
        <button id="close-game-btn" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 text-2xl z-10">&times;</button>
        <div id="combo-display" class="absolute top-4 left-4 text-2xl font-bold text-orange-500 hidden">🔥 连击 x<span id="combo-count">0</span></div>
        <div class="text-center mb-4">
            <span class="text-3xl font-bold text-[#4F46E5]">第 <span id="game-level">1</span> 关</span>
            <span class="ml-4 text-red-500">错误 <span id="game-error-count">0</span>/3</span>
            <span class="ml-4 text-gray-600">进度 <span id="game-progress">1/10</span></span>
        </div>
        <div id="game-container" class="relative bg-white/60 rounded-xl p-4 mb-4 shadow-inner overflow-x-auto overflow-y-hidden" style="height:220px;">
            <div id="game-blocks" class="flex items-end gap-8 h-full" style="min-width:max-content;"></div>
            <div id="game-character" class="absolute bottom-0 transform -translate-x-1/2 transition-all duration-500 ease-in-out" style="left:30px; font-size:50px; filter: drop-shadow(0 5px 5px rgba(0,0,0,0.2));">🐱</div>
        </div>
        <div class="flex flex-col items-center gap-4">
            <button id="play-game-word" class="btn-primary"><i class="fa fa-volume-up mr-2"></i> 听发音</button>
            <div class="flex gap-4 w-full">
                <input id="game-input" type="text" class="input-primary flex-grow" placeholder="输入你听到的单词...">
                <button id="game-submit" class="btn-accent relative overflow-hidden">确认</button>
            </div>
            <button id="game-restart" class="btn-secondary hidden">重新开始</button>
            <p id="game-message" class="font-semibold text-center text-lg"></p>
        </div>
    </div>
</div>

<script>
(function() {
    // 配置
    const AZURE_SPEECH_KEY = "aqYofh05xCJalC3geFaW2WexYBug5BY5u4uAKZNGIwwtyjQfz9WNJQQJ99CEAC3pKaRXJ3w3AAAYACOGpaZW";
    const AZURE_SPEECH_REGION = "eastasia";

    const animals = {
        cat: { name: "语语", image: "https://cdn-icons-png.flaticon.com/512/616/616430.png", greetings: ["你好！我是语语！"] },
        dog: { name: "旺旺", image: "https://cdn-icons-png.flaticon.com/512/616/616408.png", greetings: ["汪汪！我是旺旺！"] },
        rabbit: { name: "跳跳", image: "https://cdn-icons-png.flaticon.com/512/616/616412.png", greetings: ["大家好！我是跳跳！"] },
        panda: { name: "盼盼", image: "https://cdn-icons-png.flaticon.com/512/616/616434.png", greetings: ["你好！我是盼盼！"] }
    };

    const wordDictionary = {
        "access": "v. 获取 n. 接近，入口","project": "n. 工程；课题、作业","intention": "n. 打算，意图",
        "equivalence": "n. 等值，相等","negotiate": "v. 谈判，协商，交涉","disappointing": "adj. 令人失望的",
        "alternative": "n. 代替品","generous": "adj. 慷慨的","administration": "n. 管理；管理部门",
        "skillful": "adj. 灵巧的，娴熟的","data": "n. 数据；资料","pollution": "n. 污染",
        "possession": "n. 有，所有；占有物","manly": "adj. 男子气概的，果断的","reverse": "v. 颠倒；翻转 n. 背面",
        "giant": "adj. 巨大的 n. 巨人，巨物；才智超群的人","arbitrary": "adj. 随心所欲的， 专断的",
        "fearful": "adj. 害怕的， 可怕的； 不安的， 忧虑的","accent": "n. 口音， 腔调； 重音",
        "passive": "adj. 被动的， 消极的","fertilizer": "n. 肥料","lorry": "n. 运货汽车， 卡车",
        "carrier": "n. 运输工具， 运载工具； 带菌者； 载重架， 置物架","fragment": "v. 成碎片 n. 碎片，破片，碎块",
        "corresponding": "adj. 相应的， 符合的","response": "n. 回答， 答复； 反应， 响应",
        "treaty": "n. 条约， 协议， 协定","responsive": "adj. 响应的； 敏感的， 易受影响的","ounce": "n. 盎司",
        "biological": "adj. 生物的","strategy": "n. 策略，战略","paradox": "n. 悖论；自相矛盾",
        "primary": "adj. 主要的，基本的","standpoint": "n. 立场","grab": "v. 抢先，抢占，抢夺",
        "crucial": "adj. 至关重要的","flaw": "n. 缺点；错误","depressed": "adj. 萧条的；沮丧的","obstacle": "n. 阻碍"
    };
    const englishWords = Object.keys(wordDictionary);

    let currentState = {
        selectedAnimal: null,
        currentWord: null,
        practiceWord: "",
        recording: false,
        azureReady: false,
        azureRecognizer: null,
        timeoutId: null,
        targetWordCache: null,
        wrongWords: [],
        gameLevel: 1
    };

    // ---------- 工具函数 ----------
    const shuffle = arr => { const a=[...arr]; for(let i=a.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[a[i],a[j]]=[a[j],a[i]];} return a; };
    const getMixedWords = (count, wrongs) => {
        const revCnt = Math.min(wrongs.length, Math.ceil(count*0.2));
        const normal = shuffle(englishWords.filter(w=>!wrongs.includes(w))).slice(0,count-revCnt);
        return shuffle([...normal, ...shuffle(wrongs).slice(0,revCnt)]);
    };
    const speak = text => {
        if(!window.speechSynthesis) return;
        speechSynthesis.cancel();
        const u=new SpeechSynthesisUtterance(text);
        u.lang='en-US';u.rate=0.9;
        const setV=()=>{const v=speechSynthesis.getVoices();const e=v.find(x=>x.lang.startsWith('en'));if(e) u.voice=e; speechSynthesis.speak(u);};
        if(speechSynthesis.getVoices().length) setV(); else speechSynthesis.onvoiceschanged=setV;
    };
    const showMsg = msg => document.getElementById('animal-speech-bubble').textContent = msg;
    let audioCtx;
    const getCtx = ()=>(audioCtx=audioCtx||new (window.AudioContext||window.webkitAudioContext)());
    const sfx = type => {
        try{
            const c=getCtx(); const o=c.createOscillator(); const g=c.createGain();
            o.connect(g);g.connect(c.destination);
            if(type==='correct'){ o.frequency.setValueAtTime(880,c.currentTime); g.gain.setValueAtTime(0.3,c.currentTime); g.gain.exponentialRampToValueAtTime(0.01,c.currentTime+0.15); o.start();o.stop(c.currentTime+0.15); }
            else if(type==='combo'){ o.frequency.setValueAtTime(1200,c.currentTime); g.gain.setValueAtTime(0.5,c.currentTime); g.gain.exponentialRampToValueAtTime(0.01,c.currentTime+0.1); o.start();o.stop(c.currentTime+0.1); setTimeout(()=>{ const o2=c.createOscillator();o2.frequency.setValueAtTime(1500,c.currentTime);o2.connect(g);o2.start();o2.stop(c.currentTime+0.1); },100); }
            else { o.type='sawtooth'; o.frequency.setValueAtTime(200,c.currentTime); g.gain.setValueAtTime(0.2,c.currentTime); g.gain.exponentialRampToValueAtTime(0.01,c.currentTime+0.3); o.start();o.stop(c.currentTime+0.3); }
        }catch(e){}
    };

    // ---------- DOM 元素 ----------
    const welcome = document.getElementById('welcome-screen');
    const learning = document.getElementById('learning-screen');
    const animalCards = document.querySelectorAll('.animal-card');
    const backBtn = document.getElementById('back-btn');
    const selAnimal = document.getElementById('selected-animal');
    const speechBubble = document.getElementById('animal-speech-bubble');
    const wordInput = document.getElementById('word-input');
    const searchBtn = document.getElementById('search-btn');
    const wordResult = document.getElementById('word-result');
    const wordTitle = document.getElementById('word-title');
    const wordMeanings = document.getElementById('word-meanings');
    const practiceInp = document.getElementById('practice-word-input');
    const startRec = document.getElementById('start-recording');
    const stopRec = document.getElementById('stop-recording');
    const recStatus = document.getElementById('recording-status');
    const pronResult = document.getElementById('pronunciation-result');
    const pronScore = document.getElementById('pronunciation-score');
    const scoreBar = document.getElementById('score-bar');
    const pronFeed = document.getElementById('pronunciation-feedback');
    const scoreSrc = document.getElementById('score-source');
    const passStat = document.getElementById('pass-status');
    const listenBtn = document.getElementById('listen-btn');
    const openGameBtn = document.getElementById('open-game-btn');
    const gameModal = document.getElementById('game-modal');
    const closeGameBtn = document.getElementById('close-game-btn');
    const gLevel = document.getElementById('game-level');
    const gErrCnt = document.getElementById('game-error-count');
    const gProgress = document.getElementById('game-progress');
    const gBlocks = document.getElementById('game-blocks');
    const gChar = document.getElementById('game-character');
    const playGameWord = document.getElementById('play-game-word');
    const gInput = document.getElementById('game-input');
    const gSubmit = document.getElementById('game-submit');
    const gRestart = document.getElementById('game-restart');
    const gMsg = document.getElementById('game-message');
    const comboDisplay = document.getElementById('combo-display');
    const comboCount = document.getElementById('combo-count');
    const gameContainer = document.getElementById('game-container');
    const wrongCard = document.getElementById('wrong-words-card');
    const wrongList = document.getElementById('wrong-words-list');
    const wrongCount = document.getElementById('wrong-count');
    const clearWrongBtn = document.getElementById('clear-wrong-words');

    // ---------- 基础功能 ----------
    const addWrong = word => { if(!currentState.wrongWords.includes(word)){ currentState.wrongWords.push(word); updateWrongUI(); } };
    const updateWrongUI = () => {
        if(!currentState.wrongWords.length){ wrongCard.classList.add('hidden'); return; }
        wrongCard.classList.remove('hidden'); wrongCount.textContent = `(${currentState.wrongWords.length}个)`;
        wrongList.innerHTML = currentState.wrongWords.map(w=>`<li class="flex items-center justify-between bg-gray-50 rounded-lg p-2"><span>${w}</span><button class="play-wrong text-[#10B981]" data-word="${w}"><i class="fa fa-volume-up"></i></button></li>`).join('');
        document.querySelectorAll('.play-wrong').forEach(b=>b.addEventListener('click',()=>speak(b.dataset.word)));
    };
    clearWrongBtn.addEventListener('click',()=>{ currentState.wrongWords=[]; updateWrongUI(); showMsg('错题集已清空！'); });

    // 页面切换
    animalCards.forEach(c=>c.addEventListener('click',()=>{
        const type=c.dataset.animal; currentState.selectedAnimal=type; const a=animals[type];
        selAnimal.innerHTML=`<img src="${a.image}" alt="${a.name}" class="w-full h-full object-contain">`;
        showMsg(a.greetings[0]); welcome.classList.add('hidden'); learning.classList.remove('hidden');
        practiceInp.value=''; currentState.practiceWord='';
    }));
    backBtn.addEventListener('click',()=>{ welcome.classList.remove('hidden'); learning.classList.add('hidden'); stopPronunciation(); });

    // 单词查询
    const searchWord = word => {
        const w=word.trim().toLowerCase(); currentState.currentWord={word:w};
        if(wordDictionary[w]){
            wordTitle.textContent=w; wordMeanings.innerHTML=`<div class="mb-2"><h5 class="font-semibold text-[#4F46E5]">中文释义</h5><p class="pl-1 mt-1">${wordDictionary[w]}</p></div>`;
            wordResult.classList.remove('hidden'); showMsg(`"${w}" 的意思是：${wordDictionary[w]}`);
        } else { showMsg(`词库里没有"${word}"`); wordResult.classList.add('hidden'); currentState.currentWord=null; }
    };
    searchBtn.addEventListener('click',()=>{ if(wordInput.value.trim()) searchWord(wordInput.value.trim()); });
    wordInput.addEventListener('keypress',e=>{ if(e.key==='Enter' && wordInput.value.trim()) searchWord(wordInput.value.trim()); });

    // 发音练习
    const getTargetWord = ()=> currentState.practiceWord || (currentState.currentWord?.word);
    const resetPronResult = ()=>{ pronResult.classList.add('hidden'); pronScore.textContent=''; scoreBar.style.width='0%'; pronFeed.textContent=''; scoreSrc.textContent=''; passStat.textContent=''; };
    const displayScore = (score, isReal)=>{
        pronScore.textContent = `${score}%`; scoreBar.style.width = `${score}%`; scoreSrc.textContent = isReal?'(Azure 真实评分)':'(模拟评分)';
        if(score>=80){
            scoreBar.className='bg-[#10B981] h-2.5 rounded-full'; pronFeed.textContent='太棒了！'; passStat.textContent='✅ 通过'; passStat.className='mt-2 text-lg font-bold text-[#10B981]';
            showMsg("你的发音太标准了！");
        } else if(score>=60){
            scoreBar.className='bg-yellow-500 h-2.5 rounded-full'; pronFeed.textContent='不错，继续练习'; passStat.textContent='🔄 请再读一遍'; passStat.className='mt-2 text-lg font-bold text-yellow-500';
            showMsg("还不错，再练习一下就更好了！");
        } else {
            scoreBar.className='bg-red-500 h-2.5 rounded-full'; pronFeed.textContent='需要努力'; passStat.textContent='🔄 请再读一遍'; passStat.className='mt-2 text-lg font-bold text-red-500';
            showMsg("别灰心，多听多读就能进步！");
        }
        pronResult.classList.remove('hidden');
    };
    const simScore = w => { const l=w.length; const c=/[^aeiou]{3,}/i.test(w)||l>7; return Math.min(98,Math.max(40,(c?55:70)+Math.floor(Math.random()*30)-10)); };
    const calcSimilarity = (a,b)=>{ if(a===b) return 100; if(b.includes(a)) return 85; if(a.includes(b)) return 75; const dist=levenshtein(a,b); const max=Math.max(a.length,b.length); return Math.max(0,100-(dist/max)*100); };
    const levenshtein = (a,b)=>{ const m=[]; for(let i=0;i<=b.length;i++) m[i]=[i]; for(let j=0;j<=a.length;j++) m[0][j]=j; for(let i=1;i<=b.length;i++) for(let j=1;j<=a.length;j++) m[i][j]=b.charAt(i-1)===a.charAt(j-1)?m[i-1][j-1]:Math.min(m[i-1][j-1]+1,m[i][j-1]+1,m[i-1][j]+1); return m[b.length][a.length]; };
    const stopRecUI = ()=>{ currentState.recording=false; startRec.classList.remove('hidden'); stopRec.classList.add('hidden'); recStatus.classList.add('hidden'); };

    const startAzureRec = ()=>{
        const cfg = SpeechSDK.SpeechConfig.fromSubscription(AZURE_SPEECH_KEY, AZURE_SPEECH_REGION);
        cfg.speechRecognitionLanguage='en-US';
        const audio = SpeechSDK.AudioConfig.fromDefaultMicrophoneInput();
        const rec = new SpeechSDK.SpeechRecognizer(cfg, audio);
        currentState.azureRecognizer=rec;
        clearTimeout(currentState.timeoutId);
        currentState.timeoutId=setTimeout(()=>{ if(currentState.recording){ stopPronunciation(); showMsg('录音超时'); } },10000);
        rec.recognizeOnceAsync(res=>{
            clearTimeout(currentState.timeoutId);
            if(currentState.azureRecognizer===rec) currentState.azureRecognizer=null;
            if(res.reason===SpeechSDK.ResultReason.RecognizedSpeech){
                const txt=res.text.trim(); let conf=0;
                try{ const j=JSON.parse(res.properties.getProperty(SpeechSDK.PropertyId.SpeechServiceResponse_JsonResult)); if(j.NBest?.length) conf=j.NBest[0].Confidence||0; }catch(e){}
                stopRecUI(); if(txt) showRealScore(txt,conf); else showSimScore();
            } else if(res.reason===SpeechSDK.ResultReason.NoMatch){ stopRecUI(); showMsg('未识别到语音'); showSimScore(); }
            else { stopRecUI(); showMsg('识别服务异常'); showSimScore(); }
            rec.close();
        }, err=>{ clearTimeout(currentState.timeoutId); stopRecUI(); showMsg('识别失败'); showSimScore(); rec.close(); });
    };
    const showRealScore = (transcript,conf)=>{
        const t=getTargetWord(); if(!t) return;
        let s=calcSimilarity(t.toLowerCase(),transcript.toLowerCase());
        const a=conf*100;
        displayScore(Math.round(Math.max(0,Math.min(100,a>0?(s*0.6+a*0.4):s))),true);
    };
    const showSimScore = ()=>{ const t=getTargetWord(); if(t) displayScore(simScore(t),false); };

    const startPronunciation = ()=>{
        const w=getTargetWord(); if(!w){ showMsg('请先输入单词'); return; }
        currentState.targetWordCache=w; speak(w); resetPronResult(); currentState.recording=true;
        startRec.classList.add('hidden'); stopRec.classList.remove('hidden'); recStatus.classList.remove('hidden');
        if(currentState.azureReady) startAzureRec();
        else { clearTimeout(currentState.timeoutId); currentState.timeoutId=setTimeout(()=>{ if(currentState.recording){ stopRecUI(); showSimScore(); } },2000); }
    };
    const stopPronunciation = ()=>{
        clearTimeout(currentState.timeoutId);
        if(currentState.azureRecognizer){ currentState.azureRecognizer.close(); currentState.azureRecognizer=null; }
        stopRecUI();
    };
    practiceInp.addEventListener('input', ()=> currentState.practiceWord = practiceInp.value.trim());
    startRec.addEventListener('click', startPronunciation);
    stopRec.addEventListener('click', stopPronunciation);
    if(typeof SpeechSDK!=='undefined' && AZURE_SPEECH_KEY && AZURE_SPEECH_KEY.length>32) currentState.azureReady=true;

    // ---------- 游戏部分 ----------
    let gameData = { words:[], index:0, correct:0, errors:0, over:false, animating:false, combo:0 };

    const randomColor = ()=>{
        const colors = ['#FF6B6B','#4ECDC4','#FFE66D','#1A535C','#FF9F1C','#A29BFE','#FD79A8','#00B894'];
        return colors[Math.floor(Math.random()*colors.length)];
    };
    const scrollToCurrentBlock = ()=>{
        if(!gameData.words.length || gameData.index >= gameData.words.length) return;
        const block = gBlocks.children[gameData.index];
        if(block) block.scrollIntoView({ behavior: 'smooth', block: 'nearest', inline: 'center' });
    };
    const renderBlocks = count => {
        gBlocks.innerHTML = '';
        for(let i=0;i<count;i++){
            const b=document.createElement('div');
            b.className='flex-shrink-0 w-16 h-16 rounded-xl border-2 border-white shadow-md flex items-center justify-center text-white font-bold transition-all duration-300';
            b.style.backgroundColor = randomColor();
            b.textContent = i+1;
            if(i===0) b.classList.add('pulse-block','ring-4','ring-yellow-300');
            gBlocks.appendChild(b);
        }
        gameContainer.scrollLeft = 0;
    };
    const updateGameUI = ()=>{
        gProgress.textContent = `${gameData.index+1}/${gameData.words.length}`;
        gErrCnt.textContent = `${gameData.errors}/3`;
        if(gameData.combo >= 2) { comboDisplay.classList.remove('hidden'); comboCount.textContent = gameData.combo; }
        else { comboDisplay.classList.add('hidden'); }
    };
    const playCurrentWord = ()=>{ if(gameData.index < gameData.words.length) speak(gameData.words[gameData.index]); };

    const jumpTo = targetIndex => {
        const curBlock = gBlocks.children[gameData.index];
        const tarBlock = gBlocks.children[targetIndex];
        const startLeft = curBlock.offsetLeft + curBlock.offsetWidth/2;
        const endLeft = tarBlock.offsetLeft + tarBlock.offsetWidth/2;
        gChar.style.transition = 'left 0.6s cubic-bezier(0.25,0.46,0.45,0.94), bottom 0.6s cubic-bezier(0.25,0.46,0.45,0.94), transform 0.6s';
        gChar.style.left = startLeft + 'px';
        gChar.style.bottom = '0px';
        gChar.style.transform = 'translateX(-50%) rotate(0deg) scale(1)';
        requestAnimationFrame(()=>{
            gChar.style.left = endLeft + 'px';
            gChar.style.bottom = '50px';
            gChar.style.transform = 'translateX(-50%) rotate(15deg) scale(1.2)';
            setTimeout(()=>{
                gChar.style.bottom = '0px';
                gChar.style.transform = 'translateX(-50%) rotate(0deg) scale(1)';
                scrollToCurrentBlock();
            },300);
        });
    };
    const failJump = ()=>{
        const curBlock = gBlocks.children[gameData.index];
        const startLeft = curBlock.offsetLeft + curBlock.offsetWidth/2;
        gChar.style.transition = 'left 0.5s ease-in, bottom 0.5s ease-in, transform 0.5s';
        gChar.style.left = startLeft + 'px';
        gChar.style.bottom = '0px';
        requestAnimationFrame(()=>{
            gChar.style.left = (startLeft + 50) + 'px';
            gChar.style.bottom = '30px';
            gChar.style.transform = 'translateX(-50%) rotate(45deg)';
            setTimeout(()=>{ gChar.style.bottom = '-80px'; },300);
        });
    };
    const spawnConfetti = ()=>{
        for(let i=0;i<30;i++){
            setTimeout(()=>{
                const el=document.createElement('div'); el.className='confetti';
                el.style.left=Math.random()*100+'%'; el.style.fontSize=(Math.random()*20+10)+'px';
                el.textContent=['🎉','✨','🌟','💖','🎈'][Math.floor(Math.random()*5)];
                el.style.animationDuration=(Math.random()*2+2)+'s';
                document.body.appendChild(el); setTimeout(()=>el.remove(),3000);
            },i*30);
        }
    };
    const gameWin = ()=>{
        gameData.over=true;
        gMsg.innerHTML='🎉 闯关成功！太厉害了！'; gMsg.className='text-[#10B981] font-bold mt-2 text-xl';
        gRestart.classList.remove('hidden'); gSubmit.disabled=true;
        currentState.gameLevel++; showMsg("你太棒了！闯关成功！"); spawnConfetti(); sfx('combo');
    };

    const submitAnswer = ()=>{
        if(gameData.over || gameData.animating) return;
        const ans=gInput.value.trim().toLowerCase();
        if(!ans){ showMsg('请输入单词'); return; }
        const correct=gameData.words[gameData.index].toLowerCase();
        if(ans===correct){
            sfx('correct'); gameData.correct++; gameData.combo++;
            if(gameData.combo===3) sfx('combo');
            const nextIdx=gameData.index+1;
            if(nextIdx<gameData.words.length){
                gameData.animating=true; jumpTo(nextIdx);
                const blocks=gBlocks.children;
                blocks[gameData.index].classList.add('bg-green-100','border-green-400');
                blocks[gameData.index].classList.remove('pulse-block','ring-4','ring-yellow-300');
                blocks[nextIdx].classList.add('pulse-block','ring-4','ring-yellow-300');
                gameData.index=nextIdx; updateGameUI(); gInput.value='';
                setTimeout(()=>{ gameData.animating=false; playCurrentWord(); },600);
            } else gameWin();
        } else {
            sfx('wrong'); gameData.errors++; gameData.combo=0; updateGameUI();
            if(gameData.errors>3){
                gameData.over=true; gameData.animating=true; failJump();
                gMsg.innerHTML='😭 游戏结束，错误太多啦！人偶掉下去了…'; gMsg.className='text-red-500 font-bold mt-2 text-lg';
                gSubmit.disabled=true; gRestart.classList.remove('hidden'); addWrong(correct);
                setTimeout(()=>gameData.animating=false,600);
            } else {
                gChar.style.transition='bottom 0.15s'; gChar.style.bottom='15px';
                setTimeout(()=>{ gChar.style.bottom='0px'; },150);
                gMsg.textContent='❌ 错误！请再试一次（已自动跳过）'; gMsg.className='text-yellow-500 font-bold mt-2';
                addWrong(correct);
                const nextIdx=gameData.index+1;
                if(nextIdx<gameData.words.length){
                    const blocks=gBlocks.children;
                    blocks[gameData.index].classList.add('bg-red-100','border-red-400');
                    blocks[gameData.index].classList.remove('pulse-block','ring-4','ring-yellow-300');
                    blocks[nextIdx].classList.add('pulse-block','ring-4','ring-yellow-300');
                    gameData.index=nextIdx; updateGameUI(); gInput.value='';
                    playCurrentWord(); scrollToCurrentBlock();
                } else gameWin();
            }
        }
        gInput.value='';
    };

    const openGame = ()=>{
        const count=10;
        gameData={ words:getMixedWords(count, currentState.wrongWords), index:0, correct:0, errors:0, over:false, animating:false, combo:0 };
        gLevel.textContent=currentState.gameLevel; renderBlocks(count); updateGameUI();
        gameModal.classList.remove('hidden');
        gChar.style.left='30px'; gChar.style.bottom='0px'; gChar.style.transform='translateX(-50%) rotate(0deg) scale(1)';
        gRestart.classList.add('hidden'); gMsg.textContent=''; gSubmit.disabled=false; gInput.value=''; comboDisplay.classList.add('hidden');
        setTimeout(()=>{ playCurrentWord(); scrollToCurrentBlock(); },300);
    };

    gSubmit.addEventListener('click', submitAnswer);
    gInput.addEventListener('keypress', e=>{ if(e.key==='Enter') submitAnswer(); });
    playGameWord.addEventListener('click', ()=>{ if(!gameData.over && gameData.index < gameData.words.length) speak(gameData.words[gameData.index]); });
    gRestart.addEventListener('click', ()=>{ gInput.value=''; gMsg.textContent=''; gRestart.classList.add('hidden'); openGame(); });
    closeGameBtn.addEventListener('click', ()=> gameModal.classList.add('hidden'));
    openGameBtn.addEventListener('click', openGame);
    listenBtn.addEventListener('click', ()=>{ if(speechBubble.textContent) speak(speechBubble.textContent); });

    // 初始化
    document.addEventListener('DOMContentLoaded', ()=>{
        if(navigator.mediaDevices) navigator.mediaDevices.getUserMedia({audio:true}).catch(()=>{});
    });
})();
</script>
</body>
</html>
