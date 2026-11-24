# HTML 输出规范：双栏批注阅读器

每次生成批注网页时，必须使用本文件中的 CSS 变量、组件类名与布局断点，保证同一套模板处理多篇论文时视觉一致、可横向对比。

## 设计原则

1. **单文件、零外部依赖**：不引用 CDN 字体或 JS 库，双击即可离线打开
2. **颜色是语义不是装饰**：五色严格对应五个论证维度，不挪作他用
3. **英文原文必须保留**：中文是意译，放在原文下方的米色块，永不替代原文
4. **批注卡片与左栏段落一一对应**：共用段落编号（如 `p3`），右栏卡片标注对应编号
5. **响应式**：桌面双栏，≤900px 单栏（每节批注集中在该节段落之后）
6. **打印友好**：打印时隐藏导航、变单栏、段落不跨页断裂

## 色彩系统

```css
:root {
  --navy: #1c2b4a;          /* 深海军蓝：顶部导航栏、总览标题 */
  --paper: #faf7f0;         /* 米白纸张底色 */
  --ink: #2b2b2b;           /* 正文墨色 */
  --muted: #6b6b6b;         /* 次要文字（元信息、编号） */
  --hl-thesis: #ffe9a8;     /* 🟡 黄：核心论点 */
  --hl-concept: #ffd3d3;    /* 🔴 红：关键概念/术语 */
  --hl-evidence: #cfe0ff;   /* 🔵 蓝：实证证据/数据 */
  --hl-concession: #cdeec9; /* 🟢 绿：让步/反驳处理 */
  --hl-method: #e3d3f7;     /* 🟣 紫：方法论说明 */
  --trans-bg: #f0ece2;      /* 中文译文块背景 */
  --trans-bar: #b8ab8f;     /* 中文译文块左侧竖线 */
  --card-bg: #fdfcf8;       /* 批注卡片背景 */
  --border: #e5dfd0;        /* 细分隔线、卡片描边 */
}
```

## 字体

- 正文英文：`Lora, Georgia, "Times New Roman", serif`（Lora 缺失时优雅降级为系统衬线）
- 界面/导航：`"IBM Plex Sans", -apple-system, "Segoe UI", "PingFang SC", "Microsoft YaHei", sans-serif`
- 中文：交给系统字体（PingFang SC / Microsoft YaHei）

## HTML 结构骨架

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{论文名} — 双栏批注阅读</title>
  <style>/* 核心 CSS，见下 */</style>
</head>
<body>
  <header class="topbar">
    <div class="title-wrap">
      <h1>{论文名}</h1>
      <div class="meta">{作者 · 期刊 · 年份 · DOI}</div>
    </div>
    <ul class="legend">
      <li><span class="chip hl-thesis">核心论点</span></li>
      <li><span class="chip hl-concept">关键概念</span></li>
      <li><span class="chip hl-evidence">证据数据</span></li>
      <li><span class="chip hl-concession">让步/反驳</span></li>
      <li><span class="chip hl-method">方法论</span></li>
    </ul>
  </header>

  <nav class="secnav">
    <a href="#abstract">摘要</a>
    <a href="#intro">引言</a>
    <a href="#method">方法</a>
    <a href="#experiments">实验</a>
    <a href="#conclusion">结论</a>
  </nav>

  <main>
    <section class="sec" id="intro">
      <h2 class="sec-title">{章节标题}</h2>
      <div class="grid">
        <div class="col-en">
          <div class="passage" id="p{N}">
            <p class="en">{英文原文，关键句用 span.hl-* 高亮}</p>
            <div class="trans">{中文意译}</div>
          </div>
          <!-- 该节其余段落 passage 依次排列 -->
        </div>
        <div class="col-notes">
          <aside class="note" data-for="p{N}">
            <div class="note-no">¶ p{N}</div>
            <div class="note-field"><b>功能</b> {引出问题/提供证据/反驳异议/总结}</div>
            <div class="note-field"><b>逻辑角色</b> {论证链中的位置}</div>
            <div class="note-field"><b>技巧或漏洞</b> {含具体依据}</div>
          </aside>
          <!-- 该节其余批注卡片依次排列 -->
        </div>
      </div>
    </section>
    <!-- 其余章节 section 依次排列 -->
  </main>

  <section class="overview">
    <h2>论证结构总览</h2>
    <div class="skeleton">
      <div class="sk-step"><div class="sk-num">1</div><h4>问题</h4><p>{…}</p></div>
      <div class="sk-step"><div class="sk-num">2</div><h4>论点</h4><p>{…}</p></div>
      <div class="sk-step"><div class="sk-num">3</div><h4>方法</h4><p>{…}</p></div>
      <div class="sk-step"><div class="sk-num">4</div><h4>证据</h4><p>{…}</p></div>
      <div class="sk-step"><div class="sk-num">5</div><h4>反驳/让步</h4><p>{…}</p></div>
      <div class="sk-step"><div class="sk-num">6</div><h4>结论</h4><p>{…}</p></div>
    </div>
    <div class="verdicts">
      <div class="verdict verdict-thesis"><b>核心主张（一句话）</b> {…}</div>
      <div class="verdict verdict-strong"><b>论证最强处</b> {…}</div>
      <div class="verdict verdict-weak"><b>论证最弱处</b> {…}</div>
    </div>
  </section>
</body>
</html>
```

## 核心 CSS

```css
* { box-sizing: border-box; margin: 0; padding: 0; }
body {
  background: var(--paper); color: var(--ink);
  font-family: "IBM Plex Sans", -apple-system, "Segoe UI", "PingFang SC", "Microsoft YaHei", sans-serif;
  line-height: 1.7;
}

/* 顶部导航栏 */
.topbar {
  background: var(--navy); color: #fff;
  padding: 20px 28px; display: flex; flex-wrap: wrap;
  align-items: center; justify-content: space-between; gap: 12px;
}
.topbar h1 { font-size: 1.35rem; font-weight: 600; letter-spacing: .3px; }
.topbar .meta { font-size: .82rem; opacity: .75; margin-top: 4px; }
.legend { list-style: none; display: flex; flex-wrap: wrap; gap: 8px; }
.chip { padding: 3px 10px; border-radius: 999px; font-size: .78rem; color: #333; }

/* sticky 章节导航 */
.secnav {
  position: sticky; top: 0; z-index: 10;
  background: #fff; border-bottom: 1px solid var(--border);
  display: flex; flex-wrap: wrap; gap: 4px; padding: 8px 24px;
}
.secnav a {
  color: var(--navy); text-decoration: none;
  font-size: .85rem; padding: 4px 12px; border-radius: 6px;
}
.secnav a:hover { background: var(--trans-bg); }

/* 主体 */
main { max-width: 1280px; margin: 0 auto; padding: 28px 24px 8px; }
.sec-title {
  font-size: 1.1rem; color: var(--navy);
  border-bottom: 2px solid var(--border); padding-bottom: 6px; margin-bottom: 18px;
}
.grid {
  display: grid; grid-template-columns: minmax(0, 1.35fr) minmax(0, 1fr);
  gap: 24px; align-items: start;
}

/* 左栏：段落 */
.passage { margin-bottom: 22px; }
.passage .en {
  font-family: Lora, Georgia, "Times New Roman", serif;
  font-size: .98rem; line-height: 1.85; text-align: justify;
}
.passage .trans {
  margin-top: 10px; padding: 10px 14px;
  background: var(--trans-bg);
  border-left: 3px solid var(--trans-bar);
  border-bottom: 1px solid var(--border);
  font-size: .92rem; color: #3d3d3d;
}

/* 五色高亮（只用在这五个类上） */
.hl-thesis     { background: var(--hl-thesis); }
.hl-concept    { background: var(--hl-concept); }
.hl-evidence   { background: var(--hl-evidence); }
.hl-concession { background: var(--hl-concession); }
.hl-method     { background: var(--hl-method); }
.hl-thesis, .hl-concept, .hl-evidence, .hl-concession, .hl-method {
  padding: 0 3px; border-radius: 3px;
}

/* 右栏：批注卡片 */
.note {
  background: var(--card-bg);
  border: 1px solid var(--border);
  border-left: 4px solid var(--border);
  border-radius: 8px;
  padding: 12px 14px; margin-bottom: 22px;
  font-size: .88rem;
}
/* 卡片左色边按该段主维度覆盖 */
.note-thesis     { border-left-color: #e3c23c; }
.note-concept    { border-left-color: #d06a6a; }
.note-evidence   { border-left-color: #5b7fc2; }
.note-concession { border-left-color: #5f9e5f; }
.note-method     { border-left-color: #8d6bb8; }
.note-no { font-size: .74rem; color: var(--muted); margin-bottom: 6px; }
.note-field { margin-bottom: 6px; }
.note-field b { color: var(--navy); font-weight: 600; }
.note-field:last-child { margin-bottom: 0; }

/* 底部论证总览 */
.overview {
  max-width: 1280px; margin: 24px auto 0; padding: 28px 24px 48px;
  border-top: 3px solid var(--navy);
}
.overview h2 { color: var(--navy); margin-bottom: 18px; }
.skeleton {
  display: grid; grid-template-columns: repeat(6, 1fr); gap: 10px;
}
.sk-step {
  background: #fff; border: 1px solid var(--border); border-top: 3px solid var(--navy);
  border-radius: 8px; padding: 12px 10px;
}
.sk-num {
  display: inline-block; width: 22px; height: 22px; line-height: 22px;
  border-radius: 50%; background: var(--navy); color: #fff;
  text-align: center; font-size: .72rem; margin-bottom: 6px;
}
.sk-step h4 { font-size: .86rem; margin-bottom: 4px; color: var(--navy); }
.sk-step p { font-size: .78rem; color: #555; }
.verdicts { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; margin-top: 16px; }
.verdict {
  background: var(--card-bg); border: 1px solid var(--border);
  border-radius: 8px; padding: 12px 14px; font-size: .86rem;
}
.verdict b { display: block; margin-bottom: 4px; color: var(--navy); }
.verdict-thesis { border-left: 4px solid #e3c23c; }
.verdict-strong { border-left: 4px solid #5f9e5f; }
.verdict-weak   { border-left: 4px solid #d06a6a; }

/* 响应式：移动端单栏，批注跟在对应段落之后 */
@media (max-width: 900px) {
  .grid { grid-template-columns: 1fr; }
  .skeleton { grid-template-columns: repeat(2, 1fr); }
  .verdicts { grid-template-columns: 1fr; }
  .topbar { padding: 16px; }
}

/* 打印：去导航、单栏、不跨页断段 */
@media print {
  .topbar, .secnav { display: none; }
  body { background: #fff; }
  .grid { grid-template-columns: 1fr; }
  .passage, .note, .sk-step, .verdict { break-inside: avoid; }
}
```

## 段落与批注的对应约定

- 左栏每个段落 `<div class="passage" id="p{N}">`，右栏对应卡片 `<aside class="note" data-for="p{N}">`，编号从 1 起全文连续
- 卡片按该段**主要论证维度**加一个左色边类：`note-thesis` / `note-concept` / `note-evidence` / `note-concession` / `note-method`
- 一段可含多色高亮；无主维度的过渡段（如「本文剩余部分安排如下」）可标注为 thesis 或 concept，批注功能写「过渡/导航段」
- 批注卡片的「技巧或漏洞」必须给出具体依据（引用哪句、哪个数字、与哪节不一致），无依据的批评不写

## 生成自检清单

- [ ] 单文件、零外部依赖（无 CDN link/script）
- [ ] 五色只用于五个语义类，图例与用法一致
- [ ] 每段英文原文逐字来自论文，中文意译不加高亮
- [ ] 段落与批注卡片一一对应，编号连续
- [ ] 章节导航锚点与 section id 全部匹配，无死链
- [ ] 底部六格骨架 + 三块判决齐全
- [ ] 关键数据均标注所在章节
- [ ] 900px 以下变单栏
