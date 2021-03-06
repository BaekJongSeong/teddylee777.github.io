---
layout: page
title: "최근접 이웃 (KNN) 알고리즘을 활용한 분류"
description: "최근접 이웃 (KNN) 알고리즘을 활용한 분류 방법에 대해 알아보겠습니다."
headline: "최근접 이웃 (KNN) 알고리즘을 활용한 분류 방법에 대해 알아보겠습니다."
categories: scikit-learn
tags: [python, tensorflow, scikit-learn, knn, 최근접 이웃 알고리즘, K-Nearest Neighbors, 텐서플로우, data science, 데이터 분석, 딥러닝, 딥러닝 자격증, 머신러닝, 빅데이터, 테디노트]
comments: true
published: true
typora-copy-images-to: ../images/2020-09-24
---



K-Nearest Neighbors 이른바, 최근접 이웃 분류 알고리즘을 활용한 간단한 머신러닝 분류 문제를 풀어보도록 하겠습니다. 알고리즘은 동작 원리는 매우 직관적이고 단순 합니다. 이해는 어렵지 않으나, 복잡한 분류 문제에 있어서는 모델의 성능에 대한 큰 기대를 하기 어렵습니다.

scikit-learn 내부에 구현된 알고리즘으로 손쉽게 머신러닝을 돌려볼 수 있습니다. 

`sklearn.neighbors` 패키지 안에 **KNeighborsClassifier** 을 import 할 수 있습니다.

자세한 설명은 실습 코드 내에 기재해 놓았습니다. 핵심 hyperparameter는 `n_neighbors` 입니다.

## 코드

![Colab으로 열기](../images/2020-09-24/colab_logo_32px.png) [Colab으로 열기](https://colab.research.google.com/github/teddylee777/machine-learning/blob/master/10-scikit-learn/05-%EB%B6%84%EB%A5%98%20(Classifications).ipynb)

![GitHub](../images/2020-09-24/GitHub-Mark-32px.png) [GitHub에서 소스보기](https://github.com/teddylee777/machine-learning/blob/master/10-scikit-learn/05-%EB%B6%84%EB%A5%98%20(Classifications).ipynb)

<br/>



<body>

<div class="border-box-sizing" id="notebook" >
<div class="container" id="notebook-container">
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">IPython.display</span> <span class="k">import</span> <span class="n">Image</span>
<span class="kn">import</span> <span class="nn">numpy</span> <span class="k">as</span> <span class="nn">np</span>
<span class="kn">import</span> <span class="nn">pandas</span> <span class="k">as</span> <span class="nn">pd</span>
<span class="kn">import</span> <span class="nn">matplotlib.pyplot</span> <span class="k">as</span> <span class="nn">plt</span>
<span class="kn">import</span> <span class="nn">seaborn</span> <span class="k">as</span> <span class="nn">sns</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h1 id="분류-(Classification)">분류 (Classification)</h1>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="K-Nearest-Neighbors-(k-최근접-이웃-분류-알고리즘)">K Nearest Neighbors (k-최근접 이웃 분류 알고리즘)</h2>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>가장 중요한 hyperparameter인 <strong>K</strong>값은 근처에 참고(reference)할 <strong>이웃의 숫자</strong></p>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<ul>
<li>k-최근접 이웃 분류 알고리즘</li>
<li>가장 고전적이고 직관적인 머신러닝 분류 알고리즘</li>
<li>기하학적 거리 분류기</li>
<li>가장 <strong>가깝게 위치하는 멤버로 분류</strong>하는 방식</li>
</ul>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">Image</span><span class="p">(</span><span class="n">url</span><span class="o">=</span><span class="s1">'https://miro.medium.com/max/405/0*QyWp7J6eSz0tayc0.png'</span><span class="p">,</span> <span class="n">width</span><span class="o">=</span><span class="mi">500</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_html rendered_html output_subarea output_execute_result">
<img src="https://miro.medium.com/max/405/0*QyWp7J6eSz0tayc0.png" width="500"/>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="더미-데이터를-활용한-분류-원리-이해">더미 데이터를 활용한 분류 원리 이해</h2>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">sklearn.neighbors</span> <span class="k">import</span> <span class="n">KNeighborsClassifier</span>
<span class="kn">from</span> <span class="nn">sklearn.datasets</span> <span class="k">import</span> <span class="n">make_classification</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">fig</span><span class="p">,</span> <span class="n">axes</span> <span class="o">=</span> <span class="n">plt</span><span class="o">.</span><span class="n">subplots</span><span class="p">(</span><span class="mi">3</span><span class="p">,</span> <span class="mi">3</span><span class="p">)</span>
<span class="n">fig</span><span class="o">.</span><span class="n">set_size_inches</span><span class="p">(</span><span class="mi">15</span><span class="p">,</span> <span class="mi">15</span><span class="p">)</span>

<span class="k">for</span> <span class="n">i</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="mi">9</span><span class="p">):</span>
    <span class="c1"># 더미 데이터 생성</span>
    <span class="n">X</span><span class="p">,</span> <span class="n">y</span> <span class="o">=</span> <span class="n">make_classification</span><span class="p">(</span><span class="n">n_samples</span><span class="o">=</span><span class="mi">50</span><span class="p">,</span> <span class="n">n_features</span><span class="o">=</span><span class="mi">2</span><span class="p">,</span> <span class="n">n_informative</span><span class="o">=</span><span class="mi">2</span><span class="p">,</span> <span class="n">n_redundant</span><span class="o">=</span><span class="mi">0</span><span class="p">,</span> <span class="n">random_state</span><span class="o">=</span><span class="mi">30</span><span class="p">)</span>

    <span class="n">blue</span> <span class="o">=</span> <span class="n">X</span><span class="p">[</span><span class="n">y</span><span class="o">==</span><span class="mi">0</span><span class="p">]</span>
    <span class="n">red</span> <span class="o">=</span> <span class="n">X</span><span class="p">[</span><span class="n">y</span><span class="o">==</span><span class="mi">1</span><span class="p">]</span>
    
    <span class="c1"># 랜덤한 새로운 점 생성</span>
    <span class="n">newcomer</span> <span class="o">=</span> <span class="n">np</span><span class="o">.</span><span class="n">random</span><span class="o">.</span><span class="n">randn</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">2</span><span class="p">)</span>
    
    <span class="c1"># K</span>
    <span class="n">K</span> <span class="o">=</span> <span class="mi">3</span><span class="o">*</span><span class="p">(</span><span class="n">i</span><span class="o">//</span><span class="mi">3</span><span class="o">+</span><span class="mi">1</span><span class="p">)</span>
    
    <span class="n">axes</span><span class="p">[</span><span class="n">i</span><span class="o">//</span><span class="mi">3</span><span class="p">,</span> <span class="n">i</span><span class="o">%</span><span class="k">3</span>].scatter(red[:,0], red[:, 1], 80, 'r', '^')
    <span class="n">axes</span><span class="p">[</span><span class="n">i</span><span class="o">//</span><span class="mi">3</span><span class="p">,</span> <span class="n">i</span><span class="o">%</span><span class="k">3</span>].scatter(blue[:,0], blue[:, 1], 80, 'b', '^')
    <span class="n">axes</span><span class="p">[</span><span class="n">i</span><span class="o">//</span><span class="mi">3</span><span class="p">,</span> <span class="n">i</span><span class="o">%</span><span class="k">3</span>].scatter(newcomer[:, 0], newcomer[:, 1], 80, 'g', 'o')
    
    <span class="c1"># n_neighbors=3</span>
    <span class="n">knn</span> <span class="o">=</span> <span class="n">KNeighborsClassifier</span><span class="p">(</span><span class="n">n_neighbors</span><span class="o">=</span><span class="mi">3</span><span class="o">*</span><span class="p">(</span><span class="n">i</span><span class="o">//</span><span class="mi">3</span><span class="o">+</span><span class="mi">1</span><span class="p">))</span>
    <span class="n">knn</span><span class="o">.</span><span class="n">fit</span><span class="p">(</span><span class="n">X</span><span class="p">,</span> <span class="n">y</span><span class="p">)</span>
    <span class="n">pred</span> <span class="o">=</span> <span class="n">knn</span><span class="o">.</span><span class="n">predict</span><span class="p">(</span><span class="n">newcomer</span><span class="p">)</span>
    
    <span class="c1"># 표기</span>
    <span class="n">axes</span><span class="p">[</span><span class="n">i</span><span class="o">//</span><span class="mi">3</span><span class="p">,</span> <span class="n">i</span><span class="o">%</span><span class="k">3</span>].annotate('red' if pred==1 else 'blue', xy=newcomer[0], xytext=(newcomer[0]), fontsize=12)

<span class="n">plt</span><span class="o">.</span><span class="n">tight_layout</span><span class="p">()</span>
<span class="n">plt</span><span class="o">.</span><span class="n">show</span><span class="p">()</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">
<div class="prompt"></div>
<div class="output_png output_subarea">
<img src="data:image/png;base64,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
"/>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="mnist-(손글씨)-데이터셋을-활용한-분류">mnist (손글씨) 데이터셋을 활용한 분류</h2>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>mnist (손글씨) 데이터셋을 활용하여 0~9까지 분류하는 분류기를 만듭니다.</p>
<p><code>sklearn.datasets</code> 보다 고해상도 이미지이기 때문에 <code>tensorflow.keras.datasets</code>을 활용합니다.</p>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">tensorflow.keras.datasets</span> <span class="k">import</span> <span class="n">mnist</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="p">(</span><span class="n">x_digit</span><span class="p">,</span> <span class="n">y_digit</span><span class="p">),</span> <span class="p">(</span><span class="n">_</span><span class="p">,</span> <span class="n">_15</span><span class="p">)</span> <span class="o">=</span> <span class="n">mnist</span><span class="o">.</span><span class="n">load_data</span><span class="p">()</span>
<span class="n">x_digit</span><span class="p">,</span> <span class="n">y_digit</span> <span class="o">=</span> <span class="n">x_digit</span><span class="p">[:</span><span class="mi">5000</span><span class="p">],</span> <span class="n">y_digit</span><span class="p">[:</span><span class="mi">5000</span><span class="p">]</span>
<span class="n">x_digit</span> <span class="o">=</span> <span class="n">x_digit</span><span class="o">.</span><span class="n">reshape</span><span class="p">(</span><span class="o">-</span><span class="mi">1</span><span class="p">,</span> <span class="mi">28</span><span class="o">*</span><span class="mi">28</span><span class="p">)</span>
<span class="n">x_digit</span><span class="o">.</span><span class="n">shape</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>(5000, 784)</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="mnist-(손글씨)-데이터-시각화">mnist (손글씨) 데이터 시각화</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">w</span><span class="p">,</span> <span class="n">h</span> <span class="o">=</span> <span class="mi">2</span><span class="p">,</span> <span class="mi">5</span>
<span class="n">fig</span><span class="p">,</span> <span class="n">axes</span> <span class="o">=</span> <span class="n">plt</span><span class="o">.</span><span class="n">subplots</span><span class="p">(</span><span class="n">w</span><span class="p">,</span> <span class="n">h</span><span class="p">)</span>
<span class="n">fig</span><span class="o">.</span><span class="n">set_size_inches</span><span class="p">(</span><span class="mi">12</span><span class="p">,</span> <span class="mi">6</span><span class="p">)</span>
<span class="k">for</span> <span class="n">i</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="n">w</span><span class="o">*</span><span class="n">h</span><span class="p">):</span>
    <span class="n">axes</span><span class="p">[</span><span class="n">i</span><span class="o">//</span><span class="n">h</span><span class="p">,</span> <span class="n">i</span><span class="o">%</span><span class="k">h</span>].imshow(x_digit[i].reshape(-1, 28))
    <span class="n">axes</span><span class="p">[</span><span class="n">i</span><span class="o">//</span><span class="n">h</span><span class="p">,</span> <span class="n">i</span><span class="o">%</span><span class="k">h</span>].set_title(y_digit[i], fontsize=20)
    <span class="n">axes</span><span class="p">[</span><span class="n">i</span><span class="o">//</span><span class="n">h</span><span class="p">,</span> <span class="n">i</span><span class="o">%</span><span class="k">h</span>].axis('off')
<span class="n">plt</span><span class="o">.</span><span class="n">tight_layout</span><span class="p">()</span>
<span class="n">plt</span><span class="o">.</span><span class="n">show</span><span class="p">()</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">
<div class="prompt"></div>
<div class="output_png output_subarea">
<img src="data:image/png;base64,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
"/>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="데이터-셋-분할">데이터 셋 분할</h2>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>학습용(train) 데이터셋과 검증 (혹은 테스트)용 데이터 셋을 분할 합니다.</p>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">sklearn.model_selection</span> <span class="k">import</span> <span class="n">train_test_split</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">x_train</span><span class="p">,</span> <span class="n">x_valid</span><span class="p">,</span> <span class="n">y_train</span><span class="p">,</span> <span class="n">y_valid</span> <span class="o">=</span> <span class="n">train_test_split</span><span class="p">(</span><span class="n">x_digit</span><span class="p">,</span> <span class="n">y_digit</span><span class="p">,</span> <span class="n">stratify</span><span class="o">=</span><span class="n">y_digit</span><span class="p">,</span> <span class="n">random_state</span><span class="o">=</span><span class="mi">30</span><span class="p">,</span> <span class="n">test_size</span><span class="o">=</span><span class="mf">0.1</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">x_train</span><span class="o">.</span><span class="n">shape</span><span class="p">,</span> <span class="n">x_valid</span><span class="o">.</span><span class="n">shape</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>((4500, 784), (500, 784))</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="모델-정의">모델 정의</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p><code>KNeighborsClassifier</code>를 정의 하고 핵심 hyperparameter 인 <code>n_neighbors</code>를 지정합니다.</p>
<p>임의로 5개의 이웃을 보도록 <code>n_neighbors=5</code>로 지정하겠습니다.</p>
<p>그리고, <code>n_jobs</code> 옵션은 학습에 사용할 코어의 숫자를 지정합니다. -1로 지정하면, 모든 코어를 사용하도록 합니다.</p>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">knn</span> <span class="o">=</span> <span class="n">KNeighborsClassifier</span><span class="p">(</span><span class="n">n_neighbors</span><span class="o">=</span><span class="mi">5</span><span class="p">,</span> <span class="n">n_jobs</span><span class="o">=-</span><span class="mi">1</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="학습-(fit)">학습 (fit)</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">knn</span><span class="o">.</span><span class="n">fit</span><span class="p">(</span><span class="n">x_train</span><span class="p">,</span> <span class="n">y_train</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
                     metric_params=None, n_jobs=-1, n_neighbors=5, p=2,
                     weights='uniform')</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="예측-(predict)">예측 (predict)</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">prediction</span> <span class="o">=</span> <span class="n">knn</span><span class="o">.</span><span class="n">predict</span><span class="p">(</span><span class="n">x_valid</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="검증-(evaluation)">검증 (evaluation)</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>정확도 (Accuracy) 산출</p>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="p">(</span><span class="n">prediction</span> <span class="o">==</span> <span class="n">y_valid</span><span class="p">)</span><span class="o">.</span><span class="n">mean</span><span class="p">()</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>0.938</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">knn</span><span class="o">.</span><span class="n">score</span><span class="p">(</span><span class="n">x_valid</span><span class="p">,</span> <span class="n">y_valid</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>0.938</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="최적의-n_neighbors-찾기">최적의 n_neighbors 찾기</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="k">for</span> <span class="n">k</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">11</span><span class="p">):</span>
    <span class="n">knn</span> <span class="o">=</span> <span class="n">KNeighborsClassifier</span><span class="p">(</span><span class="n">n_neighbors</span><span class="o">=</span><span class="n">k</span><span class="p">,</span> <span class="n">n_jobs</span><span class="o">=-</span><span class="mi">1</span><span class="p">)</span>
    <span class="n">knn</span><span class="o">.</span><span class="n">fit</span><span class="p">(</span><span class="n">x_train</span><span class="p">,</span> <span class="n">y_train</span><span class="p">)</span>
    <span class="n">score</span> <span class="o">=</span> <span class="n">knn</span><span class="o">.</span><span class="n">score</span><span class="p">(</span><span class="n">x_valid</span><span class="p">,</span> <span class="n">y_valid</span><span class="p">)</span>
    <span class="nb">print</span><span class="p">(</span><span class="s1">'k: </span><span class="si">%d</span><span class="s1">, accuracy: </span><span class="si">%.2f</span><span class="s1">'</span> <span class="o">%</span> <span class="p">(</span><span class="n">k</span><span class="p">,</span> <span class="n">score</span><span class="o">*</span><span class="mi">100</span><span class="p">))</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">
<div class="prompt"></div>
<div class="output_subarea output_stream output_stdout output_text">
<pre>k: 1, accuracy: 95.60
k: 2, accuracy: 95.00
k: 3, accuracy: 94.60
k: 4, accuracy: 93.80
k: 5, accuracy: 93.80
k: 6, accuracy: 93.40
k: 7, accuracy: 93.40
k: 8, accuracy: 93.60
k: 9, accuracy: 92.80
k: 10, accuracy: 92.80
</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="Iris-붓꽃-데이터셋을-활용한-실습">Iris 붓꽃 데이터셋을 활용한 실습</h2>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="필요한-데이터셋-불러오기-(load_iris)">필요한 데이터셋 불러오기 (load_iris)</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="kn">from</span> <span class="nn">sklearn.datasets</span> <span class="k">import</span> <span class="n">load_iris</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">iris</span> <span class="o">=</span> <span class="n">load_iris</span><span class="p">()</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="데이터프레임-(DataFrame)-만들기">데이터프레임 (DataFrame) 만들기</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">df</span> <span class="o">=</span> <span class="n">pd</span><span class="o">.</span><span class="n">DataFrame</span><span class="p">(</span><span class="n">iris</span><span class="p">[</span><span class="s1">'data'</span><span class="p">],</span> <span class="n">columns</span><span class="o">=</span><span class="n">iris</span><span class="p">[</span><span class="s1">'feature_names'</span><span class="p">])</span>
<span class="n">df</span><span class="p">[</span><span class="s1">'target'</span><span class="p">]</span> <span class="o">=</span> <span class="n">iris</span><span class="p">[</span><span class="s1">'target'</span><span class="p">]</span>
<span class="n">df</span><span class="o">.</span><span class="n">head</span><span class="p">()</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_html rendered_html output_subarea output_execute_result">
<div>
<style scoped="">
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
<thead>
<tr style="text-align: right;">
<th></th>
<th>sepal length (cm)</th>
<th>sepal width (cm)</th>
<th>petal length (cm)</th>
<th>petal width (cm)</th>
<th>target</th>
</tr>
</thead>
<tbody>
<tr>
<th>0</th>
<td>5.1</td>
<td>3.5</td>
<td>1.4</td>
<td>0.2</td>
<td>0</td>
</tr>
<tr>
<th>1</th>
<td>4.9</td>
<td>3.0</td>
<td>1.4</td>
<td>0.2</td>
<td>0</td>
</tr>
<tr>
<th>2</th>
<td>4.7</td>
<td>3.2</td>
<td>1.3</td>
<td>0.2</td>
<td>0</td>
</tr>
<tr>
<th>3</th>
<td>4.6</td>
<td>3.1</td>
<td>1.5</td>
<td>0.2</td>
<td>0</td>
</tr>
<tr>
<th>4</th>
<td>5.0</td>
<td>3.6</td>
<td>1.4</td>
<td>0.2</td>
<td>0</td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h2 id="데이터-셋-분할">데이터 셋 분할</h2>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>학습용(train) 데이터셋과 검증 (혹은 테스트)용 데이터 셋을 분할 합니다.</p>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">x_train</span><span class="p">,</span> <span class="n">x_valid</span><span class="p">,</span> <span class="n">y_train</span><span class="p">,</span> <span class="n">y_valid</span> <span class="o">=</span> <span class="n">train_test_split</span><span class="p">(</span><span class="n">df</span><span class="o">.</span><span class="n">iloc</span><span class="p">[:,</span> <span class="p">:</span><span class="mi">4</span><span class="p">],</span> <span class="n">df</span><span class="p">[</span><span class="s1">'target'</span><span class="p">],</span> <span class="n">stratify</span><span class="o">=</span><span class="n">df</span><span class="p">[</span><span class="s1">'target'</span><span class="p">],</span> <span class="n">test_size</span><span class="o">=</span><span class="mf">0.2</span><span class="p">,</span> <span class="n">random_state</span><span class="o">=</span><span class="mi">30</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<p>잘 로드가 되었는지 <strong>shape를 확인</strong>하도록 합니다.</p>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">x_train</span><span class="o">.</span><span class="n">shape</span><span class="p">,</span> <span class="n">y_train</span><span class="o">.</span><span class="n">shape</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>((120, 4), (120,))</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">x_valid</span><span class="o">.</span><span class="n">shape</span><span class="p">,</span> <span class="n">y_valid</span><span class="o">.</span><span class="n">shape</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>((30, 4), (30,))</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="모델-정의">모델 정의</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">knn</span> <span class="o">=</span> <span class="n">KNeighborsClassifier</span><span class="p">()</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="학습-(fit)">학습 (fit)</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">knn</span><span class="o">.</span><span class="n">fit</span><span class="p">(</span><span class="n">x_train</span><span class="p">,</span> <span class="n">y_train</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
                     metric_params=None, n_jobs=None, n_neighbors=5, p=2,
                     weights='uniform')</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="검증-(evaluation)">검증 (evaluation)</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">prediction</span> <span class="o">=</span> <span class="n">knn</span><span class="o">.</span><span class="n">predict</span><span class="p">(</span><span class="n">x_valid</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="p">(</span><span class="n">prediction</span> <span class="o">==</span> <span class="n">y_valid</span><span class="p">)</span><span class="o">.</span><span class="n">mean</span><span class="p">()</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>0.9333333333333333</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="n">knn</span><span class="o">.</span><span class="n">score</span><span class="p">(</span><span class="n">x_valid</span><span class="p">,</span> <span class="n">y_valid</span><span class="p">)</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">

<div class="output_text output_subarea output_execute_result">
<pre>0.9333333333333333</pre>
</div>
</div>
</div>
</div>
</div>
<div class="cell border-box-sizing text_cell rendered"><div class="inner_cell">
<div class="text_cell_render border-box-sizing rendered_html">
<h3 id="최적의-k-값-찾기">최적의 k 값 찾기</h3>
</div>
</div>
</div>
<div class="cell border-box-sizing code_cell rendered">
<div class="input">

<div class="inner_cell">
<div class="input_area">
<div class="highlight hl-ipython3"><pre><span></span><span class="k">for</span> <span class="n">k</span> <span class="ow">in</span> <span class="nb">range</span><span class="p">(</span><span class="mi">1</span><span class="p">,</span> <span class="mi">11</span><span class="p">):</span>
    <span class="n">knn</span> <span class="o">=</span> <span class="n">KNeighborsClassifier</span><span class="p">(</span><span class="n">n_neighbors</span><span class="o">=</span><span class="n">k</span><span class="p">,</span> <span class="n">n_jobs</span><span class="o">=-</span><span class="mi">1</span><span class="p">)</span>
    <span class="n">knn</span><span class="o">.</span><span class="n">fit</span><span class="p">(</span><span class="n">x_train</span><span class="p">,</span> <span class="n">y_train</span><span class="p">)</span>
    <span class="n">score</span> <span class="o">=</span> <span class="n">knn</span><span class="o">.</span><span class="n">score</span><span class="p">(</span><span class="n">x_valid</span><span class="p">,</span> <span class="n">y_valid</span><span class="p">)</span>
    <span class="nb">print</span><span class="p">(</span><span class="s1">'k: </span><span class="si">%d</span><span class="s1">, accuracy: </span><span class="si">%.2f</span><span class="s1">'</span> <span class="o">%</span> <span class="p">(</span><span class="n">k</span><span class="p">,</span> <span class="n">score</span><span class="o">*</span><span class="mi">100</span><span class="p">))</span>
</pre></div>
</div>
</div>
</div>
<div class="output_wrapper">
<div class="output">
<div class="output_area">
<div class="prompt"></div>
<div class="output_subarea output_stream output_stdout output_text">
<pre>k: 1, accuracy: 96.67
k: 2, accuracy: 93.33
k: 3, accuracy: 96.67
k: 4, accuracy: 96.67
k: 5, accuracy: 93.33
k: 6, accuracy: 93.33
k: 7, accuracy: 96.67
k: 8, accuracy: 96.67
k: 9, accuracy: 96.67
k: 10, accuracy: 96.67
</pre>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</body>