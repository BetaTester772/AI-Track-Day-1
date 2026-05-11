# 트러블슈팅 가이드

세션 중 학생들이 자주 막히는 부분과 해결법 모음입니다.

---

## ❌ CUDA Out of Memory

**증상**: `RuntimeError: CUDA out of memory. Tried to allocate ...`

**원인**: 여러 큰 모델을 메모리에 동시에 올림. T4 의 16GB 를 넘김.

**해결**:
```python
import gc
import torch

del model        # 또는 del classifier / del generator / del summarizer
gc.collect()
torch.cuda.empty_cache()
```

그래도 안 되면 메뉴에서 **Runtime → Restart runtime** 후 처음부터 다시 실행.

---

## ❌ 모델 다운로드 실패 / `HTTPSConnectionPool` 에러

**증상**: `requests.exceptions.ConnectionError` 또는 `HfHubHTTPError`

**원인**: 네트워크 또는 HF Hub 일시 장애.

**해결**:
1. 같은 셀을 한 번 더 실행 (`Shift+Enter`)
2. 그래도 안 되면 5분 후 재시도
3. 그래도 안 되면 [HF Status 페이지](https://status.huggingface.co/) 확인

---

## ❌ `pipeline()` 호출 시 경고 메시지가 너무 많이 떠요

**원인**: HuggingFace transformers 의 일반적인 경고 (config 추론 등).

**해결**: 노트북 상단에서 이미 `warnings.filterwarnings("ignore")` 처리되어 있어요. 그래도 보이면 단순 정보성 메시지이니 무시해도 됩니다.

만약 정말 안 보이게 하고 싶다면:
```python
from transformers.utils import logging
logging.set_verbosity_error()
```

---

## ❌ SmolLM2 결과가 이상해요 (반복, 의미 없음)

**증상**:
```
The robot said hello.
The robot said hello.
The robot said hello again.
```

**원인**: SmolLM2-360M 은 매우 작은 모델 (3.6억 파라미터). 한계가 있어요.

**해결**:
- 프롬프트를 더 구체적으로: `"Write a 3-sentence story about a robot who learns to paint"`
- `temperature` 를 0.5–0.8 사이로 (너무 낮으면 반복, 너무 높으면 영뚱)
- `do_sample=True` 인지 다시 확인
- 더 좋은 결과를 원하면 더 큰 모델 (예: `Qwen/Qwen2.5-1.5B-Instruct`, `meta-llama/Llama-3.2-3B-Instruct`) 사용

---

## ❌ `temperature` 가 안 먹는 것 같아요 — 항상 같은 결과만 나와요

**원인**: `do_sample=False` 일 때 `temperature` 는 **silently ignored** 됩니다 (greedy decoding).

**해결**: `do_sample=True` 추가
```python
out = generator(messages, temperature=0.7, do_sample=True, max_new_tokens=120)
```

---

## ❌ Section 5 에서 `KeyError: Unknown task summarization`

**증상**:
```
KeyError: "Unknown task summarization, available tasks are [..., 'sentiment-analysis', 'text-generation', ...]"
```
에러 메시지의 available tasks 리스트에 `summarization` 이 없음.

**원인**: Colab 에 설치된 `transformers` 가 **5.x 계열** (`any-to-any`, `keypoint-matching` 같은 최신 task가 보이면 5.x 신호). 5.x 에서는 `summarization` task alias 가 pipeline registry 에서 제외됨.

**해결**: 노트북 상단 install 셀의 핀을 `transformers>=4.45,<5` 로 좁히고 **Runtime → Restart runtime** 후 처음부터 다시 실행.
이미 본 저장소의 노트북은 이 핀이 적용되어 있어요.

**즉시 우회 (런타임 재시작 없이)**: Section 5 의 셀을 다음처럼 모델 ID 를 명시:
```python
summarizer = pipeline(model="sshleifer/distilbart-cnn-12-6", device=device)
```
`task` 인자 없이 모델만 주면 모델 config 에서 task 가 자동 감지되어 5.x 에서도 동작합니다.

---

## ❌ Section 2 sentiment-analysis 가 한국어를 잘 못 알아봐요

**원인**: Section 2 의 모델 (`distilbert-base-uncased-finetuned-sst-2-english`) 은 영어 SST-2 로만 학습됨.

**해결**: Section 6 (보너스) 의 `tabularisai/multilingual-sentiment-analysis` 모델 사용. 한국어 포함 다국어 지원.

---

## ❌ Colab 에서 GPU 가 안 잡혀요

**증상**: `torch.cuda.is_available()` 가 `False`.

**해결**:
1. 메뉴: **Runtime → Change runtime type → Hardware accelerator: T4 GPU → Save**
2. 가끔 Colab 무료 GPU 가 만석일 수 있음 → 잠시 후 재시도
3. CPU 로도 돌아가긴 하지만 BART/SmolLM2 가 매우 느림 (분 단위)

> 노트북 코드는 `device = 0 if torch.cuda.is_available() else -1` 로 자동 fallback 되어 있어서 GPU 없어도 작동합니다.

---

## ❌ 한국어 출력이 `□□□` 로 깨져요

**원인**: matplotlib 한국어 폰트 미설치.

**해결**: 본 노트북은 matplotlib 을 사용하지 않으므로 해당 없음.
다른 노트북에서 발생한다면:
```python
!apt-get install -y fonts-nanum
import matplotlib.font_manager as fm
plt.rcParams['font.family'] = 'NanumGothic'
```
