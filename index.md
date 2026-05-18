---
layout: home
---

<!-- Hero Section -->
<section class="hero">
  <img src="https://avatars.githubusercontent.com/u/{{ site.github_username }}" alt="avatar" class="hero-avatar" onerror="this.style.display='none'">
  <h1>LBK</h1>
  <p class="subtitle">AI 视觉算法工程师 · NPU 模型转换</p>
  <p class="tagline">
    专注嵌入式 AI 视觉算法研发与 NPU 模型部署优化。<br>
    工作涉及 AI 视觉核心库 (algcore) 架构、多品牌 NPU 模型转换、
    C++/Python 开发与 ARM 平台交叉编译。
  </p>

  <div class="hero-badges">
    <span class="badge"><i class="fas fa-microchip"></i> NPU 部署</span>
    <span class="badge"><i class="fas fa-eye"></i> 计算机视觉</span>
    <span class="badge"><i class="fas fa-code"></i> C++ / Python</span>
    <span class="badge"><i class="fas fa-robot"></i> AI Agent</span>
  </div>

  <div class="hero-links">
    <a href="{{ '/about' | relative_url }}" class="btn btn-primary">
      <i class="fas fa-user"></i> 关于我
    </a>
    <a href="{{ '/projects' | relative_url }}" class="btn btn-outline">
      <i class="fas fa-code"></i> 项目
    </a>
    <a href="https://github.com/{{ site.github_username }}" target="_blank" rel="noopener" class="btn btn-outline">
      <i class="fab fa-github"></i> GitHub
    </a>
  </div>
</section>

<!-- Stats Section -->
<section class="section section-alt">
  <div class="container">
    <div class="stats-grid">
      <div class="stat-card">
        <div class="stat-number">25+</div>
        <div class="stat-label">GitHub 仓库</div>
      </div>
      <div class="stat-card">
        <div class="stat-number">3+</div>
        <div class="stat-label">NPU 平台</div>
      </div>
      <div class="stat-card">
        <div class="stat-number">3</div>
        <div class="stat-label">关注者</div>
      </div>
    </div>
  </div>
</section>

<!-- Featured Projects -->
<section class="section">
  <div class="container">
    <h2 class="section-title">精选项目</h2>
    <div class="project-grid">
      <a href="https://github.com/popcornGit/" target="_blank" rel="noopener" class="project-card">
        <div class="project-icon"><i class="fas fa-brain"></i></div>
        <h3>algcore · AI 视觉核心库</h3>
        <p>面向 AX650N 平台的 AI 视觉推理核心库。集成目标检测、人脸识别、TTS 语音合成等模块，支持多模型流水线调度。</p>
        <div class="project-tech">
          <span>C++</span>
          <span>AX650N</span>
          <span>Kokoro TTS</span>
          <span>ARM64</span>
        </div>
      </a>

      <a href="https://github.com/popcornGit/" target="_blank" rel="noopener" class="project-card">
        <div class="project-icon"><i class="fas fa-microchip"></i></div>
        <h3>NPU 模型转换实战</h3>
        <p>从 PyTorch → ONNX → 各品牌 NPU 格式的完整转换流程。涵盖瑞芯微 RKNN、爱芯元智 AXModel、地平线等平台。</p>
        <div class="project-tech">
          <span>RKNN</span>
          <span>Pulsar2</span>
          <span>ONNX</span>
          <span>YOLO11s</span>
        </div>
      </a>

      <a href="https://github.com/popcornGit/LBK-Intelligent-dialogue" target="_blank" rel="noopener" class="project-card">
        <div class="project-icon"><i class="fas fa-comments"></i></div>
        <h3>智能对话系统</h3>
        <p>基于深度学习的智能对话交互系统，融合自然语言理解与多轮对话管理。</p>
        <div class="project-tech">
          <span>Python</span>
          <span>NLP</span>
          <span>Deep Learning</span>
        </div>
      </a>

      <a href="https://github.com/popcornGit/LBK-Bert-Text-Classification" target="_blank" rel="noopener" class="project-card">
        <div class="project-icon"><i class="fas fa-tags"></i></div>
        <h3>BERT 文本分类</h3>
        <p>基于 BERT 模型的文本分类实践，涵盖情感分析、意图识别等场景。</p>
        <div class="project-tech">
          <span>BERT</span>
          <span>PyTorch</span>
          <span>NLP</span>
        </div>
      </a>
    </div>
    <div class="view-all">
      <a href="{{ '/projects' | relative_url }}" class="btn btn-outline">
        <i class="fas fa-arrow-right"></i> 查看全部项目
      </a>
    </div>
  </div>
</section>

<!-- Recent Blog Posts -->
<section class="section section-alt">
  <div class="container">
    <h2 class="section-title">最新文章</h2>
    <div class="blog-list">
      {% for post in site.posts limit:3 %}
      <a href="{{ post.url | relative_url }}" class="blog-card">
        <div class="blog-date">{{ post.date | date: "%Y-%m-%d" }}</div>
        <h3>{{ post.title }}</h3>
        {% if post.excerpt %}
        <p>{{ post.excerpt | strip_html | truncate: 120 }}</p>
        {% endif %}
        {% if post.tags %}
        <div class="blog-tags">
          {% for tag in post.tags %}
          <span>{{ tag }}</span>
          {% endfor %}
        </div>
        {% endif %}
      </a>
      {% else %}
      <p style="text-align:center; color: var(--text-muted); padding: 40px 0;">
        <i class="fas fa-pen-fancy" style="font-size: 2rem; display: block; margin-bottom: 12px;"></i>
        暂无文章，请到 <a href="{{ '/blog' | relative_url }}" style="color: var(--accent);">博客页面</a> 浏览。
      </p>
      {% endfor %}
    </div>
    <div class="view-all">
      <a href="{{ '/blog' | relative_url }}" class="btn btn-outline">
        <i class="fas fa-arrow-right"></i> 浏览所有文章
      </a>
    </div>
  </div>
</section>
