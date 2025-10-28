## 📨 목차

- [🔍 설명 가능한 AI 기반 피싱 이메일 탐지](#-설명-가능한-ai-기반-피싱-이메일-탐지)
  - [🔹 개요](#-개요)
  - [🔹 주요 내용 및 특징](#-주요-내용-및-특징)
- [🎬 시연 영상](#-시연-영상)
- [🗂️ 시스템 아키텍처](#-시스템-아키텍처)
- [📁 파일 구성](#-파일-구성)
- [📊 데이터셋](#-데이터셋)
- [🧠 모델](#-모델)
  - [🔹 MLP](#-mlp)
  - [🔹 RoBERTa](#-roberta)
- [⚙️ 학습 파이프라인](#️-학습-파이프라인)
- [📈 성능 요약](#-성능-요약)
- [💡 장단점 및 향후 계획](#-장단점-및-향후-계획)
- [📄 라이선스](#-라이선스)

---

## 🔍 설명 가능한 AI 기반 피싱 이메일 탐지

> **Team [ExplainSec]**   
> **20202905 Hyunmin Park (박현민)**   
> **20200397 Seungjun You (유승준)**   


<img alt="project overview img" src="https://github.com/user-attachments/assets/9d15a902-d17a-4fa5-afd1-98db325a06fd" style="max-width: 100%;" />


### 🔹 개요 
최근 피싱 이메일은 ChatGPT와 같은 AI 모델로 생성되는 등 점점 더 정교해지고 있습니다. 이러한 위협에 대응하기 위해서는 단순히 정확한 탐지기뿐만 아니라, 이메일이 왜 탐지되었는지 인간 분석가가 이해할 수 있도록 **설명 가능한 모델**이 필요합니다.

본 프로젝트의 목적은 AI가 생성한 고도화된 피싱 이메일을 단순히 정확하게 탐지하는 데 그치지 않고, **AI가 왜 특정 이메일을 피싱으로 판단했는지 그 근거를 사용자에게 명확하게 설명하는 것**에 있습니다. 단순한 탐지/차단 결과만 제공하는 AI는 사용자의 신뢰를 얻기 힘든 '블랙박스 모델'에 머무를 수밖에 없습니다. 이에 본 시스템은 사용자가 AI의 판단 과정을 이해하고 신뢰할 수 있도록, **설명 가능하고 신뢰할 수 있는 AI 기반 보안 시스템** 구현을 목표로 합니다. 

본 프로젝트에서는 실제 피싱 이메일 데이터셋(Kaggle)을 활용하여, 먼저 전통적인 MLP(TF-IDF 기반) 모델과 최신 Transformer(RoBERTa) 모델을 구현하고 성능을 정밀하게 비교했습니다. 모델의 탐지 성능을 충분히 끌어올린 뒤, AI의 판별 근거가 투명하게 드러나도록 다양한 XAI(설명 가능한 인공지능) 기법을 적용해 해석 가능성을 높였습니다.

### 🔹 주요 내용 및 특징 
1. 실전 데이터셋을 바탕으로 MLP, RoBERTa 두 모델을 구축 후 정량 비교(정밀도/재현율/정확도 등) 및 성능 최적화
2. RoBERTa 모델 파인튜닝을 통해 문맥 이해 기반 98% F1-Score 달성 (최신 AI 모델의 효과 극대화)
3. XAI 기법(Layer Integrated Gradients, LIG)으로 판별에 영향력이 컸던 주요 단어(예: update, current 등)를 시각적으로 하이라이트 하여 분석가가 쉽게 판단 근거를 확인 가능하게 함
4. LIG 결과를 GPT API로 분석해 누구나 이해할 수 있는 “위험 결론·판단 근거·대응 가이드”가 담긴 자동 분석 리포트 생성
<img alt="프로젝트 설계 이미지" src="https://github.com/user-attachments/assets/1cb156a8-9e4b-4603-a637-5cf2c71c91c9" style="max-width: 100%;" />
5. 전체 프로세스(메일 수신→RoBERTa 분석→설명 생성→GPT 리포트→Slack 알림)까지 완전히 자동화한 엔드투엔드 XAI 기반 보안 파이프라인 구현
 

이처럼 단순 탐지 정확도 최적화에 그치지 않고, AI의 판단 근거를 누구나 쉽게 이해할 수 있도록 XAI 및 자동화에 중점을 둔 것이 이 프로젝트의 가장 큰 강점입니다.

## 🎬 시연 영상 
<p align="center">
  <a href="https://www.youtube.com/watch?v=KzXkFYNGH_I">
    <img src="https://img.youtube.com/vi/KzXkFYNGH_I/0.jpg" alt="ExplainSec 시연 영상" width="600">
  </a>
  <br>
  <strong>Click to watch the Demo Video (시연 영상 보기)</strong>
</p>

## 🗂️ 시스템 아키텍처 

본 시스템은 이메일 수신부터 분석, 설명, 알림까지 전 과정이 자동으로 처리되도록 설계되었습니다.

<img alt="프로젝트 설계 이미지" src="https://github.com/user-attachments/assets/4a3fe7ea-d0bc-431b-80ee-7e70b323074f" style="max-width: 100%;" />

자동화 파이프라인 5단계 :

1. (Email Received): 새 이메일이 수신되면 Webhook으로 시스템이 트리거됩니다 .

2. (Phishing Analysis): Fine-tuned RoBERTa 모델이 즉시 피싱 여부를 분석합니다 (F1-Score 0.98) .

3. (Phishing Explanation): LIG가 RoBERTa 모델의 판단 근거(주요 영향 토큰)를 역추적합니다 .

4. (Report Generation): GPT API가 LIG 데이터를 바탕으로 사람이 읽을 수 있는 HTML 보고서를 생성합니다 .

5. (Real-Time Alert): 최종 분석 결과가 Slack을 통해 사용자에게 실시간으로 전송됩니다 .

## 📁 파일 구성
### 1. 모델 비교 및 성능 최적화 관련 
- [`mlp.ipynb`](mlp.ipynb): TF-IDF 벡터를 기반으로 학습된 **경량화 및 해석 가능한 MLP(다층 퍼셉트론)** 모델을 구현합니다. 이 파일에는 학습, 검증, 평가 과정이 자세히 설명되어 있습니다.
- [`roberta.ipynb`](roberta.ipynb): Hugging Face의 사전학습된 **RoBERTa** 모델을 피싱 이메일 분류에 맞게 미세조정합니다. 이 파일에는 학습, 검증, 평가 절차가 상세하게 안내되어 있습니다.
- [`mlp_best.pt`](mlp_best.pt): **MLP 모델이 저장된 파일**입니다.  
- [`roberta-best.pt`](roberta-best.pt): **미세조정된 RoBERTa 모델이 저장된 파일**입니다.  
https://drive.google.com/file/d/1x9UwTcQZg6zkUTaZoFT8Q1OYpz-0j4Ue/view?usp=sharing
> **참고:** RoBERTa 모델 파일(`roberta-best.pt`)은 약 500MB로, 파일 크기가 커서 google drive 에 별도로 업로드되었습니다.

### 2. 설명 가능한 AI 기반 보안 시스템 구축 관련 
- [`/roberta-model-xai`](/roberta-model-xai): 최종적으로 시스템에 사용한 RoBERTa 모델이 저장된 디렉토리입니다. 
https://drive.google.com/file/d/1YcvT3l2h7b1GQPVjIZCBmbqR3YvhoUUW/view?usp=sharing
> **참고:** 시스템에 사용한 RoBERTa 모델 디렉토리(`/roberta-model-xai`)는 약 500MB로, 파일 크기가 커서 google drive 에 별도로 업로드되었습니다.

- [`/xai_reports`](/xai_reports): **자동 생성된 XAI 분석 리포트가 저장되는 디렉토리입니다.**

- [`roberta-xai.ipynb`](roberta-xai.ipynb): **전체 시스템이 포함된 ipynb 파일 입니다.** RoBERTa 모델을 활용한 피싱 이메일 탐지 전 과정을 단계별로 다루고, 데이터 전처리·모델 학습·성능 평가뿐만 아니라 Layer Integrated Gradients(LIG)를 적용한 토큰 단위 설명가능성 분석, 그리고 GPT 및 Slack 연동까지 자동화된 XAI 파이프라인 전체 구현 과정이 상세하게 안내되어 있습니다.
> **중요:** 본 시요템은 OpenAI API Key, Slack Webhook URL, Gmail API 등 민감한 외부 서비스 연동 정보를 직접 별도 입력해야 하며, 보안 및 개인정보 보호 측면에서 해당 키값을 저장해두거나 공유하지 않습니다.  
> 또한 API 연동 및 환경설정 과정이 다소 복잡할 수 있으니, 시연 영상을 참고하시길 바랍니다.

## 📊 데이터셋

- 출처: [Kaggle - Phishing Emails Dataset](https://www.kaggle.com/datasets/subhajournal/phishingemails)
- 샘플 수: 18,650개 이메일(피싱 및 정상 포함)
- 태스크: 이진 분류 (1 = 정상, 0 = 피싱)  
  **(safe=1, phishing=0)**

## 🧠 모델

### 🔹 MLP 
- TF-IDF + 피드포워드 신경망
- 입력: TF-IDF 벡터
- 아키텍처: [128] → [64] → [1] (시그모이드)
- 기법: 드롭아웃(0.4), L2 정규화, EarlyStopping
- 프레임워크: PyTorch
- 임계값(threshold) 0.45로 리콜 향상

### 🔹 RoBERTa 
- 트랜스포머 파인튜닝
- 입력: `RobertaTokenizer`로 토크나이즈된 원본 텍스트
- 기반 모델: Hugging Face의 `roberta-base`
- 출력: `[CLS]` 토큰을 활용한 이진 분류
- 최적화: `AdamW`, 검증 정확도 기반 EarlyStopping
- 프레임워크: Transformers(Hugging Face) + PyTorch

## ⚙️ 학습 파이프라인

1. **전처리**: 텍스트 정제, 결측/중복 제거, 라벨 인코딩 (정상=1, 피싱=0)
2. **특성 엔지니어링**:
   - MLP → TF-IDF 벡터화
   - RoBERTa → BPE 토크나이즈
3. **데이터 분할**: 학습 60% / 검증 20% / 테스트 20% (층화 추출)
4. **학습**: EarlyStopping이 적용된 커스텀 학습 루프
5. **평가**: 정확도, F1-score, 혼동행렬, 임계값 튜닝

## 📈 성능 요약

<img alt="프로젝트 개요 이미지" src="https://github.com/user-attachments/assets/db38e575-0f51-4824-b89c-94b4e9af3122" style="max-width: 100%;" />


| 모델    | 정밀도 | 재현율 | F1 점수 |
|---------|--------------|-------------|---------|
| MLP     | 0.96         | 0.98        | 0.96    |
| RoBERTa | 0.98         | 0.98        | 0.98    |


## 💡 장단점 및 향후 계획 

- Gmail 스팸 필터가 놓친 실제 피싱 사례(넷플릭스 사칭)를 성공적으로 탐지 및 설명하는 실용성을 입증했으며, End-to-End 자동화 파이프라인을 구축하여 실무 활용도를 높였다.
- 현재 탐지 모델이 주로 이메일 본문(Body)의 텍스트를 기반으로 분석을 수행한다. 향후 더 정교한 탐지를 위해서는 발신자 정보, 헤더(Header) 등 메타데이터를 종합적으로 분석하는 고도화가 필요하다.

## 📄 라이선스

이 프로젝트는 [GNU LGPL v3.0](https://www.gnu.org/licenses/lgpl-3.0.html) 라이선스 하에 배포됩니다.


---

# 📨 Table of Contents

- [🔍 Explainable AI-based Phishing Email Detection](#-explainable-ai-based-phishing-email-detection)
- [🎬 Demo Video](#-demo-video)
- [🗂️ System Architecture](#-system-architecture)
- [📁 File Structure](#-file-structure)
- [📊 Dataset](#-dataset)
- [🧠 Model](#-model)
  - [🔹 MLP](#-mlp)
  - [🔹 RoBERTa](#-roberta)
- [⚙️ Training Pipeline](#️-training-pipeline)
- [📈 Performance Summary](#-performance-summary)
- [💡 Pros, Cons and Future Work](#-pros-cons-and-future-work)
- [📄 License](#-license)

---

## 🔍 Explainable AI-based Phishing Email Detection

> **Team [ExplainSec]**   
> **20202905 Hyunmin Park**   
> **20200397 Seungjun You**   

<img alt="project overview img" src="https://github.com/user-attachments/assets/9d15a902-d17a-4fa5-afd1-98db325a06fd" style="max-width: 100%;" />



Recently, phishing emails are becoming increasingly sophisticated, even generated by AI models such as ChatGPT. To counter these threats, it is necessary not only to have accurate detectors but also **explainable models** so that human analysts can understand why an email was detected.

The goal of this project is not just to accurately detect advanced phishing emails generated by AI, but also to **clearly explain to users why the AI judged a specific email as phishing**. An AI that only provides detection/blocking results is difficult to trust and becomes a 'black-box model'. Thus, our system aims to implement an **explainable and trustworthy AI-based security system** that allows users to understand and trust the AI's decision-making process.

For this project, we utilized a real phishing email dataset (Kaggle), implemented both the traditional MLP (TF-IDF based) model and the latest Transformer (RoBERTa) model, and conducted a rigorous comparison of their performance. After maximizing detection accuracy, we applied a variety of XAI (eXplainable AI) techniques to enhance interpretability and make AI decisions transparent.

**Key Features and Highlights**
1. Built and quantitatively compared (precision/recall/accuracy/etc) two models—MLP and RoBERTa—using a real-world dataset, and optimized their performance.
2. Achieved a 98% F1-Score based on contextual understanding via fine-tuned RoBERTa (maximizing the power of state-of-the-art AI models).
3. Used XAI technique (Layer Integrated Gradients, LIG) to visually highlight important words (e.g., update, current, etc.) that strongly impacted decisions, helping analysts easily check the reasoning.
4. GPT API analyzes LIG results to automatically generate human-readable analysis reports containing “risk summary, rationales, and response guidance”.
<img alt="프로젝트 설계 이미지" src="https://github.com/user-attachments/assets/1cb156a8-9e4b-4603-a637-5cf2c71c91c9" style="max-width: 100%;" />
5. Implemented a fully-automated end-to-end XAI-based security pipeline (email reception → RoBERTa analysis → explanation generation → GPT report → Slack alert).

Unlike projects focused only on perfecting detection accuracy, a strength of this project is its focus on XAI and automation so that the AI's decisions can be easily understood by anyone.

## 🎬 Demo Video
<p align="center">
  <a href="https://www.youtube.com/watch?v=KzXkFYNGH_I">
    <img src="https://img.youtube.com/vi/KzXkFYNGH_I/0.jpg" alt="ExplainSec Demo Video" width="600">
  </a>
  <br>
  <strong>Click to watch the Demo Video</strong>
</p>

## 🗂️ System Architecture

The entire process—from receiving emails to analysis, explanation, and alert—is automated.

<img alt="프로젝트 설계 이미지" src="https://github.com/user-attachments/assets/4a3fe7ea-d0bc-431b-80ee-7e70b323074f" style="max-width: 100%;" />

Five stages of the automated pipeline:

1. (Email Received): When a new email is received, a webhook triggers the system.
2. (Phishing Analysis): The fine-tuned RoBERTa model immediately analyzes whether it is phishing (F1-Score 0.98).
3. (Phishing Explanation): LIG backtracks the key tokens that influenced the RoBERTa model’s decision.
4. (Report Generation): The GPT API creates a human-readable HTML report based on LIG data.
5. (Real-Time Alert): Final analysis result is sent to users in real time via Slack.

## 📁 File Structure
### 1. Model Comparison & Performance Optimization
- [`mlp.ipynb`](mlp.ipynb): Implements a **lightweight and interpretable MLP (Multilayer Perceptron)** model trained on TF-IDF vectors. This file details the training, validation, and evaluation processes.
- [`roberta.ipynb`](roberta.ipynb): Fine-tunes a pre-trained **RoBERTa** model (from Hugging Face) for phishing email classification. This file details training, validation, and evaluation steps.
- [`mlp_best.pt`](mlp_best.pt): **Saved MLP model weights.**
- [`roberta-best.pt`](roberta-best.pt): **Fine-tuned RoBERTa model weights.**  
https://drive.google.com/file/d/1x9UwTcQZg6zkUTaZoFT8Q1OYpz-0j4Ue/view?usp=sharing
> **Note:** The RoBERTa model file (`roberta-best.pt`) is about 500MB and is provided separately via Google Drive due to its large size.

### 2. Explainable AI Security System
- [`/roberta-model-xai`](/roberta-model-xai): Directory containing the final RoBERTa model used in the system.
https://drive.google.com/file/d/1YcvT3l2h7b1GQPVjIZCBmbqR3YvhoUUW/view?usp=sharing
> **Note:** The model directory (`/roberta-model-xai`) is about 500MB and is uploaded separately to Google Drive due to its large size.

- [`/xai_reports`](/xai_reports): **Directory for automatically generated XAI analysis reports.**

- [`roberta-xai.ipynb`](roberta-xai.ipynb): **Notebook containing the entire system.** Covers the stepwise process of phishing email detection using RoBERTa, including data preprocessing, model training and evaluation, token-level explainability via Layer Integrated Gradients (LIG), and full automation with GPT and Slack integration.
> **Important:** This system requires manual entry of sensitive external service information (OpenAI API Key, Slack Webhook URL, Gmail API, etc.), and such credentials are never stored or shared (for security/privacy).  
> The API integration and setup may be complex, so please refer to the demo video.

## 📊 Dataset

- Source: [Kaggle - Phishing Emails Dataset](https://www.kaggle.com/datasets/subhajournal/phishingemails)
- Samples: 18,650 emails (including both phishing and safe)
- Task: Binary classification (1 = safe, 0 = phishing)  
  **(safe=1, phishing=0)**

## 🧠 Model

### 🔹 MLP
- TF-IDF + feedforward neural network
- Input: TF-IDF vectors
- Architecture: [128] → [64] → [1] (sigmoid)
- Techniques: Dropout (0.4), L2 regularization, EarlyStopping
- Framework: PyTorch
- Improved recall with threshold = 0.45

### 🔹 RoBERTa
- Transformer fine-tuning
- Input: Original text tokenized by `RobertaTokenizer`
- Base model: Hugging Face `roberta-base`
- Output: Binary classification using `[CLS]` token
- Optimization: `AdamW`, EarlyStopping based on validation accuracy
- Framework: Transformers (Hugging Face) + PyTorch

## ⚙️ Training Pipeline

1. **Preprocessing**: Text cleaning, remove missing/duplicates, label encoding (safe=1, phishing=0)
2. **Feature Engineering**:
   - MLP → TF-IDF vectorization
   - RoBERTa → BPE tokenization
3. **Data Splitting**: Train 60% / Validation 20% / Test 20% (stratified sampling)
4. **Training**: Custom loop with EarlyStopping
5. **Evaluation**: Accuracy, F1-score, confusion matrix, threshold tuning

## 📈 Performance Summary

<img alt="프로젝트 개요 이미지" src="https://github.com/user-attachments/assets/db38e575-0f51-4824-b89c-94b4e9af3122" style="max-width: 100%;" />


| Model   | Precision | Recall | F1 Score |
|---------|-----------|--------|----------|
| MLP     | 0.96      | 0.98   | 0.96     |
| RoBERTa | 0.98      | 0.98   | 0.98     |

## 💡 Pros, Cons and Future Work

- Successfully demonstrated real-world applicability by detecting/explaining actual phishing cases (e.g., fake Netflix) missed by Gmail spam filters, and built a fully end-to-end automated pipeline for practical use.
- Current detection is mainly based on the email body (text). For more advanced detection in the future, it is necessary to comprehensively analyze metadata such as sender information and headers.

## 📄 License

This project is licensed under [GNU LGPL v3.0](https://www.gnu.org/licenses/lgpl-3.0.html).
