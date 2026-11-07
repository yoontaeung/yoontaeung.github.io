---
layout: researcher
permalink: /
title: "About me"
excerpt: "Taeung Yoon studies AI transparency and accountability through verifiable systems, training-data auditing, and LLM evaluation. Computer Science Ph.D. student at UIUC."
profile_home: true
author_profile: false
redirect_from:
  - /about/
  - /about.html
---

<aside class="profile" id="about" aria-labelledby="name">
  <div class="profile-heading">
    <h1 id="name">{{ site.author.name }}</h1>
    <p class="pronunciation">Pronounced “Teh-oong”</p>
    <figure class="portrait">
      <img src="{{ site.author.avatar | prepend: '/images/' | relative_url }}" alt="{{ site.author.name }}" width="440" height="550">
    </figure>
    <p class="role">Ph.D. student, Computer Science</p>
    <p class="affiliation">University of Illinois Urbana-Champaign</p>
  </div>
  <div class="contact" aria-label="Contact and profiles">
    <a href="mailto:{{ site.author.email }}">Email</a>
    <a href="{{ site.author.googlescholar }}">Scholar</a>
    <a href="https://github.com/{{ site.author.github }}">GitHub</a>
  </div>
</aside>

<div class="research-content">
<section class="introduction" aria-label="About and research">
  <div class="bio">
    <p>I'm a second-year Ph.D. student at the University of Illinois <span class="institution-tail">Urbana-Champaign<img class="institution-logo" src="{{ '/images/institutions/illinois.svg' | relative_url }}" alt="" aria-hidden="true" width="13" height="18"></span>, advised by <a href="https://www.xiaojingliao.com/">Xiaojing Liao</a>.</p>
    <p>My research centers on <strong>AI transparency and accountability</strong>, with a focus on large language models. I study how to establish evidence about the data models learn from, the computations they perform, and the capabilities they demonstrate. I approach these questions from both systems and model perspectives, combining systems research, cryptography, and empirical measurement.</p>
    <p>Previously, I received my M.S. in Computer Science from <span class="institution-tail">KAIST<img class="institution-logo" src="{{ '/images/institutions/kaist.png' | relative_url }}" alt="" aria-hidden="true" width="18" height="18"></span>, where I worked on blockchain security with <a href="https://netsp.kaist.ac.kr/">Min Suk Kang</a> in the Network Security and Privacy Lab. I received my B.S. in Computer Science from <span class="institution-tail">Yonsei University<img class="institution-logo" src="{{ '/images/institutions/yonsei.png' | relative_url }}" alt="" aria-hidden="true" width="23" height="18"></span>.</p>
  </div>
  <div class="research-interests" aria-labelledby="interests-heading">
    <h2 id="interests-heading">Research interests</h2>
    <ul>
      <li><strong>Verifiable AI systems</strong><span>: provable LLM inference, zkML, and GPU-accelerated proving. I am developing <strong>zk-KernelBench</strong> to evaluate how effectively LLM agents generate GPU kernels for ZK proving workloads.</span></li>
      <li><strong>Training-data provenance</strong><span>: studying membership-preserving knowledge distillation and membership inference on distilled models for efficient, privacy-preserving audits of LLM training data.</span></li>
      <li><strong>LLM evaluation and measurement</strong><span>: developing methods to evaluate benchmarks reliably and measure their influence.</span></li>
    </ul>
    <p class="related-research">I also explore information-flow control and verifiable tool use in multi-agent systems.</p>
  </div>
</section>
<section class="section" id="publications" aria-labelledby="pub-heading">
  <h2 class="section-heading" id="pub-heading">Publications</h2>
  <ol class="publications">
    <li class="paper">
      <time class="paper-year" datetime="2026">2026</time>
      <div>
        <h3 class="paper-title">Bits Under ZK-LLM: Evaluating Zero-Knowledge-Friendly Quantization for Verifiable Private LLM Inference</h3>
        <p class="authors"><strong>Taeung Yoon</strong>, Yupeng Zhang, Xiaojing Liao</p>
        <p class="paper-venue">Preprint · Under review</p>
        <p class="paper-description">A systematic study of how quantization precision affects LLM utility and the cost of zero-knowledge proofs.</p>
        <p class="paper-links"><a href="https://arxiv.org/abs/2609.36437">paper</a></p>
      </div>
    </li>
    <li class="paper">
      <time class="paper-year" datetime="2026">2026</time>
      <div>
        <h3 class="paper-title">Privacy-Preserving Distributed Virtual Network Embedding</h3>
        <p class="authors"><strong>Taeung Yoon</strong>, Lincoln Thurlow</p>
        <p class="paper-venue">Cyber Security Experimentation and Test Workshop (CSET) 2026 · To appear</p>
        <p class="paper-note">Acceptance rate: 37%</p>
        <p class="paper-description">3pViNE uses zero-knowledge proofs to verify valid virtual network embeddings across multiple domains without revealing providers’ private network topologies.</p>
      </div>
    </li>
    <li class="paper">
      <time class="paper-year" datetime="2025">2025</time>
      <div>
        <h3 class="paper-title">On Frontrunning Risks in Batch-Order Fair Systems for Blockchains</h3>
        <p class="authors">Eunchan Park*, <strong>Taeung Yoon*</strong>, Hocheol Nam, Deepak Maram, Min Suk Kang</p>
        <p class="paper-venue">ACM Conference on Computer and Communications Security (CCS) · October 2025</p>
        <p class="paper-note">* Equal contribution · Acceptance rate: 13.9%</p>
        <p class="paper-description">The Ambush attack exposes frontrunning risks in batch-order fair systems; delayed gossiping helps mitigate these risks while preserving throughput.</p>
        <p class="paper-links"><a href="https://dl.acm.org/doi/10.1145/3719027.3744879">paper</a></p>
      </div>
    </li>
    <li class="paper">
      <time class="paper-year" datetime="2023">2023</time>
      <div>
        <h3 class="paper-title">Partitioning Ethereum without Eclipsing It</h3>
        <p class="authors">Hwanjo Heo, Seungwon Woo, <strong>Taeung Yoon</strong>, Min Suk Kang, Seungwon Shin</p>
        <p class="paper-venue">Network and Distributed System Security Symposium (NDSS) · February 2023</p>
        <p class="paper-note">Acceptance rate: 16.4%</p>
        <p class="paper-description">Gethlighting partitions Ethereum nodes without controlling every peer connection; the work also develops countermeasures.</p>
        <p class="paper-links"><a href="https://www.ndss-symposium.org/ndss-paper/partitioning-ethereum-without-eclipsing-it/">paper</a></p>
      </div>
    </li>
  </ol>
</section>
<section class="section" id="news" aria-labelledby="news-heading">
  <h2 class="section-heading" id="news-heading">News</h2>
  <div>
    <ul class="news">
      <li><time datetime="2026-10">Oct 2026</time><span>Our paper “Privacy-Preserving Distributed Virtual Network Embedding” was accepted to CSET 2026!</span></li>
      <li><time datetime="2026-10">Oct 2026</time><span>Serving on the Program Committee of CSET 2026.</span></li>
      <li><time datetime="2026-09">Sep 2026</time><span>Our preprint “Bits Under ZK-LLM: Evaluating Zero-Knowledge-Friendly Quantization for Verifiable Private LLM Inference” is available on <a href="https://arxiv.org/abs/2609.36437">arXiv</a>.</span></li>
    </ul>
    <details><summary>Earlier updates</summary><ul class="news">
      <li><time datetime="2026-06">Jun 2026</time><span>Joined the SPHERE project at USC-ISI as a research intern.</span></li>
      <li><time datetime="2025-08">Aug 2025</time><span>Started my Ph.D. at the <a href="https://siebelschool.illinois.edu/">University of Illinois Urbana-Champaign</a>.</span></li>
      <li><time datetime="2025-06">Jun 2025</time><span>Started a research internship at the <a href="https://www.mpi-sp.org/">Max Planck Institute for Security and Privacy</a> in Bochum, Germany.</span></li>
      <li><time datetime="2025-05">May 2025</time><span>“On Frontrunning Risks in Batch-Order Fair Systems for Blockchains” was accepted to <a href="https://www.sigsac.org/ccs/CCS2025/">ACM CCS 2025</a>.</span></li>
    </ul></details>
  </div>
</section>
<section class="section" aria-labelledby="service-heading">
  <h2 class="section-heading" id="service-heading">Service &amp; awards</h2>
  <div class="records">
    <div class="record"><div><p>Program Committee</p><p class="organization">Cyber Security Experimentation and Test (CSET)</p></div><span class="date">2026</span></div>
    <div class="record"><div><p>Undergraduate Research Apprenticeship Program Mentor</p><p class="organization">University of Illinois Urbana-Champaign</p></div><span class="date">2026–2027</span></div>
    <div class="record"><div><p>CS Graduate Ambassador</p><p class="organization">University of Illinois Urbana-Champaign</p></div><span class="date">Mar 2026</span></div>
    <div class="record"><div><p>Artifact Evaluation Committee</p><p class="organization">USENIX Security 2026 · NDSS 2027</p></div><span class="date">2026</span></div>
    <div class="record"><div><p>Travel Grant</p><p class="organization">ACM CCS</p></div><span class="date">Oct 2025</span></div>
    <div class="record"><div><p>2nd Place · Near Horizon AI Hackathon</p><p class="organization">USD 4K</p></div><span class="date">Sep 2024</span></div>
  </div>
</section>
<section class="section" aria-labelledby="teaching-heading">
  <h2 class="section-heading" id="teaching-heading">Teaching</h2>
  <div class="records">
    <div class="record"><div><p>Advanced Topics in Security, Privacy, and Machine Learning</p><p class="organization">Teaching Assistant · UIUC</p></div><span class="date">Fall 2026</span></div>
    <div class="record"><div><p>Introduction to Computer Networks</p><p class="organization">Teaching Assistant · KAIST</p></div><span class="date">Fall 2024</span></div>
    <div class="record"><div><p>Introduction to Information Security</p><p class="organization">Teaching Assistant · KAIST</p></div><span class="date">Spring 2024</span></div>
  </div>
</section>

</div>

<!-- Current Projects
------
- **AI Agents for GPU Optimization in ZK Proving**: Developing a benchmark to evaluate how effectively LLM agents can generate efficient GPU kernels for ZK proving workloads.
- **Security for Multi-Agent AI Systems**: Designing security mechanisms for multi-agent systems, including information-flow control and privacy-preserving verification of whitelisted tool usage using cryptographic proofs. -->
