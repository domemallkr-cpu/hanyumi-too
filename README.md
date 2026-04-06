import React, { useState, useEffect, useRef } from 'react';
import { Camera, Download, Sparkles, Image as ImageIcon, RefreshCcw, Layout, Type, Palette, Eye, Music, Play, CheckCircle } from 'lucide-react';

// --- 사계절 테마 및 컬러 설정 ---
const SEASONS = {
  SPRING: { label: "봄 (Spring)", top: "#fde7f3", bottom: "#fbcfe8", accent: "#ec4899", text: "#831843", bgDesc: "벚꽃 흩날리는 핑크빛 감성" },
  SUMMER: { label: "여름 (Summer)", top: "#dbeafe", bottom: "#93c5fd", accent: "#2563eb", text: "#1e3a8a", bgDesc: "청량하고 푸른 바다의 에너기" },
  AUTUMN: { label: "가을 (Autumn)", top: "#fef3c7", bottom: "#fdba74", accent: "#c2410c", text: "#7c2d12", bgDesc: "노을빛과 단풍의 성숙한 분위기" },
  WINTER: { label: "겨울 (Winter)", top: "#f1f5f9", bottom: "#cbd5e1", accent: "#334155", text: "#0f172a", bgDesc: "눈 내리는 차분하고 깊은 감동" }
};

const CTR_POWER_WORDS = ["전성기", "인생", "눈물", "최초", "반전", "충격", "레전드", "지금", "터졌다", "기적"];

const DEFAULT_LYRICS = `[Chorus]
가을 바람 따라 마음 흩어지는
단풍잎에 새긴 우리의 약속
시간은 흘러도 변하지 않는 건
내 가슴속 뜨거운 그대 목소리

[Verse 1]
노을빛 물든 거리를 걸으며
잊었던 추억을 다시 꺼내보네`;

const App = () => {
  const canvasRef = useRef(null);
  const [activeSeason, setActiveSeason] = useState("AUTUMN");
  const [songName, setSongName] = useState("가을의 약속 (Autumn Promise)");
  const [mainTitle, setMainTitle] = useState("가을 바람 따라 마음 흩어지는");
  const [subTitle, setSubTitle] = useState("사계절을 노래하는 AI 싱어 한유미");
  const [hookText, setHookText] = useState("가을 감성 레전드 무대");
  const [lyrics, setLyrics] = useState(DEFAULT_LYRICS);
  
  // 가을3.jpg 이미지 스타일 반영 (고화질 플레이스홀더)
  const [faceUrl, setFaceUrl] = useState("https://images.unsplash.com/photo-1544005313-94ddf0286df2?auto=format&fit=crop&q=80&w=800");
  
  const [isRendering, setIsRendering] = useState(false);
  const [ctrScore, setCtrScore] = useState(0);
  const [logs, setLogs] = useState([]);
  const [executionState, setExecutionState] = useState('idle');

  // --- 클립보드 복사 폴백 로직 (iframe 환경 대응) ---
  const copyToClipboard = (text) => {
    try {
      const textArea = document.createElement("textarea");
      textArea.value = text;
      textArea.style.position = "fixed";
      textArea.style.left = "-9999px";
      textArea.style.top = "0";
      document.body.appendChild(textArea);
      textArea.focus();
      textArea.select();
      const successful = document.execCommand('copy');
      document.body.removeChild(textArea);
      return successful;
    } catch (err) {
      console.error("Copy failed: ", err);
      return false;
    }
  };

  // --- 디자인 성과 점수 계산 ---
  const calculateCtr = () => {
    let score = 82;
    if (mainTitle.length > 10 && mainTitle.length < 25) score += 8;
    CTR_POWER_WORDS.forEach(word => {
      if (mainTitle.includes(word) || hookText.includes(word)) score += 4;
    });
    setCtrScore(Math.min(99, score));
  };

  const addLog = (msg) => {
    setLogs(prev => [{ id: Date.now(), msg, time: new Date().toLocaleTimeString() }, ...prev].slice(0, 5));
  };

  // --- 가사에서 타이틀 추출 ---
  const extractFromLyrics = () => {
    const lines = lyrics
      .split('\n')
      .map(l => l.trim())
      .filter(l => l && !l.startsWith('['));
    
    if (lines.length > 0) {
      const randomLine = lines[Math.floor(Math.random() * lines.length)];
      setMainTitle(randomLine);
      return randomLine;
    }
    return mainTitle;
  };

  // --- 디자인 실행 (Run) 전체 시퀀스 ---
  const handleRunExecution = async () => {
    setExecutionState('running');
    addLog("자동 디자인 워크플로우 시작...");
    
    await new Promise(r => setTimeout(r, 600));
    const extracted = extractFromLyrics();
    addLog(`가사 매칭 완료: "${extracted.slice(0, 12)}..."`);
    
    await new Promise(r => setTimeout(r, 400));
    drawThumbnail();
    
    await new Promise(r => setTimeout(r, 500));
    setExecutionState('completed');
    addLog("썸네일 레이아웃 최적화 완료");
    
    setTimeout(() => setExecutionState('idle'), 3000);
  };

  // --- 캔버스 드로잉 엔진 ---
  const drawThumbnail = async () => {
    const canvas = canvasRef.current;
    if (!canvas) return;
    const ctx = canvas.getContext('2d');
    const theme = SEASONS[activeSeason];

    setIsRendering(true);
    
    // 1. 배경 그라데이션
    const grad = ctx.createLinearGradient(0, 0, canvas.width, canvas.height);
    grad.addColorStop(0, theme.top);
    grad.addColorStop(1, theme.bottom);
    ctx.fillStyle = grad;
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // 2. 가을 잎사귀 장식 효과
    ctx.fillStyle = 'rgba(194, 65, 12, 0.06)';
    for(let i=0; i<15; i++) {
        ctx.save();
        ctx.translate(Math.random() * canvas.width, Math.random() * canvas.height);
        ctx.rotate(Math.random() * Math.PI);
        ctx.fillRect(-20, -20, 40, 40);
        ctx.restore();
    }

    // 3. 텍스트 오버레이 화이트 박스
    ctx.fillStyle = 'rgba(255, 255, 255, 0.98)';
    ctx.shadowColor = 'rgba(0,0,0,0.1)';
    ctx.shadowBlur = 40;
    ctx.fillRect(50, 50, 650, 620);
    ctx.shadowBlur = 0;

    // 4. 곡명 태그
    ctx.fillStyle = theme.accent;
    ctx.font = "bold 22px sans-serif";
    const songTagText = `🍁 ${songName}`;
    const songTagWidth = ctx.measureText(songTagText).width + 40;
    
    ctx.beginPath();
    if(ctx.roundRect) ctx.roundRect(80, 85, songTagWidth, 45, 8); else ctx.fillRect(80, 85, songTagWidth, 45);
    ctx.fill();
    
    ctx.fillStyle = "#ffffff";
    ctx.fillText(songTagText, 100, 115);

    // 5. 메인 타이틀
    ctx.fillStyle = "#111827";
    ctx.font = "bold 72px sans-serif";
    const words = mainTitle.split(" ");
    let line1 = "", line2 = "";
    words.forEach(w => {
        if ((line1 + w).length < 10) line1 += w + " ";
        else line2 += w + " ";
    });
    ctx.fillText(line1.trim(), 80, 230);
    if(line2) ctx.fillText(line2.trim(), 80, 330);

    // 6. 서브타이틀
    ctx.fillStyle = "#6b7280";
    ctx.font = "500 32px sans-serif";
    ctx.fillText(subTitle, 80, 550);

    // 7. 후킹 포인트 배지
    ctx.fillStyle = theme.accent;
    ctx.beginPath();
    const hookWidth = ctx.measureText(hookText).width + 40;
    if (ctx.roundRect) ctx.roundRect(80, 590, hookWidth, 50, 10); else ctx.fillRect(80, 590, hookWidth, 50);
    ctx.fill();
    ctx.fillStyle = "#ffffff";
    ctx.font = "bold 24px sans-serif";
    ctx.fillText(hookText, 100, 625);

    // 8. 한유미 얼굴 이미지 배치 (우측 프레임)
    if (faceUrl) {
      const img = new Image();
      img.crossOrigin = "anonymous";
      img.src = faceUrl;
      img.onload = () => {
        const aspect = img.width / img.height;
        const targetH = 620;
        const targetW = targetH * aspect;
        
        ctx.save();
        if (ctx.roundRect) {
            ctx.beginPath();
            ctx.roundRect(730, 50, 500, 620, 30);
            ctx.clip();
        }
        ctx.drawImage(img, 730 + (500 - targetW)/2, 50, targetW, targetH);
        ctx.restore();
        
        ctx.strokeStyle = theme.accent;
        ctx.lineWidth = 12;
        if (ctx.roundRect) {
            ctx.beginPath();
            ctx.roundRect(730, 50, 500, 620, 30);
            ctx.stroke();
        }
        setIsRendering(false);
        calculateCtr();
      };
      img.onerror = () => {
          setIsRendering(false);
          addLog("이미지 로드 실패 (URL 확인 필요)");
      };
    } else {
        setIsRendering(false);
    }
  };

  useEffect(() => {
    drawThumbnail();
  }, [activeSeason, songName, mainTitle, subTitle, hookText, faceUrl]);

  const handleDownload = () => {
    const link = document.createElement('a');
    link.download = `hanyumi-${songName.replace(/\s+/g, '-')}.png`;
    link.href = canvasRef.current.toDataURL('image/png');
    link.click();
    addLog("디자인 이미지 저장 완료");
  };

  const getAiPrompt = () => {
    const prompt = `Ultra-premium YouTube thumbnail background, ${SEASONS[activeSeason].bgDesc}, golden hour lighting, cinematic atmosphere, high quality portrait of a Korean singer, gold microphone, autumn leaves, photorealistic, 8k.`;
    const success = copyToClipboard(prompt);
    if (success) addLog("배경 AI 프롬프트 복사 성공");
  };

  return (
    <div className="min-h-screen bg-slate-50 p-8 font-sans text-slate-900">
      <div className="mx-auto max-w-7xl space-y-8">
        
        <header className="flex flex-col md:flex-row md:items-center justify-between gap-4">
          <div>
            <h1 className="text-3xl font-extrabold tracking-tight flex items-center gap-2">
              <Sparkles className="text-orange-600" />
              Thumbnail Master <span className="text-orange-600 text-lg font-medium">Autumn Special</span>
            </h1>
            <p className="text-slate-500 mt-1">한유미 가을 이미지 최적화 및 자동 썸네일 생성 도구</p>
          </div>
          <div className="flex gap-2">
            <button 
              onClick={handleRunExecution}
              disabled={executionState === 'running'}
              className={`flex items-center gap-2 px-6 py-2 rounded-xl font-bold transition-all shadow-md ${
                executionState === 'running' ? 'bg-slate-400 cursor-not-allowed' : 
                executionState === 'completed' ? 'bg-orange-600 text-white' : 'bg-slate-900 text-white hover:bg-slate-800'
              }`}
            >
              {executionState === 'running' ? <RefreshCcw size={18} className="animate-spin" /> : 
               executionState === 'completed' ? <CheckCircle size={18} /> : <Play size={18} fill="currentColor" />}
              {executionState === 'running' ? '생성 중...' : executionState === 'completed' ? '생성 완료' : '디자인 실행 (Run)'}
            </button>
            <button 
              onClick={handleDownload}
              className="flex items-center gap-2 bg-blue-600 text-white px-6 py-2 rounded-xl font-bold hover:bg-blue-700 transition-all shadow-md shadow-blue-200"
            >
              <Download size={18} />
              PNG 저장
            </button>
          </div>
        </header>

        <div className="grid grid-cols-1 lg:grid-cols-12 gap-8">
          
          <aside className="lg:col-span-4 space-y-6">
            <div className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
              <h2 className="text-lg font-bold flex items-center gap-2"><Palette size={20} className="text-orange-500"/> 계절 테마</h2>
              <div className="grid grid-cols-2 gap-2">
                {Object.entries(SEASONS).map(([key, config]) => (
                  <button
                    key={key}
                    onClick={() => setActiveSeason(key)}
                    className={`p-3 rounded-2xl text-sm font-bold border-2 transition-all ${
                      activeSeason === key 
                      ? "border-orange-600 bg-orange-50 text-orange-700" 
                      : "border-transparent bg-slate-50 text-slate-600 hover:bg-slate-100"
                    }`}
                  >
                    {config.label}
                  </button>
                ))}
              </div>
            </div>

            <div className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
              <h2 className="text-lg font-bold flex items-center gap-2"><Music size={20} className="text-orange-500"/> 노래 및 가사</h2>
              <div className="space-y-4">
                <div className="space-y-1">
                  <label className="text-xs font-bold text-slate-400 uppercase">곡명</label>
                  <input 
                    type="text" 
                    value={songName}
                    onChange={(e) => setSongName(e.target.value)}
                    className="w-full bg-slate-50 border-none rounded-xl p-3 text-sm focus:ring-2 focus:ring-orange-500 outline-none"
                  />
                </div>
                <div className="space-y-1">
                  <label className="text-xs font-bold text-slate-400 uppercase">가사</label>
                  <textarea 
                    value={lyrics}
                    onChange={(e) => setLyrics(e.target.value)}
                    className="w-full h-32 bg-slate-50 border-none rounded-xl p-3 text-sm focus:ring-2 focus:ring-orange-500 outline-none resize-none"
                  />
                </div>
              </div>
            </div>

            <div className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
              <h2 className="text-lg font-bold flex items-center gap-2"><Type size={20} className="text-orange-500"/> 노출 문구</h2>
              <div className="space-y-4">
                <div className="space-y-1">
                  <label className="text-xs font-bold text-slate-400 uppercase">메인 타이틀</label>
                  <input 
                    type="text" 
                    value={mainTitle}
                    onChange={(e) => setMainTitle(e.target.value)}
                    className="w-full bg-slate-50 border-none rounded-xl p-3 text-sm focus:ring-2 focus:ring-orange-500 outline-none font-bold"
                  />
                </div>
                <div className="space-y-1">
                  <label className="text-xs font-bold text-slate-400 uppercase">강조 배지</label>
                  <input 
                    type="text" 
                    value={hookText}
                    onChange={(e) => setHookText(e.target.value)}
                    className="w-full bg-slate-50 border-none rounded-xl p-3 text-sm focus:ring-2 focus:ring-orange-500 outline-none"
                  />
                </div>
              </div>
            </div>

            <div className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
              <h2 className="text-lg font-bold flex items-center gap-2"><ImageIcon size={20} className="text-orange-500"/> 한유미 이미지 URL</h2>
              <input 
                type="text" 
                value={faceUrl}
                onChange={(e) => setFaceUrl(e.target.value)}
                placeholder="이미지 URL을 입력하세요"
                className="w-full bg-slate-50 border-none rounded-xl p-3 text-sm focus:ring-2 focus:ring-orange-500 outline-none"
              />
            </div>
          </aside>

          <main className="lg:col-span-8 space-y-6">
            <div className="bg-white p-4 rounded-[40px] border border-slate-200 shadow-xl overflow-hidden">
              <div className="relative aspect-video w-full bg-slate-100 rounded-[28px] overflow-hidden border border-slate-100 shadow-inner">
                <canvas 
                  ref={canvasRef} 
                  width={1280} 
                  height={720}
                  className="w-full h-full object-contain"
                />
                {isRendering && (
                  <div className="absolute inset-0 bg-white/60 backdrop-blur-sm flex items-center justify-center">
                    <div className="flex flex-col items-center gap-3">
                      <div className="w-12 h-12 border-4 border-orange-600 border-t-transparent rounded-full animate-spin"></div>
                      <span className="font-extrabold text-slate-800 text-lg">디자인 구성 중...</span>
                    </div>
                  </div>
                )}
              </div>
            </div>

            <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
              <div className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm flex items-center gap-6">
                <div className="relative w-24 h-24 flex items-center justify-center">
                    <svg className="w-full h-full transform -rotate-90">
                        <circle cx="48" cy="48" r="40" stroke="#f1f5f9" strokeWidth="10" fill="none" />
                        <circle 
                            cx="48" cy="48" r="40" stroke="#c2410c" strokeWidth="10" fill="none" 
                            strokeDasharray={251}
                            strokeDashoffset={251 - (251 * ctrScore) / 100}
                            className="transition-all duration-1000 ease-out"
                            strokeLinecap="round"
                        />
                    </svg>
                    <span className="absolute text-2xl font-black text-slate-800">{ctrScore}</span>
                </div>
                <div>
                    <h3 className="text-lg font-bold flex items-center gap-2"><Eye size={18} className="text-orange-500"/> 디자인 성과 예측</h3>
                    <p className="text-sm text-slate-500 mt-1">
                        고퀄리티 가을 이미지가 적용되어 시각적 몰입감이 매우 우수합니다.
                    </p>
                </div>
              </div>

              <div className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-3">
                <h3 className="text-sm font-bold text-slate-400 uppercase tracking-widest flex items-center gap-2">
                    <Layout size={16} /> 실시간 로그
                </h3>
                <div className="space-y-2">
                    {logs.length === 0 ? (
                        <p className="text-xs text-slate-400">명령을 기다리고 있습니다.</p>
                    ) : logs.map(log => (
                        <div key={log.id} className="text-xs flex justify-between items-center bg-slate-50 p-2 rounded-lg border border-slate-100">
                            <span className="font-semibold text-slate-700">{log.msg}</span>
                            <span className="text-slate-400 italic font-mono">{log.time}</span>
                        </div>
                    ))}
                </div>
              </div>
            </div>

            <div className="bg-slate-900 text-white p-8 rounded-[32px] flex flex-col md:flex-row items-center justify-between gap-6 shadow-2xl">
                <div className="text-center md:text-left">
                    <h3 className="text-2xl font-bold">가을 테마 AI 프롬프트</h3>
                    <p className="text-slate-400 text-sm mt-2">이 디자인과 가장 잘 어울리는 배경 제작용 AI 프롬프트입니다.</p>
                </div>
                <button 
                    onClick={getAiPrompt}
                    className="w-full md:w-auto bg-orange-600 text-white px-8 py-4 rounded-2xl font-black hover:bg-orange-500 transition-all flex items-center justify-center gap-2 shadow-lg"
                >
                    <Sparkles size={20} />
                    프롬프트 복사
                </button>
            </div>
          </main>
        </div>

        <footer className="text-center py-8 text-slate-400 text-sm border-t border-slate-200">
          <p>© 2025 Han Yumi AI Singer Brand. Automation Tools.</p>
        </footer>
      </div>
    </div>
  );
};

export default App;
