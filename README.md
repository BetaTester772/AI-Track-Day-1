# GDG on Campus SKKU — AI Track Day 1
## 🤗 Hugging Face Hands-on: 텍스트 임베딩·분류 & 텍스트 생성

> 🎓 **GDG on Campus SKKU 2026 — AI Track Week 1** 발표 자료 + 실습 노트북
> 발표자: 안호성 ([@BetaTester772](https://github.com/BetaTester772))

이 저장소는 90분 세션에서 ML 입문자가 Hugging Face 생태계를 처음 만져보는 실습 노트북 모음입니다.
임베딩으로 의미를 좌표로 바꾸는 것부터, Zero-shot 분류, LLM 디코딩 파라미터 체험, 요약, 다국어 모델 교체까지 6개의 실습을 다룹니다.

모든 코드는 **Google Colab 무료 T4 GPU** 에서 OOM 없이 돌도록 검증되어 있고, GPU 가 없는 환경에서도 자동으로 CPU fallback 됩니다.

---

## 🚀 빠른 시작

세션 운영자가 학생들에게 자료를 배포하는 방법은 두 가지입니다:

### 방법 A. GitHub 링크 공유 (권장)

학생들에게 이 저장소 링크를 알려주면, 아래 Colab 배지로 바로 열 수 있어요.

| 노트북 | 용도 | Colab |
|---|---|---|
| **학생용 빈칸** | 세션 당일 직접 채우며 따라오기 | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/GDG-SKKU/AI-Track-Day-1/blob/main/notebooks/01_student_blank.ipynb) |
| **학생용 완성본** | 막혔을 때 참조용 | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/GDG-SKKU/AI-Track-Day-1/blob/main/notebooks/02_student_complete.ipynb) |
| **강사용 솔루션** | 발표 노트·흔한 실수·토론 질문 포함 | [![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/GDG-SKKU/AI-Track-Day-1/blob/main/notebooks/03_instructor_solutions.ipynb) |

### 방법 B. `.ipynb` 파일을 직접 공유 (카카오톡·메일 등)

학생들이 `01_student_blank.ipynb` 와 `02_student_complete.ipynb` 파일을 직접 받으면:

1. [colab.research.google.com](https://colab.research.google.com) 접속 → 우상단 **New notebook** 옆 **Upload** 선택 후 `.ipynb` 파일 업로드
2. **또는** 구글 드라이브에 업로드 → 파일 우클릭 → **연결 앱 → Google Colaboratory**

노트북은 모든 의존성을 자체 설치(`!pip install`)하고 데이터는 HF Hub 에서 직접 받아오므로, GitHub 저장소 없이도 완전히 독립적으로 실행됩니다.

> 💡 두 방법 모두 Colab 진입 후 **Runtime → Change runtime type → T4 GPU** 로 바꿔주세요.

---

## 📅 세션 구성 (90분)

| 시간 | 슬라이드 | 내용 |
|---|---|---|
| 0–5분 | 1–3 | 환영 + Hugging Face 소개 + Setup |
| 5–15분 | 4–11 | **Part 1**: 임베딩 개념 + 실습 ① 문장 유사도 |
| 15–25분 | 12–13 | Pipeline API + 실습 ② 감정 분석 (IMDb) |
| 25–35분 | 14–17 | 실습 ③ Zero-shot 분류 (NLI 메커니즘) |
| 35–40분 | 18 | ☕ 5분 휴식 |
| 40–60분 | 19–24 | **Part 2**: LM/토큰화/디코딩 + 실습 ④ temperature 비교 |
| 60–75분 | 25 | 실습 ⑤ 텍스트 요약 (Seq2Seq) |
| 75–85분 | 26 | 🎁 보너스 — 모델만 바꿔 한국어 지원 |
| 85–90분 | 27–29 | 마무리 + Q&A |

---

## 🤖 사용 모델

| # | 모델 | 크기 | task |
|---|---|---|---|
| ① | [sentence-transformers/all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2) | ~80MB | sentence-similarity |
| ② | [distilbert-base-uncased-finetuned-sst-2-english](https://huggingface.co/distilbert-base-uncased-finetuned-sst-2-english) (자동 선택) | ~250MB | text-classification |
| ③ | [facebook/bart-large-mnli](https://huggingface.co/facebook/bart-large-mnli) | ~1.6GB | zero-shot-classification |
| ④ | [HuggingFaceTB/SmolLM2-360M-Instruct](https://huggingface.co/HuggingFaceTB/SmolLM2-360M-Instruct) | ~720MB | text-generation |
| ⑤ | [sshleifer/distilbart-cnn-12-6](https://huggingface.co/sshleifer/distilbart-cnn-12-6) (자동 선택) | ~1.2GB | summarization |
| 🎁 | [tabularisai/multilingual-sentiment-analysis](https://huggingface.co/tabularisai/multilingual-sentiment-analysis) | ~250MB | text-classification (다국어) |

---

## 📚 사용 데이터셋

- [stanfordnlp/imdb](https://huggingface.co/datasets/stanfordnlp/imdb) — 영화 리뷰 감정 분류 (실습 ②)
- [fancyzhx/ag_news](https://huggingface.co/datasets/fancyzhx/ag_news) — 뉴스 카테고리 분류 (실습 ③)

---

## 📁 저장소 구조

```
AI-Track-Day-1/
├── README.md                  ← 지금 보고 계신 파일
├── LICENSE                    ← MIT License
├── notebooks/
│   ├── 01_student_blank.ipynb        ← 세션 당일 사용
│   ├── 02_student_complete.ipynb     ← 막혔을 때 참조
│   └── 03_instructor_solutions.ipynb ← 강사용
└── docs/
    ├── PRE_CHECK.md           ← 발표 전 사전 점검
    └── TROUBLESHOOTING.md     ← 흔한 에러 + 해결법
```

---

## 📖 다음 단계

세션 이후 더 깊이 들어가고 싶다면:

- 🌐 [Hugging Face Hub](https://huggingface.co/) — task 별로 모델 검색
- 📚 [HF Course (무료)](https://huggingface.co/learn) — Transformers, Fine-tuning, RAG 까지 체계적으로
- 🚀 [HF Spaces](https://huggingface.co/spaces) — Gradio 로 모델 데모 5분만에 배포

---

## 📝 License

[MIT License](./LICENSE) — 자유롭게 가져다 쓰세요.
