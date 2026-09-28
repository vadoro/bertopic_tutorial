# BERTopic 토픽 지도

BERTopic 토픽 모델링의 원리를 아주 쉽게 설명하는 **인터랙티브 모션그래픽 영상**입니다.
짧은 한국어 문장 90개가 "숫자 → 지도 → 섬 → 이름표"로 바뀌는 과정을 3분 46초 동안 따라갑니다.

- `index.html`: 인터랙티브 버전. 브라우저로 열면 바로 재생돼요. 설치할 것은 없습니다.
- `video/bertopic-explainer.mp4`: 같은 애니메이션을 녹화한 1080p 영상(자막 포함). 수업이나 발표에 그대로 쓸 수 있어요.

## 장면 구성

| 시간 | 장면 | 내용 |
| --- | --- | --- |
| 0:00 | 들어가며 | 글 더미 앞에서 던지는 질문 |
| 0:10 | 문서 더미 | 짧은 글 90개와 네 단계 미리 보기 |
| 0:21 | ① 임베딩 | 문장이 숫자 384개로 바뀌고, 뜻이 비슷하면 숫자도 비슷해진다 |
| 0:55 | ② 차원 축소 · UMAP | 말린 종이를 펴듯 이웃 관계를 지키며 2차원(실제 기본값 5차원)으로 |
| 1:24 | ③ 군집화 · HDBSCAN | 밀도를 땅 높이로, 수면이 내려가며 드러나는 섬 = 토픽, 이상치(-1) |
| 2:04 | ④ c-TF-IDF | 섬의 문서를 합쳐 단어를 세고(TF), 흔한 단어는 깎아(IDF) 이름표 만들기 |
| 2:50 | 완성된 토픽 지도 | `get_topic_info()` 표, 새 문서의 토픽 예측 |
| 3:08 | 레고 블록 | 임베딩·UMAP·HDBSCAN·토크나이저·표현 모델 바꿔 끼우기 |
| 3:28 | 정리 | 네 단계 요약과 파이썬 코드 |

## 조작법

- 재생·일시정지: 스페이스(또는 화면 클릭), 5초 이동: ← →, 자막: C, 전체 화면: F
- 재생 막대의 눈금과 왼쪽 장면 목록으로 원하는 장면에 바로 갈 수 있어요.
- 화면 속 종이나 점에 마우스를 올리면(휴대폰은 탭) 원문과 토픽이 보여요.
- 영상 아래 **직접 해 보기** 패널은 지금 장면에 맞춰 바뀌어요.
  - 임베딩: 두 문장을 골라 유사도 비교
  - UMAP: 말린 종이를 직접 펴 보기, 이웃 연결선 보기
  - HDBSCAN: `min_cluster_size`와 수면 높이를 바꾸면 군집을 바로 다시 계산
  - c-TF-IDF: 토픽별 단어 점수표, IDF 켜고 끄기
  - 결과: 새 문장을 넣어 어느 토픽 섬에 떨어지는지 보기
  - 레고 블록·정리: 복사해서 쓸 수 있는 BERTopic 코드

## 무엇이 진짜 계산이고 무엇이 비유인가요?

- **실제로 계산**: HDBSCAN 군집화(상호 도달 거리 → 최소 신장 트리 → 응축 트리 → 안정성 기반 선택)와 c-TF-IDF 점수(`tf × log(1 + A / f)`)는 페이지 안에서 BERTopic과 같은 방식으로 계산합니다. 그래서 `min_cluster_size`를 바꾸면 토픽 개수, 이상치, 토픽 이름이 실제로 달라져요.
- **설명용 예시**: 문서 90개, 16칸짜리 임베딩 값, 2차원 좌표, 스위스 롤 모양의 UMAP 애니메이션은 원리를 보여 주기 위해 만든 것입니다. 새 문장 예측도 토픽 대표 단어를 이용한 간이 시뮬레이션이에요. 실제 BERTopic은 새 문장을 임베딩한 뒤 가장 가까운 군집을 찾습니다.

## 영상 다시 만들기

`index.html`을 고친 뒤 MP4를 새로 뽑으려면 Playwright(Chromium)와 ffmpeg가 필요합니다.

```bash
npm install playwright            # 이미 있다면 생략
node tools/render-video.mjs video/bertopic-explainer.mp4 30
# ffmpeg가 PATH에 없다면: FFMPEG=/path/to/ffmpeg node tools/render-video.mjs
# 일부 장면만 이미지로 확인: FRAMES="6.2,100,150" node tools/render-video.mjs
```

스크립트는 `index.html#export`를 열어 시간마다 캔버스를 그린 뒤 프레임을 ffmpeg로 넘깁니다. 애니메이션이 시간의 함수로만 그려지기 때문에 녹화 결과가 매번 똑같아요.

## 참고

- BERTopic 공식 문서: https://maartengr.github.io/BERTopic/
- Grootendorst, M. (2022). *BERTopic: Neural topic modeling with a class-based TF-IDF procedure*. https://arxiv.org/abs/2203.05794
