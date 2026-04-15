<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LinguaFlow — AI Language Tutor</title>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,700;1,400&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
* { box-sizing: border-box; margin: 0; padding: 0; }
:root {
  --bg: #0f0e17; --surface: #1a1929; --card: #1e1d2e;
  --card2: #252338; --accent: #e8c547; --accent2: #7c6af5;
  --text: #fffffe; --muted: #a7a9be; --border: rgba(255,255,255,0.07);
  --success: #52d9a4; --danger: #ff6b6b; --info: #60b4ff;
  --radius: 16px; --radius-sm: 10px;
}
html { scroll-behavior: smooth; }
body { font-family: 'DM Sans', sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; }
::-webkit-scrollbar { width: 6px; } ::-webkit-scrollbar-track { background: var(--bg); } ::-webkit-scrollbar-thumb { background: #333; border-radius: 3px; }

/* LAYOUT */
.app { display: flex; min-height: 100vh; }
.sidebar { width: 240px; min-height: 100vh; background: var(--surface); border-right: 1px solid var(--border); padding: 28px 0; display: flex; flex-direction: column; position: fixed; top: 0; left: 0; bottom: 0; z-index: 10; }
.main { margin-left: 240px; flex: 1; padding: 32px; min-height: 100vh; }

/* SIDEBAR */
.logo { font-family: 'Playfair Display', serif; font-size: 22px; padding: 0 24px 28px; border-bottom: 1px solid var(--border); }
.logo span { color: var(--accent); }
.logo sub { font-size: 10px; font-family: 'DM Sans'; color: var(--muted); letter-spacing: 2px; display: block; margin-top: 2px; }

.lang-section { padding: 20px 16px 12px; }
.lang-label { font-size: 10px; text-transform: uppercase; letter-spacing: 2px; color: var(--muted); padding: 0 8px; margin-bottom: 8px; }
.lang-btn { display: flex; align-items: center; gap: 10px; width: 100%; padding: 10px 12px; border-radius: var(--radius-sm); border: none; background: transparent; color: var(--muted); font-family: 'DM Sans'; font-size: 14px; cursor: pointer; transition: all 0.18s; text-align: left; }
.lang-btn:hover { background: var(--card); color: var(--text); }
.lang-btn.active { background: var(--card2); color: var(--text); font-weight: 500; }
.lang-btn .flag { font-size: 18px; }
.lang-btn .lvl { margin-left: auto; font-size: 10px; background: rgba(124,106,245,0.2); color: var(--accent2); padding: 2px 7px; border-radius: 20px; }

.nav-section { padding: 16px; flex: 1; }
.nav-label { font-size: 10px; text-transform: uppercase; letter-spacing: 2px; color: var(--muted); padding: 0 8px; margin-bottom: 8px; }
.nav-btn { display: flex; align-items: center; gap: 12px; width: 100%; padding: 11px 12px; border-radius: var(--radius-sm); border: none; background: transparent; color: var(--muted); font-family: 'DM Sans'; font-size: 14px; cursor: pointer; transition: all 0.18s; text-align: left; }
.nav-btn:hover { background: var(--card); color: var(--text); }
.nav-btn.active { background: rgba(232,197,71,0.12); color: var(--accent); font-weight: 500; }
.nav-icon { font-size: 16px; width: 20px; text-align: center; }

.streak-badge { margin: 12px 16px 20px; background: linear-gradient(135deg, rgba(232,197,71,0.1), rgba(232,197,71,0.05)); border: 1px solid rgba(232,197,71,0.2); border-radius: var(--radius-sm); padding: 12px 14px; }
.streak-num { font-size: 26px; font-weight: 500; color: var(--accent); }
.streak-lbl { font-size: 11px; color: var(--muted); }
.xp-bar-wrap { height: 4px; background: var(--card); border-radius: 4px; margin-top: 8px; overflow: hidden; }
.xp-bar { height: 100%; background: var(--accent); border-radius: 4px; transition: width 0.5s; }

/* PANELS */
.panel { display: none; animation: fadeIn 0.25s ease; }
.panel.active { display: block; }
@keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: none; } }

/* PAGE HEADER */
.page-header { margin-bottom: 28px; }
.page-title { font-family: 'Playfair Display', serif; font-size: 30px; margin-bottom: 6px; }
.page-sub { color: var(--muted); font-size: 15px; }

/* STAT CARDS */
.stats-row { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-bottom: 28px; }
.stat-card { background: var(--card); border-radius: var(--radius-sm); padding: 16px; border: 1px solid var(--border); }
.stat-num { font-size: 26px; font-weight: 500; }
.stat-lbl { font-size: 12px; color: var(--muted); margin-top: 2px; }

/* FLASHCARDS */
.fc-controls-top { display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px; }
.cat-pills { display: flex; gap: 8px; flex-wrap: wrap; }
.cat-pill { padding: 6px 14px; border-radius: 20px; border: 1px solid var(--border); background: transparent; color: var(--muted); font-size: 12px; cursor: pointer; font-family: 'DM Sans'; transition: all 0.18s; }
.cat-pill.active { background: var(--accent2); color: #fff; border-color: var(--accent2); }
.pill-btn { padding: 8px 18px; border-radius: 10px; border: 1px solid var(--border); background: var(--card); color: var(--text); font-family: 'DM Sans'; font-size: 13px; cursor: pointer; transition: all 0.18s; }
.pill-btn:hover { background: var(--card2); }

.progress-track { display: flex; align-items: center; gap: 12px; margin-bottom: 24px; }
.progress-bar-wrap { flex: 1; height: 5px; background: var(--card); border-radius: 4px; overflow: hidden; }
.progress-bar { height: 100%; background: linear-gradient(90deg, var(--accent2), var(--accent)); border-radius: 4px; transition: width 0.4s; }
.card-counter { font-size: 13px; color: var(--muted); white-space: nowrap; }

.flashcard-wrap { perspective: 1200px; max-width: 520px; margin: 0 auto 24px; }
.flashcard { width: 100%; height: 240px; position: relative; cursor: pointer; transform-style: preserve-3d; transition: transform 0.6s cubic-bezier(0.4,0.2,0.2,1); }
.flashcard.flipped { transform: rotateY(180deg); }
.card-face { position: absolute; inset: 0; backface-visibility: hidden; border-radius: 20px; display: flex; flex-direction: column; justify-content: center; align-items: center; gap: 10px; border: 1px solid var(--border); }
.card-front { background: var(--card2); }
.card-back { background: linear-gradient(135deg, #1a2a3e, #1e2d45); transform: rotateY(180deg); }
.card-label { font-size: 10px; text-transform: uppercase; letter-spacing: 2.5px; color: var(--muted); }
.card-word { font-family: 'Playfair Display', serif; font-size: 48px; line-height: 1.1; }
.card-hint { font-size: 13px; color: var(--muted); }
.card-tag { font-size: 11px; padding: 3px 10px; border-radius: 20px; background: rgba(124,106,245,0.15); color: var(--accent2); }
.card-translation { font-family: 'Playfair Display', serif; font-size: 40px; color: var(--accent); }
.card-roman { font-size: 16px; color: var(--muted); font-style: italic; }
.card-example { font-size: 13px; color: var(--info); text-align: center; max-width: 300px; }

.card-actions { display: flex; gap: 16px; justify-content: center; align-items: center; margin-bottom: 24px; }
.act-btn { display: flex; flex-direction: column; align-items: center; gap: 4px; background: transparent; border: none; cursor: pointer; color: var(--muted); font-family: 'DM Sans'; font-size: 11px; transition: all 0.18s; }
.act-circle { width: 56px; height: 56px; border-radius: 50%; border: 1.5px solid var(--border); background: var(--card); display: flex; align-items: center; justify-content: center; font-size: 22px; transition: all 0.2s; }
.act-btn:hover .act-circle { transform: scale(1.1); }
.act-btn.bad .act-circle { background: rgba(255,107,107,0.12); border-color: var(--danger); }
.act-btn.good .act-circle { background: rgba(82,217,164,0.12); border-color: var(--success); }
.act-btn.speak .act-circle { background: rgba(96,180,255,0.12); border-color: var(--info); }

/* QUIZ */
.quiz-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
.score-pills { display: flex; gap: 12px; }
.score-pill { padding: 6px 16px; border-radius: 20px; font-size: 14px; font-weight: 500; }
.score-pill.right { background: rgba(82,217,164,0.12); color: var(--success); }
.score-pill.wrong { background: rgba(255,107,107,0.12); color: var(--danger); }

.quiz-mode-btns { display: flex; gap: 8px; margin-bottom: 20px; }
.mode-btn { padding: 8px 16px; border-radius: var(--radius-sm); border: 1px solid var(--border); background: transparent; color: var(--muted); font-family: 'DM Sans'; font-size: 13px; cursor: pointer; transition: all 0.18s; }
.mode-btn.active { background: var(--card2); color: var(--text); border-color: var(--accent2); }

.quiz-q { font-family: 'Playfair Display', serif; font-size: 36px; text-align: center; padding: 36px 24px; background: var(--card); border-radius: 20px; margin-bottom: 20px; border: 1px solid var(--border); min-height: 120px; display: flex; align-items: center; justify-content: center; line-height: 1.2; }
.quiz-q small { font-family: 'DM Sans'; font-size: 14px; color: var(--muted); display: block; margin-bottom: 8px; }
.quiz-options { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 16px; }
.quiz-opt { padding: 18px 20px; background: var(--card); border: 1.5px solid var(--border); border-radius: var(--radius-sm); font-size: 15px; cursor: pointer; font-family: 'DM Sans'; color: var(--text); transition: all 0.18s; text-align: left; }
.quiz-opt:hover:not(:disabled) { border-color: var(--accent2); background: rgba(124,106,245,0.08); }
.quiz-opt.correct { border-color: var(--success); background: rgba(82,217,164,0.1); color: var(--success); }
.quiz-opt.wrong { border-color: var(--danger); background: rgba(255,107,107,0.1); color: var(--danger); }
.quiz-opt:disabled { cursor: default; }
.quiz-type-input { width: 100%; padding: 14px 18px; background: var(--card); border: 1.5px solid var(--border); border-radius: var(--radius-sm); font-size: 18px; font-family: 'Playfair Display'; color: var(--text); outline: none; transition: border 0.18s; }
.quiz-type-input:focus { border-color: var(--accent2); }
.quiz-submit { width: 100%; padding: 14px; border-radius: var(--radius-sm); border: none; background: var(--accent2); color: #fff; font-family: 'DM Sans'; font-weight: 500; font-size: 15px; cursor: pointer; transition: opacity 0.18s; margin-bottom: 16px; }
.quiz-submit:hover { opacity: 0.85; }
.quiz-feedback { text-align: center; font-size: 15px; min-height: 28px; padding: 6px; }

/* GRAMMAR */
.grammar-grid { display: grid; gap: 16px; }
.grammar-card { background: var(--card); border-radius: var(--radius); padding: 22px 24px; border: 1px solid var(--border); }
.grammar-card-title { font-family: 'Playfair Display', serif; font-size: 19px; margin-bottom: 6px; display: flex; align-items: center; gap: 10px; }
.badge { font-size: 10px; font-family: 'DM Sans'; padding: 3px 10px; border-radius: 20px; font-weight: 500; letter-spacing: 1px; }
.badge-core { background: rgba(232,197,71,0.15); color: var(--accent); }
.badge-tip { background: rgba(124,106,245,0.15); color: var(--accent2); }
.badge-culture { background: rgba(82,217,164,0.15); color: var(--success); }
.badge-tricky { background: rgba(255,107,107,0.15); color: var(--danger); }
.rule-text { font-size: 14px; line-height: 1.75; color: var(--muted); margin-bottom: 14px; }
.example-box { background: var(--card2); border-radius: var(--radius-sm); padding: 14px 16px; display: flex; gap: 16px; align-items: center; flex-wrap: wrap; font-size: 15px; }
.ex-orig { color: var(--accent); font-family: 'Playfair Display', serif; }
.ex-arrow { color: var(--muted); }
.ex-roman { color: var(--info); font-style: italic; font-size: 13px; }
.ex-trans { color: var(--text); }

.fill-blank-card { background: var(--card); border-radius: var(--radius); padding: 22px 24px; border: 1px solid var(--border); margin-top: 8px; }
.fill-sentence { font-family: 'Playfair Display', serif; font-size: 22px; margin-bottom: 8px; }
.fill-hint { font-size: 13px; color: var(--muted); margin-bottom: 16px; }
.fill-row { display: flex; gap: 10px; align-items: center; flex-wrap: wrap; }
.fill-input { background: var(--card2); border: 1.5px solid var(--border); border-radius: var(--radius-sm); padding: 10px 16px; color: var(--text); font-family: 'DM Sans'; font-size: 15px; width: 180px; outline: none; transition: border 0.18s; }
.fill-input:focus { border-color: var(--accent2); }
.check-btn { padding: 10px 22px; border-radius: var(--radius-sm); border: none; background: var(--accent2); color: #fff; font-family: 'DM Sans'; font-size: 14px; font-weight: 500; cursor: pointer; transition: opacity 0.18s; }
.check-btn:hover { opacity: 0.85; }
.fill-result { font-size: 14px; margin-top: 10px; min-height: 22px; }

/* PHRASEBOOK */
.phrase-cats { display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 20px; }
.phrase-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.phrase-card { background: var(--card); border-radius: var(--radius); padding: 18px 20px; border: 1px solid var(--border); cursor: pointer; transition: border-color 0.18s; display: flex; flex-direction: column; gap: 6px; }
.phrase-card:hover { border-color: var(--accent2); }
.phrase-top { display: flex; justify-content: space-between; align-items: flex-start; }
.phrase-foreign { font-family: 'Playfair Display', serif; font-size: 20px; color: var(--accent); flex: 1; }
.phrase-speak-btn { background: rgba(96,180,255,0.1); border: 1px solid rgba(96,180,255,0.2); color: var(--info); border-radius: 8px; padding: 4px 10px; font-size: 12px; cursor: pointer; font-family: 'DM Sans'; transition: all 0.18s; white-space: nowrap; }
.phrase-speak-btn:hover { background: rgba(96,180,255,0.2); }
.phrase-roman { font-size: 13px; color: var(--muted); font-style: italic; }
.phrase-eng { font-size: 14px; color: var(--text); }
.phrase-ctx { font-size: 12px; color: var(--muted); padding: 4px 10px; background: var(--card2); border-radius: 6px; align-self: flex-start; }

/* AI CONVERSATION */
.chat-layout { display: grid; grid-template-columns: 1fr 320px; gap: 20px; height: calc(100vh - 180px); }
.chat-main { display: flex; flex-direction: column; background: var(--card); border-radius: var(--radius); border: 1px solid var(--border); overflow: hidden; }
.chat-header { padding: 16px 20px; border-bottom: 1px solid var(--border); display: flex; align-items: center; gap: 12px; }
.ai-avatar { width: 36px; height: 36px; border-radius: 50%; background: linear-gradient(135deg, var(--accent2), var(--accent)); display: flex; align-items: center; justify-content: center; font-size: 16px; }
.chat-info h3 { font-size: 15px; font-weight: 500; }
.chat-info p { font-size: 12px; color: var(--muted); }
.chat-scenario-sel { margin-left: auto; }
.scenario-select { background: var(--card2); border: 1px solid var(--border); color: var(--text); font-family: 'DM Sans'; font-size: 13px; padding: 6px 12px; border-radius: 8px; outline: none; cursor: pointer; }

.chat-messages { flex: 1; overflow-y: auto; padding: 20px; display: flex; flex-direction: column; gap: 14px; }
.msg { max-width: 78%; display: flex; flex-direction: column; gap: 4px; }
.msg.user { align-self: flex-end; align-items: flex-end; }
.msg.ai { align-self: flex-start; align-items: flex-start; }
.msg-bubble { padding: 12px 16px; border-radius: 16px; font-size: 14px; line-height: 1.6; }
.msg.ai .msg-bubble { background: var(--card2); border-bottom-left-radius: 4px; }
.msg.user .msg-bubble { background: var(--accent2); color: #fff; border-bottom-right-radius: 4px; }
.msg-time { font-size: 11px; color: var(--muted); }
.msg-correction { font-size: 12px; color: var(--success); background: rgba(82,217,164,0.08); border: 1px solid rgba(82,217,164,0.2); border-radius: 8px; padding: 6px 10px; margin-top: 4px; }
.typing-indicator { display: flex; gap: 4px; align-items: center; padding: 14px 16px; background: var(--card2); border-radius: 16px; border-bottom-left-radius: 4px; width: fit-content; }
.typing-dot { width: 7px; height: 7px; border-radius: 50%; background: var(--muted); animation: typing 1.2s infinite; }
.typing-dot:nth-child(2) { animation-delay: 0.2s; }
.typing-dot:nth-child(3) { animation-delay: 0.4s; }
@keyframes typing { 0%, 60%, 100% { transform: translateY(0); opacity: 0.4; } 30% { transform: translateY(-6px); opacity: 1; } }

.chat-input-row { padding: 16px 20px; border-top: 1px solid var(--border); display: flex; gap: 10px; align-items: flex-end; }
.chat-input { flex: 1; background: var(--card2); border: 1.5px solid var(--border); border-radius: 12px; padding: 12px 16px; color: var(--text); font-family: 'DM Sans'; font-size: 14px; resize: none; outline: none; transition: border 0.18s; max-height: 120px; }
.chat-input:focus { border-color: var(--accent2); }
.send-btn { width: 44px; height: 44px; border-radius: 12px; border: none; background: var(--accent2); color: #fff; font-size: 18px; cursor: pointer; transition: opacity 0.18s; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.send-btn:hover { opacity: 0.85; }
.send-btn:disabled { opacity: 0.4; cursor: default; }

.chat-sidebar { display: flex; flex-direction: column; gap: 14px; }
.chat-side-card { background: var(--card); border-radius: var(--radius); padding: 18px; border: 1px solid var(--border); }
.side-title { font-size: 13px; font-weight: 500; color: var(--muted); text-transform: uppercase; letter-spacing: 1px; margin-bottom: 12px; }
.hint-list { display: flex; flex-direction: column; gap: 8px; }
.hint-item { font-size: 13px; color: var(--text); }
.hint-foreign { color: var(--accent); font-family: 'Playfair Display'; }
.hint-trans { color: var(--muted); }
.vocab-used { display: flex; flex-wrap: wrap; gap: 6px; }
.vocab-chip { font-size: 12px; padding: 4px 10px; background: rgba(82,217,164,0.1); color: var(--success); border-radius: 20px; }
.key-phrases { display: flex; flex-direction: column; gap: 8px; }
.key-phrase { font-size: 13px; }
.key-phrase .kp-f { color: var(--accent); font-family: 'Playfair Display'; }
.key-phrase .kp-t { color: var(--muted); }

/* API KEY MODAL */
.modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.7); display: flex; align-items: center; justify-content: center; z-index: 100; }
.modal-box { background: var(--surface); border-radius: var(--radius); padding: 32px; max-width: 440px; width: 90%; border: 1px solid var(--border); }
.modal-title { font-family: 'Playfair Display', serif; font-size: 22px; margin-bottom: 8px; }
.modal-sub { font-size: 14px; color: var(--muted); margin-bottom: 20px; line-height: 1.6; }
.modal-input { width: 100%; padding: 12px 16px; background: var(--card); border: 1.5px solid var(--border); border-radius: var(--radius-sm); color: var(--text); font-family: 'DM Sans'; font-size: 14px; outline: none; margin-bottom: 12px; transition: border 0.18s; }
.modal-input:focus { border-color: var(--accent2); }
.modal-actions { display: flex; gap: 10px; }
.modal-btn { flex: 1; padding: 12px; border-radius: var(--radius-sm); border: none; font-family: 'DM Sans'; font-size: 14px; font-weight: 500; cursor: pointer; transition: opacity 0.18s; }
.modal-btn.primary { background: var(--accent); color: #0f0e17; }
.modal-btn.secondary { background: var(--card); color: var(--muted); border: 1px solid var(--border); }
.modal-btn:hover { opacity: 0.85; }
.modal-note { font-size: 12px; color: var(--muted); margin-top: 12px; line-height: 1.6; }

/* PROGRESS PAGE */
.progress-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; margin-bottom: 24px; }
.progress-lang-card { background: var(--card); border-radius: var(--radius); padding: 20px; border: 1px solid var(--border); }
.plc-header { display: flex; align-items: center; gap: 10px; margin-bottom: 14px; }
.plc-flag { font-size: 24px; }
.plc-name { font-size: 16px; font-weight: 500; }
.plc-level { margin-left: auto; font-size: 12px; padding: 3px 10px; border-radius: 20px; background: rgba(124,106,245,0.15); color: var(--accent2); }
.plc-stat { display: flex; justify-content: space-between; font-size: 13px; margin-bottom: 6px; }
.plc-stat-lbl { color: var(--muted); }
.plc-bar-wrap { height: 4px; background: var(--card2); border-radius: 4px; overflow: hidden; margin-top: 2px; margin-bottom: 10px; }
.plc-bar { height: 100%; border-radius: 4px; }

.achievements-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; }
.achievement { background: var(--card); border-radius: var(--radius-sm); padding: 16px; border: 1px solid var(--border); text-align: center; }
.achievement.locked { opacity: 0.4; }
.ach-icon { font-size: 28px; margin-bottom: 8px; }
.ach-name { font-size: 13px; font-weight: 500; }
.ach-desc { font-size: 11px; color: var(--muted); margin-top: 3px; }

/* RESPONSIVE */
@media (max-width: 900px) {
  .sidebar { width: 200px; }
  .main { margin-left: 200px; }
  .stats-row { grid-template-columns: repeat(2, 1fr); }
  .phrase-grid { grid-template-columns: 1fr; }
  .chat-layout { grid-template-columns: 1fr; }
  .chat-sidebar { display: none; }
}
@media (max-width: 640px) {
  .sidebar { position: relative; width: 100%; min-height: auto; flex-direction: row; flex-wrap: wrap; }
  .main { margin-left: 0; padding: 16px; }
}
</style>
</head>
<body>
<div class="app">

<!-- SIDEBAR -->
<aside class="sidebar">
  <div class="logo">Lingua<span>Flow</span><sub>AI LANGUAGE TUTOR</sub></div>

  <div class="lang-section">
    <div class="lang-label">Learning</div>
    <button class="lang-btn active" data-lang="japanese"><span class="flag">🇯🇵</span> Japanese <span class="lvl" id="lvl-japanese">A1</span></button>
    <button class="lang-btn" data-lang="spanish"><span class="flag">🇪🇸</span> Spanish <span class="lvl" id="lvl-spanish">A1</span></button>
    <button class="lang-btn" data-lang="french"><span class="flag">🇫🇷</span> French <span class="lvl" id="lvl-french">A1</span></button>
    <button class="lang-btn" data-lang="swahili"><span class="flag">🇰🇪</span> Swahili <span class="lvl" id="lvl-swahili">A1</span></button>
    <button class="lang-btn" data-lang="arabic"><span class="flag">🇸🇦</span> Arabic <span class="lvl" id="lvl-arabic">A1</span></button>
    <button class="lang-btn" data-lang="mandarin"><span class="flag">🇨🇳</span> Mandarin <span class="lvl" id="lvl-mandarin">A1</span></button>
  </div>

  <div class="nav-section">
    <div class="nav-label">Practice</div>
    <button class="nav-btn active" data-tab="flashcards"><span class="nav-icon">🃏</span> Flashcards</button>
    <button class="nav-btn" data-tab="quiz"><span class="nav-icon">❓</span> Quiz</button>
    <button class="nav-btn" data-tab="grammar"><span class="nav-icon">📖</span> Grammar</button>
    <button class="nav-btn" data-tab="phrases"><span class="nav-icon">💬</span> Phrasebook</button>
    <button class="nav-btn" data-tab="conversation"><span class="nav-icon">🤖</span> AI Conversation</button>
    <button class="nav-btn" data-tab="progress"><span class="nav-icon">📊</span> My Progress</button>
  </div>

  <div class="streak-badge">
    <div class="streak-num" id="streakNum">0</div>
    <div class="streak-lbl">day streak 🔥</div>
    <div class="xp-bar-wrap"><div class="xp-bar" id="xpBar" style="width:0%"></div></div>
    <div style="font-size:11px;color:var(--muted);margin-top:4px;"><span id="xpNum">0</span> / <span id="xpMax">100</span> XP</div>
  </div>
</aside>

<!-- MAIN -->
<main class="main">

  <!-- FLASHCARDS -->
  <div class="panel active" id="panel-flashcards">
    <div class="page-header">
      <div class="page-title">Vocabulary Flashcards</div>
      <div class="page-sub">Tap a card to reveal the translation. Mark yourself honestly!</div>
    </div>
    <div class="fc-controls-top">
      <div class="cat-pills" id="catPills"></div>
      <div style="display:flex;gap:8px;">
        <button class="pill-btn" id="shuffleBtn">⇄ Shuffle</button>
        <button class="pill-btn" id="speakWordBtn">🔊 Hear</button>
      </div>
    </div>
    <div class="progress-track">
      <div class="progress-bar-wrap"><div class="progress-bar" id="progressBar" style="width:0%"></div></div>
      <span class="card-counter" id="cardCounter">1 / 10</span>
    </div>
    <div class="flashcard-wrap">
      <div class="flashcard" id="flashcard">
        <div class="card-face card-front">
          <span class="card-label">tap to flip</span>
          <span class="card-word" id="cardWord">—</span>
          <span class="card-hint" id="cardHint"></span>
          <span class="card-tag" id="cardTag"></span>
        </div>
        <div class="card-face card-back">
          <span class="card-label">translation</span>
          <span class="card-translation" id="cardTrans">—</span>
          <span class="card-roman" id="cardRoman"></span>
          <span class="card-example" id="cardExample"></span>
        </div>
      </div>
    </div>
    <div class="card-actions">
      <button class="act-btn bad" id="badBtn">
        <div class="act-circle">✗</div>
        <span>Again</span>
      </button>
      <button class="act-btn speak" id="flipBtn">
        <div class="act-circle">↺</div>
        <span>Flip</span>
      </button>
      <button class="act-btn good" id="goodBtn">
        <div class="act-circle">✓</div>
        <span>Got it</span>
      </button>
    </div>
  </div>

  <!-- QUIZ -->
  <div class="panel" id="panel-quiz">
    <div class="quiz-header">
      <div>
        <div class="page-title">Quiz</div>
        <div class="page-sub">Test your knowledge</div>
      </div>
      <div class="score-pills">
        <div class="score-pill right">✓ <span id="scoreRight">0</span></div>
        <div class="score-pill wrong">✗ <span id="scoreWrong">0</span></div>
      </div>
    </div>
    <div class="quiz-mode-btns">
      <button class="mode-btn active" data-mode="mc">Multiple Choice</button>
      <button class="mode-btn" data-mode="type">Type Answer</button>
      <button class="mode-btn" data-mode="reverse">Reverse (Translate back)</button>
    </div>
    <div class="quiz-q" id="quizQuestion">Loading…</div>
    <div id="quizBody"></div>
    <div class="quiz-feedback" id="quizFeedback"></div>
  </div>

  <!-- GRAMMAR -->
  <div class="panel" id="panel-grammar">
    <div class="page-header">
      <div class="page-title">Grammar Guide</div>
      <div class="page-sub" id="grammarSub">Key rules and structures</div>
    </div>
    <div class="grammar-grid" id="grammarContent"></div>
    <div id="fillBlankArea" style="margin-top:16px;"></div>
  </div>

  <!-- PHRASES -->
  <div class="panel" id="panel-phrases">
    <div class="page-header">
      <div class="page-title">Phrasebook</div>
      <div class="page-sub">Essential phrases with pronunciation</div>
    </div>
    <div class="phrase-cats" id="phraseCats"></div>
    <div class="phrase-grid" id="phraseGrid"></div>
  </div>

  <!-- AI CONVERSATION -->
  <div class="panel" id="panel-conversation">
    <div class="page-header">
      <div class="page-title">AI Conversation Practice</div>
      <div class="page-sub">Chat with an AI tutor — it corrects your grammar gently</div>
    </div>
    <div class="chat-layout">
      <div class="chat-main">
        <div class="chat-header">
          <div class="ai-avatar">🤖</div>
          <div class="chat-info">
            <h3>LinguaFlow Tutor</h3>
            <p id="chatLangLabel">Powered by Claude AI</p>
          </div>
          <div class="chat-scenario-sel">
            <select class="scenario-select" id="scenarioSelect">
              <option value="general">General conversation</option>
              <option value="restaurant">At a restaurant</option>
              <option value="travel">Traveling / directions</option>
              <option value="shopping">Shopping</option>
              <option value="intro">Self-introduction</option>
            </select>
          </div>
        </div>
        <div class="chat-messages" id="chatMessages">
          <div class="msg ai">
            <div class="msg-bubble">Hi! I'm your LinguaFlow tutor. Enter your Anthropic API key to start practicing conversation. I'll respond in your target language, gently correct mistakes, and help you learn naturally! 🌍</div>
          </div>
        </div>
        <div class="chat-input-row">
          <textarea class="chat-input" id="chatInput" placeholder="Type in any language — I'll guide you…" rows="1"></textarea>
          <button class="send-btn" id="sendBtn" title="Send">➤</button>
        </div>
      </div>
      <div class="chat-sidebar">
        <div class="chat-side-card">
          <div class="side-title">Useful phrases</div>
          <div class="key-phrases" id="scenarioPhrases"></div>
        </div>
        <div class="chat-side-card">
          <div class="side-title">Vocabulary used</div>
          <div class="vocab-used" id="vocabUsed"><span style="font-size:13px;color:var(--muted);">Words will appear here as you chat…</span></div>
        </div>
        <div class="chat-side-card">
          <div class="side-title">API Key</div>
          <input type="password" class="fill-input" style="width:100%;margin-bottom:10px;" id="apiKeyInput" placeholder="sk-ant-…">
          <button class="check-btn" style="width:100%;" id="saveApiKey">Save Key</button>
          <div style="font-size:11px;color:var(--muted);margin-top:8px;line-height:1.6;">Your key is stored locally in your browser and never sent anywhere except the Anthropic API.</div>
        </div>
      </div>
    </div>
  </div>

  <!-- PROGRESS -->
  <div class="panel" id="panel-progress">
    <div class="page-header">
      <div class="page-title">My Progress</div>
      <div class="page-sub">Track your learning journey</div>
    </div>
    <div class="stats-row">
      <div class="stat-card"><div class="stat-num" id="totalCards" style="color:var(--accent)">0</div><div class="stat-lbl">Cards studied</div></div>
      <div class="stat-card"><div class="stat-num" id="totalCorrect" style="color:var(--success)">0</div><div class="stat-lbl">Quiz correct</div></div>
      <div class="stat-card"><div class="stat-num" id="totalXP" style="color:var(--accent2)">0</div><div class="stat-lbl">Total XP</div></div>
      <div class="stat-card"><div class="stat-num" id="totalStreak" style="color:var(--danger)">0</div><div class="stat-lbl">Best streak</div></div>
    </div>
    <div class="progress-grid" id="progressGrid"></div>
    <div style="margin-bottom:16px;"><div class="page-title" style="font-size:20px;margin-bottom:12px;">Achievements</div>
    <div class="achievements-grid" id="achievementsGrid"></div></div>
    <button class="check-btn" id="resetProgress" style="background:rgba(255,107,107,0.15);color:var(--danger);border:1px solid rgba(255,107,107,0.2);">Reset All Progress</button>
  </div>

</main>
</div>

<script>
// ===================== DATA =====================
const DATA = {
  japanese: {
    name: "Japanese", flag: "🇯🇵", voice: "ja-JP",
    categories: ["Greetings","Food","Numbers","Travel","Shopping","Time"],
    grammar: [
      {title:"Sentence Structure (SOV)",badge:"core",rule:"Japanese follows Subject-Object-Verb order. The verb always comes last in the sentence, which is the opposite of English.",examples:[{orig:"私はりんごを食べます",roman:"Watashi wa ringo wo tabemasu",trans:"I eat an apple (lit. I apple eat)"}]},
      {title:"Particles",badge:"core",rule:"Particles mark the grammatical role of words. は (wa) = topic marker, が (ga) = subject, を (wo) = object, に (ni) = direction/time, で (de) = location/means, の (no) = possession.",examples:[{orig:"駅に行きます。電車で行きます。",roman:"Eki ni ikimasu. Densha de ikimasu.",trans:"I go to the station. I go by train."}]},
      {title:"Politeness Levels",badge:"culture",rule:"Japanese has formal (ます masu / です desu) and informal (dictionary form) speech. Always use formal speech with strangers and superiors.",examples:[{orig:"食べます (formal) / 食べる (casual)",roman:"Tabemasu / Taberu",trans:"To eat (formal/casual)"}]},
      {title:"Adjective Types",badge:"tip",rule:"i-adjectives end in い and conjugate directly. na-adjectives need な before nouns. Both have past and negative forms.",examples:[{orig:"大きい犬 / 元気な人",roman:"Ookii inu / Genki na hito",trans:"Big dog / Energetic person"}]},
      {title:"Verb Groups",badge:"tip",rule:"Group 1 (u-verbs): stems end in consonant. Group 2 (ru-verbs): stems end in vowel. Irregular: する (suru) & くる (kuru). Conjugation rules differ per group.",examples:[{orig:"書く→書きます / 食べる→食べます",roman:"Kaku→Kakimasu / Taberu→Tabemasu",trans:"Write / Eat (formal)"}]},
    ],
    fillBlank: {sentence:"私は毎日__で勉強します", answer:"図書館", hint:"I study every day at the ___ (library)"},
    vocab: {
      Greetings: [
        {word:"こんにちは",roman:"Konnichiwa",trans:"Hello",hint:"daytime",example:"こんにちは、田中さん！(Hello, Tanaka-san!)"},
        {word:"おはようございます",roman:"Ohayou gozaimasu",trans:"Good morning",hint:"formal",example:"Use until about 10am"},
        {word:"こんばんは",roman:"Konbanwa",trans:"Good evening",hint:"after sunset",example:"こんばんは！お疲れ様。"},
        {word:"ありがとうございます",roman:"Arigatou gozaimasu",trans:"Thank you very much",hint:"formal",example:"ありがとうございます、助かりました。"},
        {word:"すみません",roman:"Sumimasen",trans:"Excuse me / Sorry",hint:"multipurpose",example:"すみません、駅はどこですか？"},
        {word:"はじめまして",roman:"Hajimemashite",trans:"Nice to meet you",hint:"first meeting",example:"はじめまして、ジョンです。"},
        {word:"お願いします",roman:"Onegai shimasu",trans:"Please (requesting)",hint:"polite request",example:"水をお願いします。"},
        {word:"どういたしまして",roman:"Dou itashimashite",trans:"You're welcome",hint:"formal response",example:"Response to ありがとう"},
      ],
      Food: [
        {word:"ラーメン",roman:"Raamen",trans:"Ramen noodles",hint:"popular dish",example:"一杯のラーメンをください。"},
        {word:"寿司",roman:"Sushi",trans:"Sushi",hint:"vinegared rice",example:"お寿司が食べたいです。"},
        {word:"おいしい",roman:"Oishii",trans:"Delicious",hint:"i-adjective",example:"これはとてもおいしいです！"},
        {word:"水",roman:"Mizu",trans:"Water",hint:"essential",example:"水をください。"},
        {word:"ビール",roman:"Biiru",trans:"Beer",hint:"katakana",example:"冷たいビールをお願いします。"},
        {word:"食べる",roman:"Taberu",trans:"To eat",hint:"ru-verb",example:"何を食べますか？"},
        {word:"飲む",roman:"Nomu",trans:"To drink",hint:"u-verb",example:"お茶を飲みますか？"},
        {word:"お腹が空いた",roman:"Onaka ga suita",trans:"I'm hungry",hint:"expression",example:"もう食べましょう、お腹が空いた！"},
      ],
      Numbers: [
        {word:"一 (いち)",roman:"Ichi",trans:"One",hint:"",example:"一つください。"},
        {word:"二 (に)",roman:"Ni",trans:"Two",hint:"",example:"二人です。"},
        {word:"三 (さん)",roman:"San",trans:"Three",hint:"",example:"三時に会いましょう。"},
        {word:"四 (し/よん)",roman:"Shi / Yon",trans:"Four",hint:"two readings",example:"よん is safer (し = death)"},
        {word:"五 (ご)",roman:"Go",trans:"Five",hint:"",example:"五百円。"},
        {word:"十 (じゅう)",roman:"Juu",trans:"Ten",hint:"",example:"十分待ってください。"},
        {word:"百 (ひゃく)",roman:"Hyaku",trans:"Hundred",hint:"",example:"二百円。"},
        {word:"千 (せん)",roman:"Sen",trans:"Thousand",hint:"",example:"三千円です。"},
      ],
      Travel: [
        {word:"駅",roman:"Eki",trans:"Train station",hint:"essential",example:"駅はどこですか？"},
        {word:"電車",roman:"Densha",trans:"Train",hint:"",example:"電車で行きます。"},
        {word:"右",roman:"Migi",trans:"Right",hint:"direction",example:"右に曲がってください。"},
        {word:"左",roman:"Hidari",trans:"Left",hint:"direction",example:"左に行きます。"},
        {word:"どこ",roman:"Doko",trans:"Where",hint:"question word",example:"お手洗いはどこですか？"},
        {word:"ホテル",roman:"Hoteru",trans:"Hotel",hint:"katakana",example:"ホテルを予約しました。"},
        {word:"チケット",roman:"Chiketto",trans:"Ticket",hint:"katakana",example:"チケットを買いたいです。"},
        {word:"地図",roman:"Chizu",trans:"Map",hint:"",example:"地図を見せてください。"},
      ],
      Shopping: [
        {word:"いくらですか",roman:"Ikura desu ka",trans:"How much is it?",hint:"shopping key phrase",example:"これはいくらですか？"},
        {word:"高い",roman:"Takai",trans:"Expensive",hint:"i-adj",example:"ちょっと高いですね。"},
        {word:"安い",roman:"Yasui",trans:"Cheap",hint:"i-adj",example:"安くてよかった！"},
        {word:"買う",roman:"Kau",trans:"To buy",hint:"u-verb",example:"これを買います。"},
        {word:"クレジットカード",roman:"Kurejitto kaado",trans:"Credit card",hint:"katakana",example:"クレジットカードで払えますか？"},
        {word:"レシート",roman:"Reshiito",trans:"Receipt",hint:"katakana",example:"レシートをください。"},
      ],
      Time: [
        {word:"今",roman:"Ima",trans:"Now",hint:"time",example:"今、何時ですか？"},
        {word:"今日",roman:"Kyou",trans:"Today",hint:"",example:"今日はいい天気ですね。"},
        {word:"明日",roman:"Ashita",trans:"Tomorrow",hint:"",example:"明日また来ます。"},
        {word:"昨日",roman:"Kinou",trans:"Yesterday",hint:"",example:"昨日は何をしましたか？"},
        {word:"朝",roman:"Asa",trans:"Morning",hint:"",example:"朝ごはんを食べましたか？"},
        {word:"夜",roman:"Yoru",trans:"Night",hint:"",example:"夜は何をしますか？"},
      ],
    },
    phrases: {
      Greetings: [
        {foreign:"よろしくお願いします",roman:"Yoroshiku onegai shimasu",eng:"Please treat me well / Nice to meet you",ctx:"Formal/professional introduction"},
        {foreign:"お元気ですか？",roman:"Ogenki desu ka?",eng:"How are you? (formal)",ctx:"Polite inquiry"},
        {foreign:"元気です、ありがとう！",roman:"Genki desu, arigatou!",eng:"I'm fine, thank you!",ctx:"Response"},
        {foreign:"またね！",roman:"Mata ne!",eng:"See you later! (casual)",ctx:"Informal goodbye"},
        {foreign:"お休みなさい",roman:"Oyasumi nasai",eng:"Good night",ctx:"Before sleeping"},
      ],
      Restaurant: [
        {foreign:"すみません、メニューをください",roman:"Sumimasen, menyuu wo kudasai",eng:"Excuse me, please give me the menu",ctx:"Ordering at a restaurant"},
        {foreign:"これをください",roman:"Kore wo kudasai",eng:"I'll have this one please",ctx:"Pointing at menu"},
        {foreign:"おすすめは何ですか？",roman:"Osusume wa nan desu ka?",eng:"What do you recommend?",ctx:"Asking the server"},
        {foreign:"お会計をお願いします",roman:"Okaikei wo onegai shimasu",eng:"The bill, please",ctx:"Requesting check"},
        {foreign:"とてもおいしかったです！",roman:"Totemo oishikatta desu!",eng:"It was very delicious!",ctx:"Complimenting food"},
      ],
      Shopping: [
        {foreign:"これはいくらですか？",roman:"Kore wa ikura desu ka?",eng:"How much is this?",ctx:"Asking price"},
        {foreign:"試着できますか？",roman:"Shichaku dekimasu ka?",eng:"Can I try this on?",ctx:"Clothing shops"},
        {foreign:"別のサイズはありますか？",roman:"Betsu no saizu wa arimasu ka?",eng:"Do you have a different size?",ctx:"Shopping"},
        {foreign:"これをください",roman:"Kore wo kudasai",eng:"I'll take this one",ctx:"Purchasing"},
      ],
      Emergency: [
        {foreign:"助けてください！",roman:"Tasukete kudasai!",eng:"Please help me!",ctx:"Emergency"},
        {foreign:"警察を呼んでください",roman:"Keisatsu wo yonde kudasai",eng:"Please call the police",ctx:"Safety emergency"},
        {foreign:"病院はどこですか？",roman:"Byouin wa doko desu ka?",eng:"Where is the hospital?",ctx:"Medical emergency"},
        {foreign:"アレルギーがあります",roman:"Arerugii ga arimasu",eng:"I have an allergy",ctx:"Medical info"},
      ],
      Travel: [
        {foreign:"～行きの電車はどれですか？",roman:"~ yuki no densha wa dore desu ka?",eng:"Which train goes to ~?",ctx:"Public transport"},
        {foreign:"次の駅はどこですか？",roman:"Tsugi no eki wa doko desu ka?",eng:"What is the next station?",ctx:"On the train"},
        {foreign:"チェックインをお願いします",roman:"Chekkuin wo onegai shimasu",eng:"I'd like to check in",ctx:"Hotel arrival"},
      ],
    },
    scenarios: {
      general: [{kp_f:"調子はどうですか？",kp_t:"How are you doing?"},{kp_f:"好きなものは何ですか？",kp_t:"What do you like?"},{kp_f:"どこから来ましたか？",kp_t:"Where are you from?"}],
      restaurant: [{kp_f:"何にしますか？",kp_t:"What will you have?"},{kp_f:"おすすめは何ですか？",kp_t:"What do you recommend?"},{kp_f:"お会計をお願いします",kp_t:"The bill, please"}],
      travel: [{kp_f:"駅はどこですか？",kp_t:"Where is the station?"},{kp_f:"右に曲がってください",kp_t:"Please turn right"},{kp_f:"チケットはいくらですか？",kp_t:"How much is a ticket?"}],
      shopping: [{kp_f:"いくらですか？",kp_t:"How much is it?"},{kp_f:"試着できますか？",kp_t:"Can I try it on?"},{kp_f:"これをください",kp_t:"I'll take this"}],
      intro: [{kp_f:"はじめまして",kp_t:"Nice to meet you"},{kp_f:"どこから来ましたか？",kp_t:"Where are you from?"},{kp_f:"日本語を勉強しています",kp_t:"I am studying Japanese"}],
    }
  },
  spanish: {
    name: "Spanish", flag: "🇪🇸", voice: "es-ES",
    categories: ["Greetings","Food","Numbers","Travel","Shopping","Time"],
    grammar: [
      {title:"Gender of Nouns",badge:"core",rule:"Every Spanish noun is masculine or feminine. Generally -o = masculine (el), -a = feminine (la). Articles and adjectives must agree in gender and number.",examples:[{orig:"el libro / la casa / los libros / las casas",roman:"",trans:"the book / the house / the books / the houses"}]},
      {title:"Verb Conjugation",badge:"core",rule:"Spanish verbs change endings for each pronoun. -AR verbs: hablo, hablas, habla, hablamos, habláis, hablan. Learn the pattern for -ER and -IR too.",examples:[{orig:"Yo hablo, tú hablas, él habla",roman:"",trans:"I speak, you speak, he speaks"}]},
      {title:"Ser vs Estar",badge:"tricky",rule:"Both mean 'to be'. SER = permanent identity (origin, profession, personality). ESTAR = temporary state or location.",examples:[{orig:"Soy médico. / Estoy cansado.",roman:"",trans:"I am a doctor. / I am tired."}]},
      {title:"Por vs Para",badge:"tricky",rule:"Both mean 'for' but differ: POR = cause/exchange/duration. PARA = purpose/destination/deadline.",examples:[{orig:"Te llamo por ti. / Esto es para ti.",roman:"",trans:"I call because of you. / This is for you."}]},
      {title:"Subjunctive Mood",badge:"tip",rule:"The subjunctive expresses doubt, desire, emotion, and hypotheticals. Triggered by expressions like 'quiero que', 'es posible que', 'ojalá'.",examples:[{orig:"Quiero que vengas.",roman:"",trans:"I want you to come (subjunctive: vengas not vienes)"}]},
    ],
    fillBlank: {sentence:"Yo __ estudiando español desde hace dos años", answer:"estoy", hint:"Use estar (not ser) for ongoing actions"},
    vocab: {
      Greetings: [
        {word:"Hola",roman:"",trans:"Hello",hint:"universal",example:"¡Hola! ¿Cómo estás?"},
        {word:"Buenos días",roman:"",trans:"Good morning",hint:"until noon",example:"Buenos días, ¿en qué puedo ayudarle?"},
        {word:"Buenas tardes",roman:"",trans:"Good afternoon",hint:"noon-evening",example:"Buenas tardes, señor."},
        {word:"Gracias",roman:"",trans:"Thank you",hint:"",example:"Muchas gracias por su ayuda."},
        {word:"Por favor",roman:"",trans:"Please",hint:"polite",example:"Un café, por favor."},
        {word:"Lo siento",roman:"",trans:"I'm sorry",hint:"apology",example:"Lo siento mucho."},
        {word:"De nada",roman:"",trans:"You're welcome",hint:"response",example:"—Gracias. —De nada."},
        {word:"Mucho gusto",roman:"",trans:"Pleased to meet you",hint:"introduction",example:"Mucho gusto, me llamo Carlos."},
      ],
      Food: [
        {word:"La paella",roman:"",trans:"Paella",hint:"rice dish",example:"Quiero probar la paella."},
        {word:"El agua",roman:"",trans:"Water",hint:"fem. exception",example:"Una botella de agua, por favor."},
        {word:"Delicioso",roman:"",trans:"Delicious",hint:"adjective",example:"¡Está delicioso!"},
        {word:"La cuenta",roman:"",trans:"The bill",hint:"restaurant",example:"¿Me puede traer la cuenta?"},
        {word:"Comer",roman:"",trans:"To eat",hint:"-er verb",example:"¿Dónde comemos?"},
        {word:"Beber",roman:"",trans:"To drink",hint:"-er verb",example:"¿Qué quieres beber?"},
        {word:"Tengo hambre",roman:"",trans:"I'm hungry",hint:"expression",example:"¡Tengo mucha hambre!"},
        {word:"El menú",roman:"",trans:"The menu",hint:"",example:"¿Me trae el menú?"},
      ],
      Numbers: [
        {word:"Uno",roman:"",trans:"One",hint:"",example:"Dame uno."},
        {word:"Dos",roman:"",trans:"Two",hint:"",example:"Dos cafés, por favor."},
        {word:"Tres",roman:"",trans:"Three",hint:"",example:"Tres personas."},
        {word:"Diez",roman:"",trans:"Ten",hint:"",example:"Diez minutos."},
        {word:"Cien",roman:"",trans:"One hundred",hint:"",example:"Cien euros."},
        {word:"Mil",roman:"",trans:"One thousand",hint:"",example:"Mil personas."},
      ],
      Travel: [
        {word:"El aeropuerto",roman:"",trans:"Airport",hint:"",example:"¿Cómo llego al aeropuerto?"},
        {word:"La derecha",roman:"",trans:"Right",hint:"direction",example:"Gire a la derecha."},
        {word:"La izquierda",roman:"",trans:"Left",hint:"direction",example:"Está a la izquierda."},
        {word:"¿Dónde está?",roman:"",trans:"Where is it?",hint:"question",example:"¿Dónde está el baño?"},
        {word:"El billete",roman:"",trans:"Ticket",hint:"Spain",example:"Quiero comprar un billete."},
        {word:"El pasaporte",roman:"",trans:"Passport",hint:"travel document",example:"Necesito mi pasaporte."},
      ],
      Shopping: [
        {word:"¿Cuánto cuesta?",roman:"",trans:"How much does it cost?",hint:"",example:"¿Cuánto cuesta esta camisa?"},
        {word:"Barato",roman:"",trans:"Cheap",hint:"adjective",example:"¡Qué barato!"},
        {word:"Caro",roman:"",trans:"Expensive",hint:"adjective",example:"Es un poco caro."},
        {word:"Comprar",roman:"",trans:"To buy",hint:"-ar verb",example:"¿Qué quieres comprar?"},
        {word:"La tarjeta",roman:"",trans:"Card",hint:"payment",example:"¿Aceptan tarjeta?"},
      ],
      Time: [
        {word:"Ahora",roman:"",trans:"Now",hint:"",example:"Lo necesito ahora."},
        {word:"Hoy",roman:"",trans:"Today",hint:"",example:"¿Qué haces hoy?"},
        {word:"Mañana",roman:"",trans:"Tomorrow",hint:"",example:"Te llamo mañana."},
        {word:"Ayer",roman:"",trans:"Yesterday",hint:"",example:"Ayer fui al mercado."},
      ],
    },
    phrases: {
      Greetings:[{foreign:"¿Cómo te llamas?",roman:"",eng:"What is your name?",ctx:"Introduction"},{foreign:"Me llamo...",roman:"",eng:"My name is...",ctx:"Introducing yourself"},{foreign:"¿De dónde eres?",roman:"",eng:"Where are you from?",ctx:"Getting to know someone"},{foreign:"¡Hasta luego!",roman:"",eng:"See you later!",ctx:"Goodbye"}],
      Restaurant:[{foreign:"Una mesa para dos, por favor",roman:"",eng:"A table for two, please",ctx:"Restaurant entry"},{foreign:"¿Qué recomienda?",roman:"",eng:"What do you recommend?",ctx:"Asking waiter"},{foreign:"Sin gluten, por favor",roman:"",eng:"Gluten-free, please",ctx:"Dietary needs"},{foreign:"¡Está buenísimo!",roman:"",eng:"It's absolutely delicious!",ctx:"Compliment"}],
      Emergency:[{foreign:"¡Ayúdeme, por favor!",roman:"",eng:"Please help me!",ctx:"Emergency"},{foreign:"Llame a la policía",roman:"",eng:"Call the police",ctx:"Safety"},{foreign:"Necesito un médico",roman:"",eng:"I need a doctor",ctx:"Medical"}],
      Travel:[{foreign:"¿A qué hora sale el tren?",roman:"",eng:"What time does the train leave?",ctx:"Transport"},{foreign:"¿Cuánto tarda?",roman:"",eng:"How long does it take?",ctx:"Travel time"},{foreign:"Me he perdido",roman:"",eng:"I'm lost",ctx:"Navigation"}],
    },
    scenarios:{general:[{kp_f:"¿Qué te gusta hacer?",kp_t:"What do you like to do?"},{kp_f:"¿Cuántos años tienes?",kp_t:"How old are you?"},{kp_f:"¿Hablas inglés?",kp_t:"Do you speak English?"}],restaurant:[{kp_f:"¿Qué me recomienda?",kp_t:"What do you recommend?"},{kp_f:"Sin carne, por favor",kp_t:"No meat, please"},{kp_f:"La cuenta, por favor",kp_t:"The bill, please"}],travel:[{kp_f:"¿Dónde está la parada?",kp_t:"Where is the stop?"},{kp_f:"¿Cuánto cuesta el billete?",kp_t:"How much is the ticket?"},{kp_f:"Necesito un taxi",kp_t:"I need a taxi"}],shopping:[{kp_f:"¿Tiene otra talla?",kp_t:"Do you have another size?"},{kp_f:"¿Puedo probármelo?",kp_t:"Can I try it on?"},{kp_f:"¿Dónde está la caja?",kp_t:"Where is the checkout?"}],intro:[{kp_f:"Soy de...",kp_t:"I am from..."},{kp_f:"Estudio español",kp_t:"I am studying Spanish"},{kp_f:"Encantado/a de conocerte",kp_t:"Nice to meet you"}]},
  },
  french: {
    name: "French", flag: "🇫🇷", voice: "fr-FR",
    categories: ["Greetings","Food","Numbers","Travel","Shopping","Time"],
    grammar: [
      {title:"Gender & Articles",badge:"core",rule:"Every French noun has a gender. Masculine uses le/un, feminine uses la/une. The plural is les/des. Gender must be learned with each word — there are few rules.",examples:[{orig:"le garçon / la fille / les enfants",roman:"",trans:"the boy / the girl / the children"}]},
      {title:"Negation: ne…pas",badge:"core",rule:"To make a sentence negative, sandwich the verb with ne … pas. In spoken French, the 'ne' is often dropped.",examples:[{orig:"Je ne parle pas français.",roman:"",trans:"I don't speak French. (spoken: 'Je parle pas')"}]},
      {title:"Liaison",badge:"tip",rule:"When a word ending in a consonant is followed by a word beginning with a vowel, the final consonant is pronounced linked to the next word.",examples:[{orig:"Vous avez → 'voo-za-vay'",roman:"",trans:"You have (the 's' in 'vous' is voiced)"}]},
      {title:"Être vs Avoir",badge:"tricky",rule:"Many verbs take avoir in compound tenses, but verbs of motion/state (DR MRS VANDERTRAMP + reflexives) take être. The past participle agrees with être.",examples:[{orig:"J'ai mangé / Je suis allé(e)",roman:"",trans:"I ate / I went"}]},
      {title:"Subjunctive",badge:"tip",rule:"Used after expressions of emotion, doubt, necessity, or desire: il faut que, je veux que, bien que, etc.",examples:[{orig:"Il faut que tu viennes.",roman:"",trans:"It is necessary that you come."}]},
    ],
    fillBlank: {sentence:"Je __ allé au marché hier matin", answer:"suis", hint:"Verbs of motion use être (not avoir) in passé composé"},
    vocab: {
      Greetings:[{word:"Bonjour",roman:"",trans:"Hello / Good day",hint:"formal",example:"Bonjour, comment puis-je vous aider?"},{word:"Bonsoir",roman:"",trans:"Good evening",hint:"after dark",example:"Bonsoir, une table pour deux?"},{word:"Merci",roman:"",trans:"Thank you",hint:"",example:"Merci beaucoup!"},{word:"S'il vous plaît",roman:"",trans:"Please (formal)",hint:"formal",example:"Un café, s'il vous plaît."},{word:"Excusez-moi",roman:"",trans:"Excuse me (formal)",hint:"",example:"Excusez-moi, où sont les toilettes?"},{word:"Pardon",roman:"",trans:"Pardon / Sorry",hint:"informal",example:"Pardon, je ne comprends pas."},{word:"De rien",roman:"",trans:"You're welcome",hint:"response",example:"—Merci. —De rien!"},{word:"Enchanté(e)",roman:"",trans:"Delighted to meet you",hint:"introduction",example:"Enchanté, je m'appelle Marie."}],
      Food:[{word:"Le croissant",roman:"",trans:"Croissant",hint:"pastry",example:"Un croissant, s'il vous plaît."},{word:"L'eau (f.)",roman:"",trans:"Water",hint:"feminine",example:"De l'eau gazeuse ou plate?"},{word:"Délicieux",roman:"",trans:"Delicious",hint:"adjective",example:"C'est délicieux!"},{word:"L'addition (f.)",roman:"",trans:"The bill",hint:"restaurant",example:"L'addition, s'il vous plaît."},{word:"Manger",roman:"",trans:"To eat",hint:"-er verb",example:"Qu'est-ce qu'on mange?"},{word:"Boire",roman:"",trans:"To drink",hint:"irregular",example:"Vous voulez boire quelque chose?"},{word:"J'ai faim",roman:"",trans:"I'm hungry",hint:"avoir expression",example:"J'ai très faim, on mange bientôt?"},{word:"La carte",roman:"",trans:"The menu",hint:"restaurant",example:"Pouvez-vous m'apporter la carte?"}],
      Numbers:[{word:"Un / Une",roman:"",trans:"One",hint:"m/f",example:"Un café, une bière."},{word:"Deux",roman:"",trans:"Two",hint:"",example:"Deux billets, s'il vous plaît."},{word:"Trois",roman:"",trans:"Three",hint:"",example:"Trois personnes."},{word:"Dix",roman:"",trans:"Ten",hint:"",example:"Dix minutes."},{word:"Cent",roman:"",trans:"Hundred",hint:"",example:"Cent euros."},{word:"Mille",roman:"",trans:"Thousand",hint:"",example:"Mille mercis!"}],
      Travel:[{word:"L'aéroport (m.)",roman:"",trans:"Airport",hint:"",example:"Comment aller à l'aéroport?"},{word:"À droite",roman:"",trans:"To the right",hint:"direction",example:"Tournez à droite."},{word:"À gauche",roman:"",trans:"To the left",hint:"direction",example:"C'est à gauche."},{word:"Où est…?",roman:"",trans:"Where is…?",hint:"question",example:"Où est la station de métro?"},{word:"Le billet",roman:"",trans:"Ticket",hint:"",example:"Je voudrais un billet aller-retour."},{word:"La gare",roman:"",trans:"Train station",hint:"",example:"La gare est loin d'ici?"}],
      Shopping:[{word:"C'est combien?",roman:"",trans:"How much is it?",hint:"",example:"C'est combien, ce manteau?"},{word:"Bon marché",roman:"",trans:"Cheap",hint:"expression",example:"C'est vraiment bon marché!"},{word:"Cher / Chère",roman:"",trans:"Expensive",hint:"adjective",example:"C'est un peu cher."},{word:"Acheter",roman:"",trans:"To buy",hint:"-er verb",example:"Je voudrais acheter un souvenir."},{word:"La caisse",roman:"",trans:"The checkout/cash desk",hint:"",example:"La caisse est là-bas."}],
      Time:[{word:"Maintenant",roman:"",trans:"Now",hint:"",example:"Je veux partir maintenant."},{word:"Aujourd'hui",roman:"",trans:"Today",hint:"",example:"Qu'est-ce que tu fais aujourd'hui?"},{word:"Demain",roman:"",trans:"Tomorrow",hint:"",example:"À demain!"},{word:"Hier",roman:"",trans:"Yesterday",hint:"",example:"Hier j'ai visité le Louvre."}],
    },
    phrases:{Greetings:[{foreign:"Comment allez-vous?",roman:"",eng:"How are you? (formal)",ctx:"Polite greeting"},{foreign:"Ça va?",roman:"",eng:"How's it going? (casual)",ctx:"Casual"},{foreign:"Très bien, merci!",roman:"",eng:"Very well, thank you!",ctx:"Response"},{foreign:"À bientôt!",roman:"",eng:"See you soon!",ctx:"Goodbye"}],Restaurant:[{foreign:"Une table pour ... personnes",roman:"",eng:"A table for ... people",ctx:"Restaurant entry"},{foreign:"Qu'est-ce que vous recommandez?",roman:"",eng:"What do you recommend?",ctx:"Asking"},{foreign:"Je suis végétarien(ne)",roman:"",eng:"I am vegetarian",ctx:"Dietary"},{foreign:"C'était excellent!",roman:"",eng:"It was excellent!",ctx:"Compliment"}],Emergency:[{foreign:"Au secours!",roman:"",eng:"Help!",ctx:"Emergency"},{foreign:"Appelez la police!",roman:"",eng:"Call the police!",ctx:"Safety"},{foreign:"J'ai besoin d'un médecin",roman:"",eng:"I need a doctor",ctx:"Medical"}],Travel:[{foreign:"À quelle heure part le train?",roman:"",eng:"What time does the train leave?",ctx:"Transport"},{foreign:"Je suis perdu(e)",roman:"",eng:"I'm lost",ctx:"Navigation"},{foreign:"Pouvez-vous m'aider?",roman:"",eng:"Can you help me?",ctx:"Asking for help"}]},
    scenarios:{general:[{kp_f:"Qu'est-ce que vous faites dans la vie?",kp_t:"What do you do for a living?"},{kp_f:"D'où venez-vous?",kp_t:"Where are you from?"},{kp_f:"Parlez-vous anglais?",kp_t:"Do you speak English?"}],restaurant:[{kp_f:"La carte, s'il vous plaît",kp_t:"The menu, please"},{kp_f:"Je voudrais...",kp_t:"I would like..."},{kp_f:"L'addition, s'il vous plaît",kp_t:"The bill, please"}],travel:[{kp_f:"Où est la station de métro?",kp_t:"Where is the metro station?"},{kp_f:"Un aller simple, s'il vous plaît",kp_t:"A one-way ticket, please"},{kp_f:"Je cherche...",kp_t:"I'm looking for..."}],shopping:[{kp_f:"Avez-vous une autre taille?",kp_t:"Do you have another size?"},{kp_f:"Puis-je l'essayer?",kp_t:"Can I try it on?"},{kp_f:"Je vais le prendre",kp_t:"I'll take it"}],intro:[{kp_f:"Je m'appelle...",kp_t:"My name is..."},{kp_f:"J'apprends le français",kp_t:"I'm learning French"},{kp_f:"Ravi(e) de vous rencontrer",kp_t:"Pleased to meet you"}]},
  },
  swahili: {
    name: "Swahili", flag: "🇰🇪", voice: "sw-KE",
    categories: ["Greetings","Food","Numbers","Travel","Shopping","Time"],
    grammar: [
      {title:"Noun Classes",badge:"core",rule:"Swahili has 8+ noun classes, each with a prefix that also appears on the verb and adjective. The m-/wa- class is for people; ki-/vi- for things; etc.",examples:[{orig:"Mtoto mzuri / Watoto wazuri",roman:"",trans:"Good child / Good children (note prefix agreement)"}]},
      {title:"Verb Structure",badge:"core",rule:"Verbs combine prefixes: [Subject prefix] + [Tense] + [Object prefix] + [Root]. E.g., ni (I) + na (present) + ku (you) + penda (love) = ninakupenda.",examples:[{orig:"Ninapenda / Unapenda / Anapenda",roman:"",trans:"I love / You love / He/She loves"}]},
      {title:"Tense Markers",badge:"tip",rule:"na = present. li = past. ta = future. Simply swap the tense marker: ninakula (I eat), nilikula (I ate), nitakula (I will eat).",examples:[{orig:"Nilikula / Ninakula / Nitakula",roman:"",trans:"I ate / I eat / I will eat"}]},
      {title:"Greetings Hierarchy",badge:"culture",rule:"Swahili greetings are elaborate and hierarchical. Younger greets older first. Reply to Hujambo with Sijambo (I am well). To a group: Hamjambo → Hatujambo.",examples:[{orig:"Hujambo! → Sijambo. / Habari? → Nzuri.",roman:"",trans:"How are you? → I'm fine. / News? → Good."}]},
      {title:"Adjective Agreement",badge:"tip",rule:"Adjectives must agree in class with their noun. The adjective prefix changes to match the noun class prefix.",examples:[{orig:"Mtu mzuri / Watu wazuri / Kitu kizuri",roman:"",trans:"Good person / Good people / Good thing"}]},
    ],
    fillBlank: {sentence:"Ninataka __ moja ya kahawa tafadhali", answer:"kikombe", hint:"I want one ___ of coffee please (cup = kikombe)"},
    vocab: {
      Greetings:[{word:"Jambo / Hujambo",roman:"",trans:"Hello (formal/to one)",hint:"common greeting",example:"Jambo! Habari?"},{word:"Habari?",roman:"",trans:"News? / How are you?",hint:"lit. 'news'",example:"Habari za asubuhi?"},{word:"Nzuri",roman:"",trans:"Good / Fine",hint:"response",example:"—Habari? —Nzuri sana!"},{word:"Asante",roman:"",trans:"Thank you",hint:"",example:"Asante sana!"},{word:"Tafadhali",roman:"",trans:"Please",hint:"polite",example:"Niambie tafadhali."},{word:"Karibu",roman:"",trans:"Welcome / Come in",hint:"hospitality",example:"Karibu sana!"},{word:"Kwaheri",roman:"",trans:"Goodbye",hint:"parting",example:"Kwaheri! Tutaonana."},{word:"Samahani",roman:"",trans:"Excuse me / Sorry",hint:"apology",example:"Samahani, sijui."}],
      Food:[{word:"Ugali",roman:"",trans:"Cornmeal staple",hint:"East African staple",example:"Ugali na sukuma wiki."},{word:"Maji",roman:"",trans:"Water",hint:"essential",example:"Maji baridi, tafadhali."},{word:"Kitamu",roman:"",trans:"Delicious / Sweet",hint:"adjective",example:"Chakula hiki ni kitamu sana!"},{word:"Chakula",roman:"",trans:"Food",hint:"general",example:"Chakula kiko tayari."},{word:"Kula",roman:"",trans:"To eat",hint:"verb",example:"Twende kula!"},{word:"Kunywa",roman:"",trans:"To drink",hint:"verb",example:"Unataka kunywa nini?"},{word:"Njaa",roman:"",trans:"Hunger",hint:"nina njaa = I'm hungry",example:"Nina njaa sana!"},{word:"Kahawa",roman:"",trans:"Coffee",hint:"",example:"Kahawa moja tafadhali."}],
      Numbers:[{word:"Moja",roman:"",trans:"One",hint:"",example:"Moja kwa moja (straight ahead)."},{word:"Mbili",roman:"",trans:"Two",hint:"",example:"Watu wawili."},{word:"Tatu",roman:"",trans:"Three",hint:"",example:"Vikombe vitatu."},{word:"Nne",roman:"",trans:"Four",hint:"",example:"Saa nne (10 o'clock in Swahili)."},{word:"Tano",roman:"",trans:"Five",hint:"",example:"Shilingi tano."},{word:"Kumi",roman:"",trans:"Ten",hint:"",example:"Dakika kumi."}],
      Travel:[{word:"Uwanja wa ndege",roman:"",trans:"Airport",hint:"lit. airplane field",example:"Uwanja wa ndege uko wapi?"},{word:"Wapi?",roman:"",trans:"Where?",hint:"question",example:"Hospitali iko wapi?"},{word:"Hoteli",roman:"",trans:"Hotel",hint:"loan word",example:"Ninabooking chumba cha hoteli."},{word:"Kulia",roman:"",trans:"Right",hint:"direction",example:"Geuza kulia."},{word:"Kushoto",roman:"",trans:"Left",hint:"direction",example:"Nenda kushoto."},{word:"Tikiti",roman:"",trans:"Ticket",hint:"loan word",example:"Nataka kununua tikiti."}],
      Shopping:[{word:"Bei gani?",roman:"",trans:"What is the price?",hint:"",example:"Bei gani ya hii?"},{word:"Ghali",roman:"",trans:"Expensive",hint:"adjective",example:"Ni ghali sana!"},{word:"Rahisi",roman:"",trans:"Cheap / Easy",hint:"adjective",example:"Bei rahisi zaidi?"},{word:"Kununua",roman:"",trans:"To buy",hint:"verb",example:"Nataka kununua hii."},{word:"Pesa",roman:"",trans:"Money",hint:"",example:"Je, unakubali kadi ya pesa?"}],
      Time:[{word:"Sasa",roman:"",trans:"Now",hint:"",example:"Nipe sasa hivi!"},{word:"Leo",roman:"",trans:"Today",hint:"",example:"Leo ni siku nzuri."},{word:"Kesho",roman:"",trans:"Tomorrow",hint:"",example:"Tutaonana kesho."},{word:"Jana",roman:"",trans:"Yesterday",hint:"",example:"Jana nilikwenda sokoni."}],
    },
    phrases:{Greetings:[{foreign:"Habari za asubuhi?",roman:"",eng:"Good morning (how is your morning news?)",ctx:"Morning greeting"},{foreign:"Niko salama",roman:"",eng:"I am safe / I am well",ctx:"Response to 'how are you'"},{foreign:"Tutaonana baadaye",roman:"",eng:"We'll see each other later",ctx:"Goodbye"},{foreign:"Mungu akubariki",roman:"",eng:"God bless you",ctx:"Blessing/farewell"}],Restaurant:[{foreign:"Niletee menyu tafadhali",roman:"",eng:"Please bring me the menu",ctx:"Ordering"},{foreign:"Unashauri nini?",roman:"",eng:"What do you recommend?",ctx:"Asking server"},{foreign:"Bila nyama tafadhali",roman:"",eng:"Without meat please",ctx:"Dietary"},{foreign:"Ilikuwa kitamu sana!",roman:"",eng:"It was very delicious!",ctx:"Compliment"}],Emergency:[{foreign:"Nisaidie tafadhali!",roman:"",eng:"Please help me!",ctx:"Emergency"},{foreign:"Piga polisi simu!",roman:"",eng:"Call the police!",ctx:"Safety"},{foreign:"Ninahitaji daktari",roman:"",eng:"I need a doctor",ctx:"Medical"}],Travel:[{foreign:"Basi linaenda lini?",roman:"",eng:"When does the bus leave?",ctx:"Transport"},{foreign:"Nimepotea",roman:"",eng:"I am lost",ctx:"Navigation"},{foreign:"Ninaenda...",roman:"",eng:"I am going to...",ctx:"Stating destination"}]},
    scenarios:{general:[{kp_f:"Unafanya kazi gani?",kp_t:"What work do you do?"},{kp_f:"Unatoka wapi?",kp_t:"Where are you from?"},{kp_f:"Unajua Kiswahili?",kp_t:"Do you know Swahili?"}],restaurant:[{kp_f:"Niletee chakula tafadhali",kp_t:"Please bring me food"},{kp_f:"Ninapenda...",kp_t:"I like..."},{kp_f:"Lipa na kadi?",kp_t:"Pay by card?"}],travel:[{kp_f:"Nenda moja kwa moja",kp_t:"Go straight ahead"},{kp_f:"Gari la moshi liko wapi?",kp_t:"Where is the train?"},{kp_f:"Ninahitaji msaada",kp_t:"I need help"}],shopping:[{kp_f:"Punguza bei tafadhali",kp_t:"Reduce the price please"},{kp_f:"Nina pesa ngapi?",kp_t:"How much money do I have?"},{kp_f:"Nitanunua kesho",kp_t:"I'll buy tomorrow"}],intro:[{kp_f:"Jina langu ni...",kp_t:"My name is..."},{kp_f:"Ninajifunza Kiswahili",kp_t:"I am learning Swahili"},{kp_f:"Nimefurahi kukujua",kp_t:"I am happy to know you"}]},
  },
  arabic: {
    name: "Arabic", flag: "🇸🇦", voice: "ar-SA",
    categories: ["Greetings","Food","Numbers","Travel"],
    grammar: [
      {title:"Right-to-Left Script",badge:"core",rule:"Arabic is written and read from right to left. Letters change shape depending on their position in a word (initial, medial, final, isolated).",examples:[{orig:"مرحبا",roman:"Marhaba",trans:"Hello (notice RTL)"}]},
      {title:"Definite Article: ال",badge:"core",rule:"The definite article 'al-' (ال) is a prefix. 'Sun letters' (d, n, r, s, t, etc.) assimilate the 'l', doubling the letter. 'Moon letters' keep the 'l'.",examples:[{orig:"الكتاب / الشمس",roman:"al-kitab / ash-shams",trans:"the book / the sun (l→sh)"}]},
      {title:"Root System",badge:"tip",rule:"Arabic words derive from 3-letter roots. The root k-t-b (ك-ت-ب) relates to writing: kataba (he wrote), kitab (book), maktaba (library), katib (writer).",examples:[{orig:"كتب، كتاب، مكتبة",roman:"Kataba, kitab, maktaba",trans:"Wrote, book, library"}]},
      {title:"Dual Form",badge:"tip",rule:"Arabic has singular, dual (exactly two), and plural. Add -ayn for dual nouns: kitab → kitabayn (two books). Verbs agree too.",examples:[{orig:"كتاب / كتابان / كتب",roman:"Kitab / Kitaban / Kutub",trans:"Book / Two books / Books"}]},
    ],
    fillBlank: {sentence:"أريد __ من فضلك", answer:"قهوة", hint:"I want ___ please (coffee = قهوة, qahwa)"},
    vocab: {
      Greetings:[{word:"مرحبا",roman:"Marhaba",trans:"Hello",hint:"universal",example:"مرحبا! كيف حالك؟"},{word:"السلام عليكم",roman:"As-salamu alaykum",trans:"Peace be upon you",hint:"Islamic greeting",example:"السلام عليكم! — وعليكم السلام"},{word:"شكراً",roman:"Shukran",trans:"Thank you",hint:"",example:"شكراً جزيلاً!"},{word:"من فضلك",roman:"Min fadlak",trans:"Please",hint:"to a man",example:"الفاتورة من فضلك."},{word:"آسف",roman:"Aasif",trans:"Sorry",hint:"",example:"آسف، لا أفهم."},{word:"أهلاً وسهلاً",roman:"Ahlan wa sahlan",trans:"Welcome",hint:"warm welcome",example:"أهلاً وسهلاً بك!"}],
      Food:[{word:"قهوة",roman:"Qahwa",trans:"Coffee",hint:"origin of 'coffee'",example:"قهوة عربية من فضلك."},{word:"ماء",roman:"Ma'",trans:"Water",hint:"",example:"أريد ماء باردًا."},{word:"لذيذ",roman:"Ladhidh",trans:"Delicious",hint:"adjective",example:"الطعام لذيذ جداً!"},{word:"أكل",roman:"Akl",trans:"To eat / food",hint:"root أ-ك-ل",example:"أريد أن آكل."},{word:"جوعان",roman:"Jaw'an",trans:"Hungry",hint:"",example:"أنا جوعان."}],
      Numbers:[{word:"واحد",roman:"Wahid",trans:"One",hint:"",example:"واحد فقط."},{word:"اثنان",roman:"Ithnan",trans:"Two",hint:"",example:"اثنان من فضلك."},{word:"ثلاثة",roman:"Thalatha",trans:"Three",hint:"",example:"ثلاثة أشخاص."},{word:"عشرة",roman:"'Ashara",trans:"Ten",hint:"",example:"عشرة دقائق."},{word:"مئة",roman:"Mi'a",trans:"Hundred",hint:"",example:"مئة دولار."}],
      Travel:[{word:"مطار",roman:"Matar",trans:"Airport",hint:"",example:"أين المطار؟"},{word:"فندق",roman:"Funduq",trans:"Hotel",hint:"",example:"أريد حجز غرفة في الفندق."},{word:"يمين",roman:"Yamin",trans:"Right",hint:"direction",example:"اذهب يميناً."},{word:"يسار",roman:"Yasar",trans:"Left",hint:"direction",example:"اذهب يساراً."},{word:"أين؟",roman:"Ayna?",trans:"Where?",hint:"question",example:"أين المحطة؟"}],
    },
    phrases:{Greetings:[{foreign:"كيف حالك؟",roman:"Kayf halak?",eng:"How are you?",ctx:"Greeting"},{foreign:"بخير، شكراً",roman:"Bikhayr, shukran",eng:"Fine, thank you",ctx:"Response"},{foreign:"مع السلامة",roman:"Ma'a as-salama",eng:"Goodbye (go with safety)",ctx:"Farewell"},{foreign:"تشرفنا",roman:"Tasharrafna",eng:"Honored to meet you",ctx:"Introduction"}],Restaurant:[{foreign:"أريد قائمة الطعام",roman:"Uriid qaa'imat al-ta'am",eng:"I want the menu",ctx:"Ordering"},{foreign:"ماذا تنصح؟",roman:"Madha tansah?",eng:"What do you recommend?",ctx:"Asking"},{foreign:"الحساب من فضلك",roman:"Al-hisab min fadlak",eng:"The bill please",ctx:"Payment"}],Emergency:[{foreign:"النجدة!",roman:"An-najda!",eng:"Help!",ctx:"Emergency"},{foreign:"اتصل بالشرطة",roman:"Ittasil bish-shurta",eng:"Call the police",ctx:"Safety"}],Travel:[{foreign:"أين المحطة؟",roman:"Ayna al-mahatta?",eng:"Where is the station?",ctx:"Transport"},{foreign:"أنا ضائع",roman:"Ana daa'i'",eng:"I am lost",ctx:"Navigation"}]},
    scenarios:{general:[{kp_f:"من أين أنت؟",kp_t:"Where are you from?"},{kp_f:"ما اسمك؟",kp_t:"What is your name?"},{kp_f:"كم عمرك؟",kp_t:"How old are you?"}],restaurant:[{kp_f:"ما هو الطبق المميز؟",kp_t:"What is the special dish?"},{kp_f:"أريد...",kp_t:"I want..."},{kp_f:"الحساب من فضلك",kp_t:"The bill please"}],travel:[{kp_f:"كيف أصل إلى...؟",kp_t:"How do I get to...?"},{kp_f:"هل تتكلم الإنجليزية؟",kp_t:"Do you speak English?"},{kp_f:"أحتاج إلى مساعدة",kp_t:"I need help"}],shopping:[{kp_f:"بكم هذا؟",kp_t:"How much is this?"},{kp_f:"هل يوجد خصم؟",kp_t:"Is there a discount?"},{kp_f:"سأشتري هذا",kp_t:"I'll buy this"}],intro:[{kp_f:"اسمي...",kp_t:"My name is..."},{kp_f:"أتعلم العربية",kp_t:"I am learning Arabic"},{kp_f:"سعيد بمعرفتك",kp_t:"Happy to know you"}]},
  },
  mandarin: {
    name: "Mandarin", flag: "🇨🇳", voice: "zh-CN",
    categories: ["Greetings","Food","Numbers","Travel"],
    grammar: [
      {title:"Tones",badge:"core",rule:"Mandarin has 4 tones + a neutral tone. The same syllable changes meaning with tone: mā (mother), má (hemp), mǎ (horse), mà (scold). Tones are essential!",examples:[{orig:"妈 / 麻 / 马 / 骂",roman:"mā / má / mǎ / mà",trans:"mother / hemp / horse / scold"}]},
      {title:"No Verb Conjugation",badge:"tip",rule:"Mandarin verbs do not change form for tense or person. Time words (yesterday, now, tomorrow) indicate when something happened.",examples:[{orig:"我昨天吃 / 我现在吃 / 我明天吃",roman:"Wǒ zuótiān chī / xiànzài chī / míngtiān chī",trans:"I ate yesterday / eat now / will eat tomorrow"}]},
      {title:"Measure Words",badge:"core",rule:"Between a number and noun, you must use a measure word (量词). 个 (gè) is the most common and can substitute when unsure.",examples:[{orig:"一个人 / 一本书 / 一杯水",roman:"Yī gè rén / yī běn shū / yī bēi shuǐ",trans:"One person / one book / one cup of water"}]},
      {title:"Question Particles",badge:"tip",rule:"Add 吗 (ma) to a statement to form a yes/no question. Use 呢 (ne) for 'and what about...?' No word order changes needed.",examples:[{orig:"你好吗？/ 你呢？",roman:"Nǐ hǎo ma? / Nǐ ne?",trans:"Are you well? / And you?"}]},
    ],
    fillBlank: {sentence:"我想要一__咖啡", answer:"杯", hint:"I want one ___ of coffee (measure word for cups/glasses = 杯 bēi)"},
    vocab: {
      Greetings:[{word:"你好",roman:"Nǐ hǎo",trans:"Hello",hint:"universal",example:"你好！你叫什么名字？"},{word:"谢谢",roman:"Xièxiè",trans:"Thank you",hint:"",example:"非常谢谢！"},{word:"对不起",roman:"Duìbuqǐ",trans:"I'm sorry",hint:"",example:"对不起，我不明白。"},{word:"请",roman:"Qǐng",trans:"Please",hint:"also 'invite'",example:"请坐！"},{word:"再见",roman:"Zàijiàn",trans:"Goodbye",hint:"lit. see again",example:"再见！明天见！"},{word:"你好吗？",roman:"Nǐ hǎo ma?",trans:"How are you?",hint:"question",example:"你好吗？—我很好！"}],
      Food:[{word:"好吃",roman:"Hǎo chī",trans:"Delicious",hint:"lit. good to eat",example:"这个很好吃！"},{word:"水",roman:"Shuǐ",trans:"Water",hint:"",example:"一杯水，请。"},{word:"菜单",roman:"Càidān",trans:"Menu",hint:"",example:"请给我看菜单。"},{word:"吃",roman:"Chī",trans:"To eat",hint:"",example:"你想吃什么？"},{word:"喝",roman:"Hē",trans:"To drink",hint:"",example:"你要喝什么？"},{word:"饿了",roman:"È le",trans:"(I'm) hungry",hint:"le = change of state",example:"我饿了，我们去吃吧。"}],
      Numbers:[{word:"一",roman:"Yī",trans:"One",hint:"1st tone",example:"一个人。"},{word:"二 / 两",roman:"Èr / Liǎng",trans:"Two",hint:"二 counting, 两 with nouns",example:"两个苹果。"},{word:"三",roman:"Sān",trans:"Three",hint:"",example:"三点了。"},{word:"十",roman:"Shí",trans:"Ten",hint:"",example:"十块钱。"},{word:"百",roman:"Bǎi",trans:"Hundred",hint:"",example:"一百元。"}],
      Travel:[{word:"机场",roman:"Jīchǎng",trans:"Airport",hint:"lit. aircraft field",example:"机场在哪里？"},{word:"右边",roman:"Yòubiān",trans:"Right side",hint:"",example:"往右边走。"},{word:"左边",roman:"Zuǒbiān",trans:"Left side",hint:"",example:"在左边。"},{word:"在哪里？",roman:"Zài nǎlǐ?",trans:"Where is it?",hint:"",example:"厕所在哪里？"},{word:"酒店",roman:"Jiǔdiàn",trans:"Hotel",hint:"",example:"我在酒店。"}],
    },
    phrases:{Greetings:[{foreign:"很高兴认识你",roman:"Hěn gāoxìng rènshi nǐ",eng:"Very pleased to meet you",ctx:"Introduction"},{foreign:"你最近怎么样？",roman:"Nǐ zuìjìn zěnmeyàng?",eng:"How have you been lately?",ctx:"Greeting a friend"},{foreign:"保重！",roman:"Bǎozhòng!",eng:"Take care!",ctx:"Farewell"},{foreign:"祝你好运！",roman:"Zhù nǐ hǎoyùn!",eng:"Good luck!",ctx:"Wishing someone well"}],Restaurant:[{foreign:"服务员！",roman:"Fúwùyuán!",eng:"Waiter! (calling server)",ctx:"Restaurant"},{foreign:"我要点菜",roman:"Wǒ yào diǎn cài",eng:"I'd like to order",ctx:"Ordering"},{foreign:"不要辣的，谢谢",roman:"Bú yào là de, xièxiè",eng:"No spicy food, thank you",ctx:"Dietary"},{foreign:"买单！",roman:"Mǎi dān!",eng:"The bill! (common in China)",ctx:"Payment"}],Emergency:[{foreign:"救命！",roman:"Jiùmìng!",eng:"Help! (save life)",ctx:"Emergency"},{foreign:"叫警察！",roman:"Jiào jǐngchá!",eng:"Call the police!",ctx:"Safety"},{foreign:"我需要医生",roman:"Wǒ xūyào yīshēng",eng:"I need a doctor",ctx:"Medical"}],Travel:[{foreign:"这趟车去...吗？",roman:"Zhè tàng chē qù... ma?",eng:"Does this train/bus go to...?",ctx:"Transport"},{foreign:"我迷路了",roman:"Wǒ mílù le",eng:"I'm lost",ctx:"Navigation"},{foreign:"请写下来",roman:"Qǐng xiě xiàlái",eng:"Please write it down",ctx:"Communication tip"}]},
    scenarios:{general:[{kp_f:"你是哪国人？",kp_t:"What nationality are you?"},{kp_f:"你会说中文吗？",kp_t:"Can you speak Chinese?"},{kp_f:"你喜欢什么？",kp_t:"What do you like?"}],restaurant:[{kp_f:"有什么推荐？",kp_t:"What is recommended?"},{kp_f:"来一份...",kp_t:"One portion of..."},{kp_f:"结账，谢谢",kp_t:"Check please, thank you"}],travel:[{kp_f:"怎么去...？",kp_t:"How do I get to...?"},{kp_f:"多少钱一张票？",kp_t:"How much is one ticket?"},{kp_f:"我要去...",kp_t:"I want to go to..."}],shopping:[{kp_f:"能便宜一点吗？",kp_t:"Can it be a bit cheaper?"},{kp_f:"有没有大一点的？",kp_t:"Do you have a bigger one?"},{kp_f:"我要这个",kp_t:"I want this one"}],intro:[{kp_f:"我叫...",kp_t:"My name is..."},{kp_f:"我在学中文",kp_t:"I am learning Chinese"},{kp_f:"很开心认识你",kp_t:"Very happy to meet you"}]},
  }
};

// ===================== STATE =====================
let currentLang = 'japanese';
let currentCat = 'Greetings';
let cardIndex = 0;
let cardList = [];
let quizRight = 0, quizWrong = 0;
let quizAnswered = false;
let quizMode = 'mc';
let chatHistory = [];
let vocabSet = new Set();
let userProgress = loadProgress();

function loadProgress() {
  try { return JSON.parse(localStorage.getItem('linguaflow_progress') || '{}'); } catch { return {}; }
}
function saveProgress() {
  try { localStorage.setItem('linguaflow_progress', JSON.stringify(userProgress)); } catch {}
}
function getLP(lang) {
  if (!userProgress[lang]) userProgress[lang] = {cardsStudied:0,quizCorrect:0,quizWrong:0,xp:0,streak:0,lastDay:'',knownWords:[]};
  return userProgress[lang];
}
function addXP(amount) {
  const lp = getLP(currentLang);
  lp.xp = (lp.xp || 0) + amount;
  const today = new Date().toDateString();
  if (lp.lastDay !== today) { lp.streak = (lp.streak||0)+1; lp.lastDay = today; }
  saveProgress();
  updateSidebarStats();
}
function updateSidebarStats() {
  const lp = getLP(currentLang);
  const xp = lp.xp || 0;
  const max = 100;
  document.getElementById('streakNum').textContent = lp.streak || 0;
  document.getElementById('xpNum').textContent = xp % max;
  document.getElementById('xpMax').textContent = max;
  document.getElementById('xpBar').style.width = ((xp % max) / max * 100) + '%';
  const levels = ['A1','A2','B1','B2','C1','C2'];
  const lvl = levels[Math.min(Math.floor(xp/100), 5)];
  document.getElementById('lvl-' + currentLang).textContent = lvl;
}

// ===================== NAVIGATION =====================
document.querySelectorAll('.lang-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.lang-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    currentLang = btn.dataset.lang;
    currentCat = DATA[currentLang].categories[0];
    cardIndex = 0;
    updateSidebarStats();
    renderFlashcards();
    if (document.getElementById('panel-quiz').classList.contains('active')) renderQuiz();
    if (document.getElementById('panel-grammar').classList.contains('active')) renderGrammar();
    if (document.getElementById('panel-phrases').classList.contains('active')) renderPhrases();
    if (document.getElementById('panel-conversation').classList.contains('active')) initConversation();
  });
});

document.querySelectorAll('.nav-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
    document.querySelectorAll('.panel').forEach(p => p.classList.remove('active'));
    btn.classList.add('active');
    const panel = document.getElementById('panel-' + btn.dataset.tab);
    if (panel) panel.classList.add('active');
    if (btn.dataset.tab === 'quiz') renderQuiz();
    if (btn.dataset.tab === 'grammar') renderGrammar();
    if (btn.dataset.tab === 'phrases') renderPhrases();
    if (btn.dataset.tab === 'conversation') initConversation();
    if (btn.dataset.tab === 'progress') renderProgress();
  });
});

// ===================== FLASHCARDS =====================
function getLangData() { return DATA[currentLang]; }

function buildCardList() {
  cardList = [...(getLangData().vocab[currentCat] || [])];
}

function renderCatPills() {
  const container = document.getElementById('catPills');
  container.innerHTML = '';
  getLangData().categories.forEach(cat => {
    const btn = document.createElement('button');
    btn.className = 'cat-pill' + (cat === currentCat ? ' active' : '');
    btn.textContent = cat;
    btn.onclick = () => { currentCat = cat; cardIndex = 0; buildCardList(); renderCard(); renderCatPills(); };
    container.appendChild(btn);
  });
}

function renderCard() {
  if (!cardList.length) return;
  const card = cardList[cardIndex % cardList.length];
  document.getElementById('cardWord').textContent = card.word;
  document.getElementById('cardHint').textContent = card.hint ? `(${card.hint})` : '';
  document.getElementById('cardTag').textContent = currentCat;
  document.getElementById('cardTrans').textContent = card.trans;
  document.getElementById('cardRoman').textContent = card.roman ? `[${card.roman}]` : '';
  document.getElementById('cardExample').textContent = card.example || '';
  document.getElementById('flashcard').classList.remove('flipped');
  const pct = Math.round((cardIndex / cardList.length) * 100);
  document.getElementById('progressBar').style.width = pct + '%';
  document.getElementById('cardCounter').textContent = `${cardIndex + 1} / ${cardList.length}`;
}

function speak(text, lang) {
  if (!('speechSynthesis' in window)) return;
  window.speechSynthesis.cancel();
  const utt = new SpeechSynthesisUtterance(text);
  utt.lang = getLangData().voice || 'en-US';
  utt.rate = 0.85;
  window.speechSynthesis.speak(utt);
}

document.getElementById('flashcard').addEventListener('click', () => document.getElementById('flashcard').classList.toggle('flipped'));
document.getElementById('flipBtn').addEventListener('click', () => document.getElementById('flashcard').classList.toggle('flipped'));
document.getElementById('speakWordBtn').addEventListener('click', () => { if (cardList.length) speak(cardList[cardIndex % cardList.length].word); });

document.getElementById('goodBtn').addEventListener('click', () => {
  const lp = getLP(currentLang);
  lp.cardsStudied = (lp.cardsStudied || 0) + 1;
  if (!lp.knownWords.includes(cardList[cardIndex % cardList.length]?.word)) {
    lp.knownWords.push(cardList[cardIndex % cardList.length]?.word);
  }
  addXP(5);
  cardIndex = (cardIndex + 1) % cardList.length;
  renderCard();
});

document.getElementById('badBtn').addEventListener('click', () => {
  const lp = getLP(currentLang);
  lp.cardsStudied = (lp.cardsStudied || 0) + 1;
  saveProgress();
  const c = cardList.splice(cardIndex % cardList.length, 1)[0];
  const ins = Math.min((cardIndex % cardList.length) + 2, cardList.length);
  cardList.splice(ins, 0, c);
  if (cardIndex >= cardList.length) cardIndex = 0;
  renderCard();
});

document.getElementById('shuffleBtn').addEventListener('click', () => {
  cardList.sort(() => Math.random() - 0.5);
  cardIndex = 0;
  renderCard();
});

function renderFlashcards() {
  buildCardList();
  renderCatPills();
  renderCard();
}

// ===================== QUIZ =====================
document.querySelectorAll('.mode-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.mode-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    quizMode = btn.dataset.mode;
    renderQuiz();
  });
});

function getAllVocab() {
  const all = [];
  Object.values(getLangData().vocab).forEach(arr => all.push(...arr));
  return all;
}

let currentQuizCard = null;

function renderQuiz() {
  quizAnswered = false;
  document.getElementById('quizFeedback').textContent = '';
  document.getElementById('quizFeedback').style.color = 'var(--muted)';
  const all = getAllVocab();
  if (!all.length) return;
  const idx = Math.floor(Math.random() * all.length);
  currentQuizCard = all[idx];
  const wrong = all.filter((_,i) => i !== idx).sort(() => Math.random() - 0.5).slice(0, 3);

  const qEl = document.getElementById('quizQuestion');
  const body = document.getElementById('quizBody');

  if (quizMode === 'mc') {
    if (Math.random() > 0.5) {
      qEl.innerHTML = `<div><small>Translate to English</small>${currentQuizCard.word}</div>`;
      const choices = [...wrong.map(w=>w.trans), currentQuizCard.trans].sort(() => Math.random() - 0.5);
      body.innerHTML = '';
      const grid = document.createElement('div'); grid.className = 'quiz-options';
      choices.forEach(ch => {
        const btn = document.createElement('button'); btn.className = 'quiz-opt'; btn.textContent = ch;
        btn.onclick = () => handleMCAnswer(btn, ch === currentQuizCard.trans);
        grid.appendChild(btn);
      });
      body.appendChild(grid);
    } else {
      qEl.innerHTML = `<div><small>Which word means</small>"${currentQuizCard.trans}"</div>`;
      const choices = [...wrong.map(w=>w.word), currentQuizCard.word].sort(() => Math.random() - 0.5);
      body.innerHTML = '';
      const grid = document.createElement('div'); grid.className = 'quiz-options';
      choices.forEach(ch => {
        const btn = document.createElement('button'); btn.className = 'quiz-opt'; btn.textContent = ch;
        btn.onclick = () => handleMCAnswer(btn, ch === currentQuizCard.word);
        grid.appendChild(btn);
      });
      body.appendChild(grid);
    }
  } else if (quizMode === 'type') {
    qEl.innerHTML = `<div><small>Type the translation of</small>${currentQuizCard.word}</div>`;
    body.innerHTML = `
      <input type="text" class="quiz-type-input" id="typeInput" placeholder="Type translation…" autocomplete="off">
      <button class="quiz-submit" id="typeSubmit">Check Answer</button>
    `;
    document.getElementById('typeInput').addEventListener('keydown', e => { if (e.key === 'Enter') document.getElementById('typeSubmit').click(); });
    document.getElementById('typeSubmit').addEventListener('click', () => {
      if (quizAnswered) return;
      const val = document.getElementById('typeInput').value.trim().toLowerCase();
      const correct = currentQuizCard.trans.toLowerCase();
      handleTypeAnswer(val === correct || correct.includes(val));
    });
  } else if (quizMode === 'reverse') {
    qEl.innerHTML = `<div><small>Say this in ${getLangData().name}</small>"${currentQuizCard.trans}"</div>`;
    const choices = [...wrong.map(w=>w.word), currentQuizCard.word].sort(() => Math.random() - 0.5);
    body.innerHTML = '';
    const grid = document.createElement('div'); grid.className = 'quiz-options';
    choices.forEach(ch => {
      const btn = document.createElement('button'); btn.className = 'quiz-opt'; btn.textContent = ch;
      btn.onclick = () => handleMCAnswer(btn, ch === currentQuizCard.word);
      grid.appendChild(btn);
    });
    body.appendChild(grid);
  }
}

function handleMCAnswer(btn, isCorrect) {
  if (quizAnswered) return;
  quizAnswered = true;
  document.querySelectorAll('.quiz-opt').forEach(b => { b.disabled = true; });
  const fb = document.getElementById('quizFeedback');
  if (isCorrect) {
    btn.classList.add('correct');
    quizRight++;
    document.getElementById('scoreRight').textContent = quizRight;
    getLP(currentLang).quizCorrect = (getLP(currentLang).quizCorrect||0)+1;
    addXP(10);
    fb.textContent = '✓ Correct!' + (currentQuizCard.roman ? ` [${currentQuizCard.roman}]` : '');
    fb.style.color = 'var(--success)';
    speak(currentQuizCard.word);
  } else {
    btn.classList.add('wrong');
    quizWrong++;
    document.getElementById('scoreWrong').textContent = quizWrong;
    getLP(currentLang).quizWrong = (getLP(currentLang).quizWrong||0)+1;
    saveProgress();
    document.querySelectorAll('.quiz-opt').forEach(b => {
      if (b.textContent === currentQuizCard.trans || b.textContent === currentQuizCard.word) b.classList.add('correct');
    });
    fb.textContent = `✗ Answer: "${currentQuizCard.trans}" ${currentQuizCard.roman ? `[${currentQuizCard.roman}]` : ''}`;
    fb.style.color = 'var(--danger)';
  }
  setTimeout(renderQuiz, 1800);
}

function handleTypeAnswer(isCorrect) {
  if (quizAnswered) return;
  quizAnswered = true;
  const fb = document.getElementById('quizFeedback');
  const input = document.getElementById('typeInput');
  if (isCorrect) {
    input.style.borderColor = 'var(--success)';
    quizRight++; document.getElementById('scoreRight').textContent = quizRight;
    getLP(currentLang).quizCorrect = (getLP(currentLang).quizCorrect||0)+1;
    addXP(15);
    fb.textContent = '✓ Correct!'; fb.style.color = 'var(--success)';
    speak(currentQuizCard.word);
  } else {
    input.style.borderColor = 'var(--danger)';
    quizWrong++; document.getElementById('scoreWrong').textContent = quizWrong;
    fb.textContent = `✗ Answer: "${currentQuizCard.trans}"`; fb.style.color = 'var(--danger)';
  }
  setTimeout(renderQuiz, 1800);
}

// ===================== GRAMMAR =====================
function renderGrammar() {
  const d = getLangData();
  document.getElementById('grammarSub').textContent = `${d.name} grammar essentials`;
  const gc = document.getElementById('grammarContent');
  gc.innerHTML = '';
  d.grammar.forEach(g => {
    const card = document.createElement('div'); card.className = 'grammar-card';
    const badgeClass = 'badge-' + g.badge;
    const exHTML = g.examples.map(e => `
      <div class="example-box">
        <span class="ex-orig">${e.orig}</span>
        ${e.roman ? `<span class="ex-roman">[${e.roman}]</span>` : ''}
        <span class="ex-arrow">→</span>
        <span class="ex-trans">${e.trans}</span>
      </div>
    `).join('');
    card.innerHTML = `
      <div class="grammar-card-title">${g.title} <span class="badge ${badgeClass}">${g.badge.toUpperCase()}</span></div>
      <div class="rule-text">${g.rule}</div>
      ${exHTML}
    `;
    gc.appendChild(card);
  });

  const fb = d.fillBlank;
  const fbArea = document.getElementById('fillBlankArea');
  const highlighted = fb.sentence.replace('__', '<span style="color:var(--accent2);border-bottom:2px solid var(--accent2);padding:0 4px;">____</span>');
  fbArea.innerHTML = `
    <div class="fill-blank-card">
      <div class="grammar-card-title">Fill in the Blank <span class="badge badge-tip">PRACTICE</span></div>
      <div class="fill-sentence">${highlighted}</div>
      <div class="fill-hint">💡 ${fb.hint}</div>
      <div class="fill-row">
        <input class="fill-input" id="fillInput" type="text" placeholder="Your answer…" autocomplete="off">
        <button class="check-btn" id="fillBtn">Check →</button>
        <button class="check-btn" id="revealBtn" style="background:var(--card2);color:var(--muted);border:1px solid var(--border);">Reveal</button>
      </div>
      <div class="fill-result" id="fillResult"></div>
    </div>
  `;
  document.getElementById('fillInput').addEventListener('keydown', e => { if (e.key==='Enter') document.getElementById('fillBtn').click(); });
  document.getElementById('fillBtn').addEventListener('click', () => {
    const val = document.getElementById('fillInput').value.trim();
    const res = document.getElementById('fillResult');
    if (val.toLowerCase() === fb.answer.toLowerCase()) {
      res.textContent = '✓ Correct! Well done.'; res.style.color = 'var(--success)'; addXP(20);
    } else {
      res.textContent = `✗ The answer is: "${fb.answer}"`; res.style.color = 'var(--danger)';
    }
  });
  document.getElementById('revealBtn').addEventListener('click', () => {
    const res = document.getElementById('fillResult');
    res.textContent = `Answer: "${fb.answer}"`; res.style.color = 'var(--info)';
  });
}

// ===================== PHRASEBOOK =====================
function renderPhrases() {
  const d = getLangData();
  const cats = Object.keys(d.phrases);
  let activeCat = cats[0];
  const catContainer = document.getElementById('phraseCats');
  catContainer.innerHTML = '';
  cats.forEach(c => {
    const btn = document.createElement('button');
    btn.className = 'cat-pill' + (c === activeCat ? ' active' : '');
    btn.textContent = c;
    btn.onclick = () => { activeCat = c; renderPhraseGrid(); catContainer.querySelectorAll('.cat-pill').forEach(b => b.classList.remove('active')); btn.classList.add('active'); };
    catContainer.appendChild(btn);
  });

  function renderPhraseGrid() {
    const grid = document.getElementById('phraseGrid');
    grid.innerHTML = '';
    (d.phrases[activeCat] || []).forEach(p => {
      const card = document.createElement('div'); card.className = 'phrase-card';
      card.innerHTML = `
        <div class="phrase-top">
          <div class="phrase-foreign">${p.foreign}</div>
          <button class="phrase-speak-btn" data-text="${p.foreign}">🔊 Hear</button>
        </div>
        ${p.roman ? `<div class="phrase-roman">${p.roman}</div>` : ''}
        <div class="phrase-eng">${p.eng}</div>
        <div class="phrase-ctx">${p.ctx}</div>
      `;
      card.querySelector('.phrase-speak-btn').addEventListener('click', e => { e.stopPropagation(); speak(p.foreign); });
      grid.appendChild(card);
    });
  }
  renderPhraseGrid();
}

// ===================== AI CONVERSATION =====================
let apiKey = localStorage.getItem('linguaflow_apikey') || '';

function initConversation() {
  const d = getLangData();
  document.getElementById('chatLangLabel').textContent = `Practicing: ${d.name}`;
  if (apiKey) document.getElementById('apiKeyInput').value = apiKey;
  updateScenarioPhrases();
  if (chatHistory.length === 0) {
    addChatMessage('ai', `Hello! I'm your ${d.name} tutor. Let's practice! I'll respond in ${d.name}, correct your mistakes gently, and help you learn. Try saying something in ${d.name} or ask me anything! ${d.flag}`);
  }
}

document.getElementById('saveApiKey').addEventListener('click', () => {
  apiKey = document.getElementById('apiKeyInput').value.trim();
  localStorage.setItem('linguaflow_apikey', apiKey);
  addChatMessage('ai', '✓ API key saved! Now let\'s practice. Try saying something in ' + getLangData().name + '!');
});

document.getElementById('scenarioSelect').addEventListener('change', () => {
  updateScenarioPhrases();
  const scenario = document.getElementById('scenarioSelect').value;
  const d = getLangData();
  const scenarios = {general:'general conversation',restaurant:'a restaurant scenario',travel:'travel and directions',shopping:'shopping',intro:'self-introduction'};
  chatHistory = [];
  document.getElementById('chatMessages').innerHTML = '';
  addChatMessage('ai', `Great! Let's practice ${scenarios[scenario] || 'conversation'} in ${d.name}. I'll play along with the scenario. You can write in English or ${d.name}. ${d.flag}`);
});

function updateScenarioPhrases() {
  const scenario = document.getElementById('scenarioSelect').value;
  const d = getLangData();
  const phrases = d.scenarios?.[scenario] || d.scenarios?.general || [];
  const container = document.getElementById('scenarioPhrases');
  container.innerHTML = '';
  phrases.forEach(p => {
    const div = document.createElement('div'); div.className = 'key-phrase';
    div.innerHTML = `<div class="kp-f">${p.kp_f}</div><div class="kp-t">${p.kp_t}</div>`;
    container.appendChild(div);
  });
}

function addChatMessage(role, text, correction) {
  const container = document.getElementById('chatMessages');
  const msg = document.createElement('div');
  msg.className = 'msg ' + role;
  const time = new Date().toLocaleTimeString('en-US', {hour:'2-digit',minute:'2-digit'});
  msg.innerHTML = `<div class="msg-bubble">${text.replace(/\n/g,'<br>')}</div><div class="msg-time">${time}</div>`;
  if (correction) {
    const corr = document.createElement('div'); corr.className = 'msg-correction';
    corr.textContent = '💡 ' + correction;
    msg.appendChild(corr);
  }
  container.appendChild(msg);
  container.scrollTop = container.scrollHeight;
  chatHistory.push({role: role === 'ai' ? 'assistant' : 'user', content: text});
}

function showTyping() {
  const container = document.getElementById('chatMessages');
  const typing = document.createElement('div'); typing.className = 'msg ai'; typing.id = 'typingIndicator';
  typing.innerHTML = `<div class="typing-indicator"><div class="typing-dot"></div><div class="typing-dot"></div><div class="typing-dot"></div></div>`;
  container.appendChild(typing);
  container.scrollTop = container.scrollHeight;
}

function removeTyping() {
  const el = document.getElementById('typingIndicator');
  if (el) el.remove();
}

async function sendMessage() {
  const input = document.getElementById('chatInput');
  const text = input.value.trim();
  if (!text) return;
  if (!apiKey) { addChatMessage('ai', '⚠️ Please enter your Anthropic API key in the sidebar to start AI conversation practice.'); return; }

  input.value = '';
  input.style.height = 'auto';
  addChatMessage('user', text);
  document.getElementById('sendBtn').disabled = true;
  showTyping();

  const d = getLangData();
  const scenario = document.getElementById('scenarioSelect').value;
  const scenarioDescriptions = {general:'general everyday conversation',restaurant:'a restaurant, you are the server/host',travel:'a travel/directions scenario',shopping:'a shopping scenario, you are a shopkeeper',intro:'a self-introduction scenario'};

  const systemPrompt = `You are a friendly, encouraging ${d.name} language tutor. The student is practicing ${d.name} in a ${scenarioDescriptions[scenario] || 'conversational'} context.

YOUR BEHAVIOR:
1. Respond naturally to what the student says, advancing the conversation.
2. Write 1-3 sentences in ${d.name}, then provide the English translation in parentheses.
3. If the student makes a grammar or vocabulary mistake, gently note the correction at the end with: 💡 Correction: [what they said] → [correct form]: [brief explanation]
4. Use vocabulary appropriate for beginners/intermediate learners.
5. Occasionally suggest a phrase the student could try next.
6. Be warm, patient, and encouraging. Celebrate effort.
7. If the student writes in English, respond partly in English and introduce the ${d.name} equivalent.

Current language: ${d.name}
Learning scenario: ${scenarioDescriptions[scenario]}
Student level: Beginner to Intermediate

Keep responses concise and practical.`;

  const messages = chatHistory.slice(-10).map(m => ({role: m.role, content: m.content}));
  messages.push({role:'user', content: text});

  try {
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json', 'x-api-key': apiKey, 'anthropic-version': '2023-06-01', 'anthropic-dangerous-direct-browser-access': 'true' },
      body: JSON.stringify({ model: 'claude-sonnet-4-20250514', max_tokens: 600, system: systemPrompt, messages })
    });
    const data = await response.json();
    removeTyping();
    if (data.content?.[0]?.text) {
      const reply = data.content[0].text;
      const corrMatch = reply.match(/💡 Correction: (.+)/);
      const mainText = reply.replace(/💡 Correction: .+/s, '').trim();
      addChatMessage('ai', mainText, corrMatch ? corrMatch[1] : null);
      addXP(15);
      const words = reply.match(/[\u3040-\u30ff\u3400-\u4dbf\u4e00-\u9fff\uAC00-\uD7AF\u0600-\u06FF\w]+/g);
      if (words) {
        words.slice(0,4).forEach(w => vocabSet.add(w));
        const vc = document.getElementById('vocabUsed');
        vc.innerHTML = [...vocabSet].slice(-12).map(w => `<span class="vocab-chip">${w}</span>`).join('');
      }
    } else if (data.error) {
      addChatMessage('ai', `⚠️ API error: ${data.error.message || 'Please check your API key.'}`);
    }
  } catch(e) {
    removeTyping();
    addChatMessage('ai', '⚠️ Connection error. Please check your API key and internet connection.');
  }
  document.getElementById('sendBtn').disabled = false;
}

document.getElementById('sendBtn').addEventListener('click', sendMessage);
document.getElementById('chatInput').addEventListener('keydown', e => {
  if (e.key === 'Enter' && !e.shiftKey) { e.preventDefault(); sendMessage(); }
});
document.getElementById('chatInput').addEventListener('input', function() {
  this.style.height = 'auto';
  this.style.height = Math.min(this.scrollHeight, 120) + 'px';
});

// ===================== PROGRESS =====================
function renderProgress() {
  let totalCards=0, totalCorrect=0, totalXPAll=0, bestStreak=0;
  Object.entries(DATA).forEach(([lang, d]) => {
    const lp = getLP(lang);
    totalCards += lp.cardsStudied || 0;
    totalCorrect += lp.quizCorrect || 0;
    totalXPAll += lp.xp || 0;
    bestStreak = Math.max(bestStreak, lp.streak || 0);
  });
  document.getElementById('totalCards').textContent = totalCards;
  document.getElementById('totalCorrect').textContent = totalCorrect;
  document.getElementById('totalXP').textContent = totalXPAll;
  document.getElementById('totalStreak').textContent = bestStreak;

  const grid = document.getElementById('progressGrid');
  grid.innerHTML = '';
  const levels = ['A1','A2','B1','B2','C1','C2'];
  const colors = ['var(--accent2)','var(--accent)','var(--success)','var(--info)','var(--danger)','#ff9f43'];
  Object.entries(DATA).forEach(([lang, d]) => {
    const lp = getLP(lang);
    const xp = lp.xp || 0;
    const lvlIdx = Math.min(Math.floor(xp/100), 5);
    const lvl = levels[lvlIdx];
    const pct = ((xp % 100));
    const card = document.createElement('div'); card.className = 'progress-lang-card';
    card.innerHTML = `
      <div class="plc-header">
        <span class="plc-flag">${d.flag}</span>
        <span class="plc-name">${d.name}</span>
        <span class="plc-level">${lvl}</span>
      </div>
      <div class="plc-stat"><span class="plc-stat-lbl">Cards studied</span><span>${lp.cardsStudied||0}</span></div>
      <div class="plc-stat"><span class="plc-stat-lbl">Quiz correct</span><span>${lp.quizCorrect||0}</span></div>
      <div class="plc-stat"><span class="plc-stat-lbl">Total XP</span><span>${xp}</span></div>
      <div class="plc-bar-wrap"><div class="plc-bar" style="width:${pct}%;background:${colors[lvlIdx]};"></div></div>
      <div style="font-size:11px;color:var(--muted);">${pct}/100 XP to next level</div>
    `;
    grid.appendChild(card);
  });

  const achievements = [
    {icon:'🌱',name:'First Steps',desc:'Study your first card',locked: totalCards===0},
    {icon:'🔥',name:'On Fire',desc:'3-day streak',locked:(userProgress[currentLang]?.streak||0)<3},
    {icon:'🎯',name:'Sharp Shooter',desc:'10 quiz correct',locked:totalCorrect<10},
    {icon:'📚',name:'Word Hoarder',desc:'Study 50 cards',locked:totalCards<50},
    {icon:'🏆',name:'Quiz Master',desc:'50 quiz correct',locked:totalCorrect<50},
    {icon:'⭐',name:'Polyglot',desc:'XP in 3 languages',locked:Object.values(userProgress).filter(lp=>lp.xp>0).length<3},
    {icon:'🎓',name:'Graduate',desc:'Reach A2 in any language',locked:!Object.values(userProgress).some(lp=>(lp.xp||0)>=100)},
    {icon:'💬',name:'Conversationalist',desc:'Use AI chat',locked:chatHistory.length===0},
    {icon:'🌍',name:'World Explorer',desc:'Study all 6 languages',locked:Object.values(userProgress).filter(lp=>lp.cardsStudied>0).length<6},
  ];
  const ag = document.getElementById('achievementsGrid');
  ag.innerHTML = '';
  achievements.forEach(a => {
    const div = document.createElement('div'); div.className = 'achievement' + (a.locked ? ' locked' : '');
    div.innerHTML = `<div class="ach-icon">${a.icon}</div><div class="ach-name">${a.name}</div><div class="ach-desc">${a.desc}</div>`;
    ag.appendChild(div);
  });
}

document.getElementById('resetProgress').addEventListener('click', () => {
  if (confirm('Reset all progress? This cannot be undone.')) {
    localStorage.removeItem('linguaflow_progress');
    userProgress = {};
    quizRight = 0; quizWrong = 0;
    document.getElementById('scoreRight').textContent = '0';
    document.getElementById('scoreWrong').textContent = '0';
    updateSidebarStats();
    renderProgress();
  }
});

// ===================== INIT =====================
renderFlashcards();
updateSidebarStats();
</script>
</body>
</html>
