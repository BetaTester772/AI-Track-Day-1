# 사전 점검 체크리스트

발표 전 노트북이 Colab 무료 T4 환경에서 안정적으로 돌아가는지 확인하기 위한 체크리스트입니다.

## 발표 2일 전

- [ ] 모든 노트북을 Colab 무료 T4 GPU 런타임에서 처음부터 끝까지 실행
- [ ] 각 모델 첫 다운로드 시간을 측정하고 아래 표에 갱신 (BART-large 가 가장 오래 걸림)
- [ ] T4 GPU 메모리 사용량 피크 확인 (`!nvidia-smi` 를 섹션 사이사이에 직접 실행해보기)
- [ ] `transformers>=4.45` 가 정상 설치되는지 확인 (4.44 이하에선 SmolLM2 chat template 깨짐)
- [ ] IMDb / AG News 데이터셋이 정상 로드되는지 확인 (간혹 HF Hub 일시 장애)

## 발표 1일 전

- [ ] GitHub repo 최신 commit 확인 (notebooks/ 3개 파일 모두 최신)
- [ ] README 의 Colab 배지 3개를 모두 클릭하여 정상 오픈되는지 확인
- [ ] 발표 슬라이드의 섹션 번호와 노트북 마크다운 셀의 "발표 슬라이드 X–Y 참조" 표기가 일치하는지 확인

## 발표 당일 (1시간 전)

- [ ] 강사용 노트북(`03_instructor_solutions.ipynb`)을 한 번 실행해서 캐시 워밍 (다운로드 미리 해두기)
- [ ] 화면 공유 시 폰트 크기 확인 (Colab 우상단 톱니바퀴 → Site → Font size 조정)
- [ ] 백업: 모든 노트북을 로컬에 `.ipynb` 로 다운로드 (네트워크 장애 대비)
- [ ] 마이크·화면 공유 사전 테스트

## 모델별 예상 다운로드 시간 (T4, 첫 실행 기준)

| 모델 | 크기 | 예상 시간 |
|---|---|---|
| `all-MiniLM-L6-v2` | ~80MB | ~10초 |
| `distilbert-base-uncased-finetuned-sst-2-english` | ~250MB | ~15초 |
| `facebook/bart-large-mnli` | ~1.6GB | **~45초** ← 가장 오래 걸림 |
| `HuggingFaceTB/SmolLM2-360M-Instruct` | ~720MB | ~25초 |
| `sshleifer/distilbart-cnn-12-6` | ~1.2GB | ~40초 |
| `tabularisai/multilingual-sentiment-analysis` | ~250MB | ~15초 |

> **두 번째 실행부터는** Colab 의 디스크 캐시 덕분에 1~3초 이내로 빠르게 로드됩니다.

## 메모리 관리 체크

| 시점 | 추정 GPU 사용량 |
|---|---|
| Section 0 직후 | ~0.3GB |
| Section 1 (MiniLM) | ~0.5GB |
| Section 2 (DistilBERT) | ~0.8GB |
| Section 3 (BART-large-MNLI) | ~3.5GB |
| Section 3 cleanup 후 → Section 4 (SmolLM2 fp16) | ~1.2GB |
| Section 4 cleanup 후 → Section 5 (distilbart) | ~2.5GB |
| Section 5 cleanup 후 → Section 6 (다국어 모델) | ~0.8GB |

T4 의 16GB 한도에 한참 못 미치지만, **`del + gc.collect() + empty_cache()` 패턴을 빼먹으면 누적 사용량이 5–7GB 까지 올라갈 수 있습니다.**
