🦠 COVID Vaccine Controversy Analysis by BERT/DeBERTa

온라인 댓글 기반 코로나 백신 여론 분석 프로젝트






1. 개요 및 목표 (Overview & Thesis)
1.1 연구 배경

COVID-19 팬데믹 동안 사람들은 뉴스 기사보다
온라인 커뮤니티 댓글, 포럼, SNS에서 훨씬 직접적인 감정과 의견을 드러냈다.

기존 연구(예: COVID 공포지수 vs. 주가)는

공포지수와 금융시장 사이의 상관성은 잘 보여주지만,

다음 질문에는 충분히 답하지 못한다:

사람들은 구체적으로 무엇 때문에 불안해했는가?

논란의 핵심은 부작용 같은 의학적 이슈였는가,
아니면 정부 정책·의무화·정치적 갈등이었는가?

본 프로젝트는 이 질문에 답하기 위해
대규모 온라인 댓글 데이터를 수집·정제하고,
감성 분석 + 토픽 모델링 + 시계열 분석을 통해
**“백신 논란의 실제 축이 무엇이었는지”**를 추적하는 것을 목표로 한다.

1.2 연구 목적

**대규모 온라인 리뷰/댓글(초기 약 11만 건)**을 크롤링/수집한 뒤,
고도로 정제하여 고순도(high-purity) 코로나/백신 텍스트 데이터셋을 구축한다.

KoELECTRA, BERT, DeBERTa 등 다양한 딥러닝 기반 감성 분석 모델을 실험하여
긍/부정/중립 감성 비율과 **시간에 따른 변화(시계열)**를 추정한다.

댓글 기반 부정 감성 비율 시계열과
**공포·탐욕 지수(Fear-Greed Index)**를 비교하여

전처리/모델링이 제대로 되었는지 검증하고,

온라인 여론이 실제 공포 지수와 어느 정도 동행하는지 판단한다.

LDA, BERTopic을 활용해 토픽 단위로 논쟁의 축을 분석하여

부정 여론이 부작용/의학적 위험 중심인지,

마스크·백신 의무화, 비용, 의료 시스템, 정치 갈등 중심인지 규명한다.

1.3 연구 질문

Q1. 코로나 백신 관련 온라인 커뮤니티에서
감성(긍·부정·중립) 전체 분포는 어떠한가?

Q2. 시간이 지남에 따라 부정 감성 비율은 어떻게 변하는가?

Q3. 부정 감성 비율 시계열은
**공포·탐욕 지수(Fear-Greed Index)**와 얼마나 유사한 패턴을 보이는가?

Q4. 토픽 모델링 결과,

부정/긍정 감성 각각에서 어떤 주제가 여론을 주도했는가?

부정 여론의 중심은 부작용인가,
아니면 정책·의무화·경제·정치 갈등인가?

1.4 핵심 목표 & 성공 지표

핵심 목표
약 10만 건 이상의 댓글을 기반으로

“코로나 백신 논란의 중심이 과학적 부작용보다 정책·의무화·정치/경제 갈등이었다”
는 가설을 데이터로 검증하는 것.

성공 지표 1 – 모델 신뢰도
수동 라벨링 데이터(약 2,000+건)를 기반으로
Validation Accuracy ≥ 0.8 수준의 감성 분류 모델 확보

성공 지표 2 – 전처리 검증
정제된 데이터에서 얻은 부정 비율 시계열과
기존 연구에서 사용한 공포·탐욕 지수 시계열 비교 시

피어슨 상관계수 및

DTW(Dynamic Time Warping) 상에서
의미 있는 수준의 유사 패턴이 나오는지로
전처리 성공 여부를 검증

2. 데이터 수집 및 크롤링
2.1 크롤링·API 시도 및 난관

여러 소스에 대해 크롤링/스크래핑·API를 시도했고,
그 과정에서 CORS, 403, 구조 문제 등 다양한 실패를 겪었다.

소스	언어	수집 내용	결과 / 문제점
Naver 지식iN	🇰🇷	Q&A (타이레놀/피임약 등)	약 32,000건 확보. 다만 백신 직접 관련 Q&A는 부족, 질문 본문 접근 제약
Naver 뉴스 댓글	🇰🇷	뉴스 댓글	Selenium CSS 선택자 변경/보안 이슈로 수집 실패
DC Inside	🇰🇷	포럼 게시글/댓글	약 23,000건 확보. 비속어·백신 무관 잡담 다수
Reddit API (PRAW)	🇺🇸	백신/코로나 관련 게시글·댓글	✅ 약 72,827건 확보 (핵심 데이터)
Pushshift API	🇺🇸	과거 Reddit 데이터	403 Forbidden 등으로 약 2,100건만 확보
Drugs.com	🇺🇸	약물/백신 리뷰	403 (IP 차단)으로 제한적 확보 (~1,500건)
WebMD	🇺🇸	포럼/리뷰	403 (IP 차단), 약 16,800건 확보
HealthBoards	🇺🇸	포럼 댓글	404 등 구조 이슈, 약 3,100건 확보
Patient.info	🇺🇸	포럼/리뷰	Requests 차단, 약 480건 확보
2.2 최종 원천 데이터

Reddit + WebMD + Pushshift + HealthBoards + Patient.info + Drugs.com 통합

한글/한국어 데이터는 이후 분석 대상에서 제거 (언어 혼재 문제)

최종 영어 데이터: 약 108,000건

이후 모든 전처리/모델링은 이 영어 데이터셋을 기준으로 진행.

3. 데이터 전처리 파이프라인
3.1 초기 시도 & 문제점

초기에는 최소한의 전처리만 적용한 뒤 LDA를 돌려 보았으나:

URL, 이모티콘, 단순 링크, 정치인 이름 등
“가비지 토픽”이 다수 등장

코로나/백신과 직접 관련이 없거나
잡담/농담/욕설 위주의 토픽들이 상위에 위치

또한, 전처리 전 데이터로 만든
부정 비율 시계열 vs 공포·탐욕 지수 시계열 비교 시:

피어슨 상관계수 ≈ -0.006

DTW 거리 ≈ 1280

→ 두 시계열이 사실상 상관이 없고 패턴도 전혀 다름
→ **“전처리와 데이터 정의에 큰 문제가 있다”**고 판단

이를 계기로 전처리 파이프라인을 처음부터 재설계하였다.

3.2 전처리 개요

최종적으로 사용한 전처리 파이프라인은 크게 네 단계:

구조적 노이즈 제거 (Structural Noise)

언어적 노이즈 제거 (Language Filtering)

형식적 노이즈 제거 (Formal Noise)

주제 관련성 필터링 (Relevance Filtering)

이후 수작업 검증 → 추가 정제

각 단계는

“토픽 모델링에서 의미 없는 토픽이 사라지는가”와

“공포지수-부정비율 시계열 패턴이 개선되는가”

를 기준으로 검증했다.

3.3 단계 1: 구조적 노이즈 제거

목표: API placeholder, 삭제된 댓글, 극단적으로 짧은 잡담 제거

가비지 유형	문제 원인	처리 기준	적용 기법
[deleted], [No Content]	Reddit API에서 삭제된 게시물	해당 문자열 포함 행 제거	pandas 필터링
너무 짧은 잡담 (lol, ok, …)	의미 없는 대화, 모델 학습에 방해	20자 미만 또는 5단어 미만 텍스트 제거	length 기반 필터링

→ 의미 없는 구조적 잡음을 제거하여
학습을 방해하는 극단적인 아웃라이어를 줄이는 것이 목적.

3.4 단계 2: 언어적 노이즈 제거 (비영어 제거)

Naver 등에서 들어온 한글 데이터, 기타 비영어 텍스트를 제거.

가비지 유형	처리 기준	적용 기법
한글/타 언어 포함 텍스트	전체 문자 중 비영어(한글 등) 비율이 10% 초과 시 제거	정규표현식 + 비영어 문자 비율 체크

→ 영어 기반 모델(KoELECTRA 실험은 있었으나, 실제 데이터는 영어 위주)에서
언어 혼재로 인한 토큰 분포 왜곡을 방지.

3.5 단계 3: 형식적 노이즈 제거 (특수문자, URL, 불용어)
가비지 유형	처리 기준	적용 기법
특수문자/URL, 이모티콘	알파벳 외 문자 비율이 40% 초과 시 제거	정규표현식 기반 비율 체크
불용어(Stopwords)	the, a, is 같은 전형적인 불용어 + covid, vaccine처럼 너무 빈번한 단어 일부도 제거	커스텀 stopword 리스트

여기서 covid, vaccine까지 일부 상황에서 stopword로 취급한 이유는:

이미 필터링을 통해 코로나/백신 관련 데이터만 남긴 후

토픽 모델링 시, 너무 당연한 단어 대신
정책·경제·정치·의료 시스템 등 추가적인 축을 더 잘 드러내기 위함

3.6 단계 4: 주제 관련성 필터링 (Relevance Filtering)

단순한 정제 이후에도
“like, think, time” 같은 비주제적 일상 단어가 많이 남아 있었고,
코로나/백신과 무관한 일반 잡담도 여전히 많았다.

그래서 “코로나/백신 관련 핵심 키워드”가 하나라도 포함된 문장만
“주제 관련(related)”으로 보고 나머지는 제거하는 필터를 추가했다.

3.6.1 키워드 리스트
KEYWORDS = [
    'vaccine', 'covid', 'coronavirus', 'side effect', 'adverse', 'pfizer', 'moderna',
    'booster', 'jab', 'shot', 'vax', 'myocarditis', 'astrazeneca', 'janssen',
    'symptoms', 'mandate', 'mask', 'masked', 'unvaccinated', 'vaxxed', 'unvaxxed',
    'hospital', 'death', 'long covid', 'long-covid', 'spike protein', 'mrna'
]


앞선 실패한 전처리/토픽 모델링에서 실제로
코로나/백신 관련 댓글들이 자주 포함하던 단어들을 경험적으로 선정했다.

문장을 소문자로 변환한 뒤,
위 키워드 중 하나라도 포함되면 is_related_topic = True,
하나도 포함되지 않으면 False로 라벨링.

3.6.2 관련성 분포
결과 구분	건수	비율
✅ True (관련 있음)	23,939	23.6%
❌ False (관련 없음)	75,338	76.4%

→ False는 모두 제거하고, True만 남긴 후 다음 단계 진행.
→ 이후 무작위 10% 샘플(약 2,200건)을 직접 읽어보며
정말 코로나/백신 관련인지 수작업 검증했다.

검증 결과:

대부분 정말로 코로나/백신 관련 텍스트였으나,

일부는 논문/기사 링크만 공유하는 등
의견이나 감정이 거의 없는 경우가 다수 존재.

그래서:

링크/기사 공유 위주 문장을 추가 필터링하여
약 1,010개 행 삭제 → FINAL_DATA_ROWS_DELETED.csv 저장

이 중 다시 읽어보니 쓸 만한 데이터 423개는 복구 →
FINAL_DATA_ROWS_DELETED_2.csv (약 23,352건) 생성

이후 실험 과정에서 다시 한번 검토 후,
“실제 분석용 메인 데이터셋”으로 사용할 버전을 최종 확정하였다.
전체 파이프라인 상에서는 이를 통칭해

정제 최종본: FINAL_DATA_ROWS_DELETED*.csv 계열 데이터

로 사용.

✔ 결론: 데이터의 양보다 질을 선택
→ 코로나/백신 여론 분석에 실제로 의미 있는 텍스트만 남기는 작업에 초점을 맞춤

3.7 전처리 검증: 단어 빈도 분석 & 시각화

전처리된 데이터에서:

소문자 변환

URL/특수문자 제거

불용어 제거

표제어(lemmatization) 처리

후, 단어 빈도 상위 50개를 분석한 결과
상위 키워드는 다음과 같이 나왔다:

people

covid

vaccine

get

dont

mask
…

→ 코로나/백신, 사람, 마스크 등 핵심 주제가 제대로 드러남

4. 수동 라벨링(Human Annotation) & 검증 데이터 구성
4.1 1차 라벨링 (Binary & 3-Class 동시)

정제된 데이터(FINAL_DATA_...) 중 **약 10% (2,200~2,300개)**를
무작위로 샘플링하여 수동 라벨링 진행.

두 가지 라벨을 동시에 부여:

Binary (0=부정, 1=긍정)

Three-Class (0=부정, 1=중립, 2=긍정)

4.1.1 이진 분류 라벨 분포
라벨	비율
부정	약 81.2%
긍정	약 18.8%
4.1.2 삼분류 라벨 분포
라벨	비율
부정	약 63.5%
중립	약 17.6%
긍정	약 18.9%




→ 데이터 자체가 부정에 크게 치우친 구조임을 확인.
→ 이후 모델 학습에서 클래스 불균형이 심각한 이슈로 작동.

4.2 2차/3차 라벨링 & 기준 재정의

모델 실험 과정에서:

Validation Accuracy가 일정 수준 이상에서 정체되는 현상,

Three-Class 모델의 Neutral F1-score가 거의 0에 가까운 문제

등이 반복적으로 나타났다.

이를 해결하기 위해:

라벨링 기준을 더 명확히 정의하고,

일부 애매한 문장은 재검토 후 재라벨링,

감정이 거의 드러나지 않는 문장은 라벨링 대상에서 제외

한 뒤,
다시 10% 샘플에 대해 정교한 수동 라벨링을 수행하였다.

이 재라벨링 데이터로 KoELECTRA/DeBERTa를 다시 학습했을 때:

KoELECTRA 기반 Binary 모델:

Validation Accuracy ≈ 0.84

이후 DeBERTa 기반 Binary 모델:

Validation Accuracy 0.86~0.87 수준으로 수렴

→ 수동 라벨링 품질이 모델 성능과 직접 연결됨을 확인.

5. 감성 분석 모델링 (Sentiment Classification)

이 프로젝트에서는 다양한 모델/전략을 실험했다.

KoELECTRA (한국어용) – 실험적으로 사용

BERT 계열 (영어) – baseline

DeBERTa / DeBERTa v3 (영어) – 최종 선택

또한, 심각한 클래스 불균형 문제로 인해
다음 기법들을 조합해 실험했다.

클래스 가중치(Class Weights)

오버샘플링(Oversampling)

SMOTE (개념적 검토, 텍스트에는 제한적)

Focal Loss

Epoch 수 조절

Learning Rate / Scheduler 조정

Gradient Clipping

Early Stopping

아래는 요약된 실험 히스토리이다.

5.1 KoELECTRA 실험 (초기)

학습 데이터: KOEL_labeled_binary.csv, KOEL_labeled_three.csv

초기 Binary 모델:

Validation Accuracy ≈ 0.81~0.82

초기 Three-Class 모델:

Validation Accuracy ≈ 0.63

하지만 중립 클래스 예측 거의 실패 (중립 비율 ~0%)

문제점

극심한 클래스 불균형(부정 80% 이상)

Epoch 2 정도에서 Prediction Collapse:

모든 샘플을 ‘부정(0)’으로 예측하는 상태

클래스 가중치, SMOTE, Focal Loss 등을 적용해도
Three-Class 모델 Macro-F1은 0.2~0.3 수준에 머물렀고
특히 Neutral 클래스 Recall이 거의 0에 가까웠다.

→ 3-Class 분류에 대한 KoELECTRA 기반 접근의 구조적 한계를 확인하고

단순·안정적인 Binary 분류 + 더 적합한 영어 모델(DeBERTa)로 전환하기로 결정.

5.2 불균형 해결 전략 요약

데이터 레벨:

오버샘플링 (Oversampling)

소수 클래스(긍정/중립) 샘플을 복제 또는 증강

다만 텍스트 데이터 특성상 과적합 위험이 있어
“완전 균형”까지 맞추진 않고 부분적 보정에 활용

모델 레벨:

클래스 가중치(Class Weighting)

CrossEntropyLoss(weight=class_weights) 형태로 적용.

예시 (Three-Class):

클래스	개수	가중치 예
0 (부정)	1165	0.52
1 (중립)	325	1.88
2 (긍정)	345	1.77

다수 클래스의 가중치를 낮추고
소수 클래스의 가중치를 높여 손실에서의 비중을 올림.

Focal Loss

쉬운 샘플보다는 어려운(오분류된) 샘플에 더 높은 가중치 부여

극단적인 불균형에서 소수 클래스에 초점을 맞추기 위해 도입

일부 조합에서 성능 향상을 보였지만,
KoELECTRA Three-Class에서는 오히려 불안정성이 크기도 했음.

5.3 모델 교체: BERT → DeBERTa / DeBERTa v3

KoELECTRA 기반 실험에서:

Binary Accuracy는 나쁘지 않았지만,

Three-Class 성능, 특히 중립 클래스 구분이 구조적으로 한계

→ 영어 데이터셋에 더 적합한 DeBERTa 계열로 전환.

5.3.1 DeBERTa 첫 실험

기본 CrossEntropyLoss 기반:

Validation Accuracy ≈ 0.80

class_weight + oversampling 적용:

Validation Accuracy ≈ 0.84

→ KoELECTRA보다 안정적으로 상위 성능을 달성.

5.3.2 DeBERTa + Focal Loss + Epoch 증가

Focal Loss + class_weight + oversampling + Epoch 5~7:

Train Accuracy: 95~99%

Validation Accuracy: 최대 ≈ 0.86~0.87

여러 실험의 결과,

DeBERTa v3 + 클래스 가중치 + 적절한 오버샘플링(또는 샘플링 전략) + Focal Loss
조합이 가장 안정적으로 높은 성능을 보였다.

5.4 최종 선택 모델 & 전체 데이터 라벨링
5.4.1 최종 학습 스냅샷 (예시)

Epoch별 로그(대표):

Epoch	Train Loss	Train Acc	Val Acc
1	0.26	0.51	0.18
2	0.12	0.80	0.81
3	0.05	0.96	0.87
4	0.03	0.98	0.84
5	0.02	0.99	0.87

Early Stopping 관점에서 Epoch 3~5 사이가 최적 구간

최종적으로 Validation Accuracy 약 0.87 수준의 DeBERTa 모델을 채택

5.4.2 전체 데이터 예측 결과

최종적으로 선택한 Binary DeBERTa v3 모델을 사용하여
정제된 전체 데이터(FINAL_DATA_ROWS_DELETED*.csv)에 감성 라벨을 부여하였다.

라벨: sentiment_label

0 = Negative

1 = Positive

sentiment_label
0    18024
1     2905


→ 전체 데이터 기준:

감성	개수	비율
부정	18,024	약 86%
긍정	2,905	약 14%




→ 부정 여론이 압도적으로 높은 구조임을 확인.

6. 시계열 분석: 부정 비율 & 공포·탐욕 지수 비교
6.1 부정 비율 시계열

DeBERTa 모델이 예측한 sentiment_label(0/1)을 기반으로
날짜(date)별 부정 비율을 계산하였다.

월별/분기별로 부정 비율의 추세를 확인

전반적으로 팬데믹 진행 내내 부정 비율이 높은 수준을 유지

정책 발표, 백신 의무화 논의 시점 등에서
특정 구간의 부정 비율이 더욱 상승하는 패턴을 관찰

6.2 공포·탐욕 지수와의 비교 (초기 vs 최종)
6.2.1 초기 전처리 결과 (실패 사례)

피어슨 상관계수 ≈ -0.006

DTW 거리 ≈ 1280

→ 시계열 패턴도 다르고, 상관 관계도 거의 0
→ 전처리/데이터 정의 자체가 실패했다고 판단

6.2.2 최종 전처리 결과

최종 정제 데이터 + DeBERTa 라벨링 결과를 활용하여
부정 비율 시계열과 공포·탐욕 지수를 다시 비교:

피어슨 상관계수:

r ≈ -0.3337

p-value ≈ 0.0853 (유의수준 0.05에는 살짝 못 미침)

DTW 거리:

DTW ≈ 3.44 (매우 작은 거리)




해석:

피어슨 관점:

약한 음의 상관관계 존재 (규모는 크지 않으나 0은 아님)

DTW 관점:

시계열의 모양 자체는 상당히 유사한 패턴을 보임

초기 전처리 대비 패턴 유사도가 극적으로 개선

→ 최종 전처리 + 라벨링 파이프라인이 성공적으로 작동했음을 강하게 시사

7. 토픽 모델링 (Topic Modeling)

토픽 모델링은 두 단계로 진행했다.

초기 LDA – 전처리 문제 파악, 전반적 주제 윤곽 파악

BERTopic + HDBSCAN – 노이즈 제거 + 정교한 토픽 추출

7.1 LDA 1차 분석 (문제점 & 인사이트)

초기 LDA(BoW 기반, num_topics=10):

중복/가비지 토픽:

URL, https, www, comments …

정치인 이름만 잔뜩 나오는 토픽 등

코로나/백신과 직접적인 관련성이 낮은 토픽 다수

하지만, 동시에 다음과 같은 중요한 인사이트도 제공:

경제·금융 토픽: company, money, debt, share, loan …

마스크 토픽: mask, wear, face, protect …

정치 갈등 토픽: trump, american, country, world …

의료/건강 토픽: doctor, health, risk, vaccination …

→ 데이터가 실제로

백신 부작용/의학 이슈뿐 아니라,

정책·경제·정치·마스크 의무화 등을 많이 다루고 있다는 점을 확인.

동시에, 잡음이 많다는 것도 명확해져서
전처리 파이프라인 개선의 동기가 되었다.

7.2 BERTopic 기반 최종 토픽 모델링

전처리를 강화한 뒤, BERTopic을 사용해 토픽 모델을 구축.

내부적으로 HDBSCAN 클러스터링 사용:

밀집도가 낮은 문서는 **노이즈 토픽(-1)**로 분류

의미 있는 클러스터만 남기는 데 유리

토큰화/벡터화 후, 상위 토픽에 대해 해석 가능한 레이블 부여

7.2.1 부정 리뷰 토픽 (상위 10개)
================================================================================
부정 리뷰 토픽 (총 18,024건 중 의미 있는 토픽 8,461건)
================================================================================
| Topic | Count | Name                                     | Representation                                                                         |
|------:|------:|:-----------------------------------------|:---------------------------------------------------------------------------------------|
| 0     | 509   | 0_hospitals_hospital_debt_pay           | hospitals, hospital, debt, pay, healthcare, nurses, money, loans, doctors, beds        |
| 1     | 353   | 1_walmart_store_customers_masks         | walmart, store, customers, masks, enforce, employees, mask, stores, local, wear        |
| 2     | 308   | 2_trump_death_threats_deaths            | trump, death, threats, deaths, president, responsible, man, fauci, celebrate, politics |
| 3     | 293   | 3_vaccine_vaccines_take_get             | vaccine, vaccines, take, get, people, want, evidence, vaccinated, taking, doctors      |
| 4     | 283   | 4_coronavirus_coronaviruses_virus_people| coronavirus, coronaviruses, virus, people, corona, cases, papers, whatsapp, new, think |
| 5     | 252   | 5_covid_vaccine_vaccinated_19           | covid, vaccine, vaccinated, 19, effects, vaccines, infection, long, side, died         |
| 6     | 203   | 6_kids_children_vaccines_parents        | kids, children, vaccines, parents, pediatrician, age, vaccine, child, baby, kid        |
| 7     | 184   | 7_asthma_breathing_breathe_oxygen       | asthma, breathing, breathe, oxygen, mask, wear, lung, inhaler, copd, wearing           |
| 8     | 164   | 8_covid_hoax_19_propaganda              | covid, hoax, 19, propaganda, fox, deniers, believe, truth, stop, news                  |
| 9     | 158   | 9_vaccine_vaccines_anti_rfk             | vaccine, vaccines, anti, rfk, flint, placebo, injecting, guy, film, water              |
--------------------------------------------------------------------------------
총 분석 문서 수: 18024 | 노이즈(-1) 토픽 문서 수: 9563 | 의미 있는 토픽 문서 수: 8461
================================================================================


해석 요약:

토픽 0 – 의료 시스템/비용/부채

병원, 빚, 의료비, 간호사 부족, 병상 부족 등

“의료 시스템에 대한 경제적 불만”

토픽 1 – 상점·마스크 의무화

월마트 등 상업 시설에서 마스크 착용 강제, 고객·직원 갈등

토픽 2 – 정치/트럼프/책임 공방

대통령과 사망, 책임 여부, 위협, 정치적 분열

토픽 3/5 – 백신 접종·부작용 우려

백신을 맞을지 말지, 부작용, 장기 코로나, 사망

토픽 6 – 아동 백신

아이 접종, 부모, 소아과 의사 등

토픽 7 – 마스크 vs 호흡기 질환자

천식·COPD 등 환자의 마스크 착용 문제

토픽 8/9 – 코로나/백신 음모론, hoax, propaganda

→ 부정 리뷰는 단순히 “백신 걱정”에 그치지 않고
의료비, 정책/의무화, 정치, 음모론 등으로 매우 넓게 분포.

7.2.2 긍정 리뷰 토픽 (상위 10개)
================================================================================
긍정 리뷰 토픽 (총 2,905건 중 의미 있는 토픽 1,701건)
================================================================================
| Topic | Count | Name                               | Representation                                                                     |
|------:|------:|:-----------------------------------|:-----------------------------------------------------------------------------------|
| 0     | 201   | 0_vaccine_vaccines_vaccinated_mrna | vaccine, vaccines, vaccinated, mrna, people, get, unvaccinated, take, immune, like |
| 1     | 131   | 1_hospital_hospitals_patients_pay  | hospital, hospitals, patients, pay, work, medical, care, nurses, debt, school      |
| 2     | 122   | 2_pfizer_moderna_side_effects      | pfizer, moderna, side, effects, got, vaccine, shot, second, arm, dose              |
| 3     | 107   | 3_death_deaths_people_die          | death, deaths, people, die, life, rate, cases, dying, think, per                   |
| 4     | 101   | 4_covid_19_clap_yes                | covid, 19, clap, yes, fdr, believe, saw, covid19, days, think                      |
| 5     | 95    | 5_walmart_store_mask_employees     | walmart, store, mask, employees, customers, masks, wearing, wal, mart, stores      |
| 6     | 89    | 6_death_man_trump_people           | death, man, trump, people, threats, knew, could, larry, cain, guy                  |
| 7     | 76    | 7_coronavirus_bosch_tests_corona   | coronavirus, bosch, tests, corona, rapid, trump, test, message, going, one         |
| 8     | 57    | 8_wear_mask_masks_wearing          | wear, mask, masks, wearing, want, people, condition, face, medical, protect        |
| 9     | 56    | 9_covid_vaccine_mrna_vaccines      | covid, vaccine, mrna, vaccines, immune, 19, tumor, vaccinated, system, spike       |
--------------------------------------------------------------------------------
총 분석 문서 수: 2905 | 노이즈(-1) 토픽 문서 수: 1204 | 의미 있는 토픽 문서 수: 1701
================================================================================


해석 요약:

백신 효과 옹호/경험 공유 (토픽 0, 2, 9)

mRNA, 효과, 면역 형성, 경미한 부작용 경험 등

의료진/의료 시스템에 대한 지지 (토픽 1)

병원, 간호사, 환자, 돌봄, 의료 행위의 가치

마스크 착용/규정 준수 긍정 (토픽 5, 8)

마스크 착용이 안전과 보호를 위한 수단이라는 인식

코로나 위험성에 대한 현실적 인정 (토픽 3, 4)

사망률, 확진자 수, 조치 필요성 등

7.2.3 부정 vs 긍정: 의료 시스템 인식 비교
관점	핵심 키워드	의미
부정 (Topic 0)	hospital, debt, pay, healthcare, nurses	의료 시스템 = 경제적 부담과 과부하, 빚/비용/인력 부족 문제
긍정 (Topic 1)	hospital, patients, care, nurses	의료 시스템 = 필수적 공공재, 의료진의 헌신과 환자 돌봄

→ 동일한 ‘병원/의료’라는 주제도

어떤 리뷰에서는 비용/빚/부담의 대상으로,

다른 리뷰에서는 헌신/돌봄/필요로 인식 →
여론의 양면성을 잘 보여준다.

7.2.4 감성별 토픽 시계열 변화 (개략)

부정 감성:

초기에는 백신 부작용/효능(Topic 3, 5) 관련 토픽 비중이 크다가,

**백신/마스크 의무화, 정책 이슈(Topic 1)**가 등장하면서
점차 정책·자유·의무화 논쟁이 중심 축으로 떠오름

중립 감성(Three-Class 실험 기준):

백신 정보 문의, 장기 코로나 정보 등
“정보 부족, 불확실성” 관련 토픽이 안정적으로 존재

긍정 감성:

전 기간에 걸쳐 백신 접종 경험 공유 + 예방 효과 기대가 우세

8. 종합 결론

감성 분포

정제된 전체 데이터 기준, DeBERTa Binary 모델 결과:

부정 ≈ 86%, 긍정 ≈ 14%

코로나/백신 관련 온라인 댓글은
전체적으로 부정 여론이 월등히 우세

백신 논란의 축

초기 LDA + BERTopic + 시계열 분석을 종합하면,

부작용/의학적 위험도 중요한 요소이지만,

장기적으로는 정책·의무화·경제·정치 갈등이
논란의 중심 축으로 이동

특히, 마스크/백신 의무화(Mandate) 이슈가
부정 여론을 크게 자극하는 패턴을 확인

공포·탐욕 지수와의 관계

전처리 실패 상태에서는 거의 무관한 패턴(r ≈ -0.006)

최종 전처리 + DeBERTa 적용 후:

피어슨 r ≈ -0.33 (약한 음의 상관)

DTW ≈ 3.44 (패턴 상당히 유사)

→ 온라인 부정 여론 비율이 상당 부분 공포·불안 심리와 동행하는 보조 지표로 활용 가능함을 시사

방법론적 성과

크롤링 난관 → 전처리 재설계 → 불균형 해결 → 모델 교체 → 토픽/시계열 결합까지
엔드투엔드 파이프라인을 실제로 구축하고 검증한 사례

특히, “초기 실패(상관 거의 0) → 전처리 개선 → 상관/DTW 개선”이라는 과정을 통해
전처리/정의가 결과에 미치는 영향을 정량적으로 보여줌

9. 한계점 및 향후 과제
9.1 한계점

Validation Accuracy ~0.87 수준

딥러닝 모델로서 나쁘지 않지만,

완전히 노이즈 없는 수준은 아니며 라벨 오류 가능성 항상 존재

데이터 편향

Reddit/영어권 포럼에 크게 의존 →
특정 문화/정치 구도(예: 미국)의 영향을 크게 받음

Neutral 감성 구분의 어려움

Three-Class 실험에서 중립 클래스 분리가 매우 어려웠고,
결국 최종 파이프라인은 Binary 중심으로 설계

외부 지표와의 통계적 유의성

p-value ≈ 0.0853 → 경향성은 있지만,
0.05 기준의 “강한 통계적 유의성”까지는 도달하지 못함

9.2 향후 과제

모델 개선 및 일반화

다른 아키텍처(예: RoBERTa, GPT 계열)에 대한 비교

Multi-task 학습(감성+토픽/속성 동시 예측) 등 시도

데이터 정제 및 필터링 자동화

주제 관련성 필터링을 규칙 기반이 아닌
**모델 기반(Relevance Classifier)**으로 자동화

다국어/다지역 비교

한국, 유럽, 기타 국가의 포럼/댓글 데이터를 추가해
국가별 백신 논란 패턴 비교

공포·탐욕 지수 외 다른 지표와의 결합

소비/이동 데이터, 주가 변동성, 정책 이벤트 타임라인 등과 결합해
더 풍부한 해석 시도

10. 연구 결과의 활용 가능성
10.1 공중보건 정책 및 위험 커뮤니케이션

본 연구는 부정 감성이 “부작용 자체”보다
“의무화·정책·경제·정치 갈등”에 더 민감하다는 것을 보여준다.

향후 팬데믹 대응에서는:

“백신은 안전하다”라는 메시지 뿐 아니라

정책의 공정성, 강제성의 한계, 보상 구조, 결정 과정의 투명성을
함께 설명하는 커뮤니케이션 전략이 필요.

10.2 의료기관·의료정책 분야

부정 토픽에서 병원, 비용, 빚, 간호 인력 부족이 반복 등장.

이는 백신 논란이 순수 의학적 이슈를 넘어
의료 접근성·비용 문제와 직결됨을 의미.

정책적으로는:

무료 접종, 보험 적용, 의료비 지원 같은 정보 제공 강화가
부정 여론 완화에 중요한 역할을 할 수 있음.

10.3 플랫폼·언론·팩트체크

코로나 hoax, propaganda, 반백신 음모론 토픽이
개별 클러스터로 뚜렷하게 추출됨.

이를 바탕으로:

특정 키워드/프레이밍을 중심으로
왜곡 정보가 확산되는 패턴을 모니터링

hoax/propaganda 토픽 비중이 급증할 때

팩트체크 콘텐츠 노출 강화

공신력 있는 정보 상단 고정

추천 알고리즘 조정 등의 개입 전략 설계 가능

10.4 금융·리스크 관리 지표

최종 전처리 데이터 기준,
부정 감성 비율 시계열은 공포·탐욕 지수와
패턴적으로 상당히 유사(DTW 기준).

이는 온라인 여론이 시장 심리/리스크의 보조 지표로
사용될 수 있음을 보여준다.

특정 정책 이슈(백신 패스, 락다운, 의무화 등) 관련
부정 여론 지표를
소비·투자·이동 심리와 연결한 리스크 모니터링 지표로 확장 가능.

10.5 학술 연구 및 시스템·서비스 확장

본 프로젝트는

크롤링 → 다단계 전처리 → 주제 필터링 →
불균형 완화 → 감성 분석 → 토픽 분석 → 시계열/외부 지표 결합에 이르는

완전한 분석 파이프라인을 실제로 구현·검증했다.

이를 바탕으로:

방법론 중심 논문 (텍스트 전처리, 불균형 해소, 모델 비교)

사회과학/보건학 논문 (정책·신뢰·위험 커뮤니케이션 분석)
양쪽으로 확장 가능.

동일 파이프라인을

기후변화, 원전, AI 규제, 선거 이슈 등으로도 적용 가능 →
“이슈별 온라인 여론 모니터링 대시보드”로 발전 여지.

11. 코드 구조 (Code Structure)

🧱 이 섹션은 깃허브 코드 구조를 정리하기 위한 자리입니다.
실제 코드 구조에 맞게 나중에 채워 넣으세요.

project_root/
├─ data/
├─ scripts/
├─ models/
├─ notebooks/
├─ utils/
└─ README.md


data/ : (데이터셋 관련 폴더 설명 예정)

scripts/ : (실험/학습/평가 스크립트 설명 예정)

models/ : (학습된 모델 가중치 및 관련 파일 설명 예정)

notebooks/ : (EDA 및 실험용 Jupyter Notebook 설명 예정)

utils/ : (공통 유틸 함수/모듈 설명 예정)

12. 모델 학습 스크립트 개요 (Training Scripts)

🧪 이 섹션에는 학습용 파이썬 스크립트 구조/사용법을 정리할 예정입니다.

scripts/
├─ train_kElectra_binary.py
├─ train_kElectra_three_class.py
├─ train_deberta_binary.py
├─ train_deberta_three_class.py
└─ utils_training.py


각 스크립트 예시 설명 (추후 실제 구조에 맞게 수정):

train_kElectra_binary.py

역할: KoELECTRA 기반 이진 감성 분류 학습

주요 인자(예시): --lr, --epochs, --batch_size, --use_class_weight …

train_kElectra_three_class.py

역할: KoELECTRA 기반 3-Class 감성 분류(부정/중립/긍정) 학습

주요 인자(예시): --lr, --epochs, --focal_loss, --oversampling …

train_deberta_binary.py

역할: DeBERTa v3 기반 이진 감성 분류 최종 모델 학습

주요 인자(예시):

--lr

--epochs

--batch_size

--use_focal_loss

--use_class_weight

--model_name (예: microsoft/deberta-v3-base)

train_deberta_three_class.py

역할: DeBERTa 기반 3-Class 감성 분류 실험용 스크립트 (실험 결과 요약만 사용)

utils_training.py

공통 학습 루프, metric 계산, 데이터로더 준비 등 유틸 함수 모음

(실제 인자/파일 이름에 맞게 나중에 자세히 채워 넣기)

13. 데이터 폴더 구조 (Data Folder Structure)

🗂 이 섹션에는 원본/전처리/라벨링/예측 결과 파일 구조를 정리합니다.

data/
├─ raw/
│  ├─ reddit_raw.csv
│  ├─ webmd_raw.csv
│  ├─ drugs_raw.csv
│  ├─ healthboards_raw.csv
│  └─ ...
├─ intermediate/
│  ├─ FINAL_DATA_RAW_108K.csv
│  ├─ FINAL_DATA_RELATED_ONLY.csv
│  ├─ FINAL_DATA_ROWS_DELETED.csv
│  ├─ FINAL_DATA_ROWS_DELETED_2.csv
│  └─ ...
├─ labeled/
│  ├─ KOEL_labeled_binary.csv
│  ├─ KOEL_labeled_three.csv
│  ├─ final_manual_labels_binary.csv
│  └─ final_manual_labels_three.csv
├─ predictions/
│  ├─ predicted_binary_deberta.csv
│  ├─ predicted_threeclass_deberta.csv
│  └─ ...
└─ external/
   ├─ fear_greed_index.csv
   ├─ macro_covid_stats.csv
   └─ ...


raw/ :

각 소스별 원본 크롤링 데이터 저장 위치

예: reddit_raw.csv, webmd_raw.csv 등

intermediate/ :

전처리/필터링 중간 결과물 저장

예:

FINAL_DATA_RAW_108K.csv : 통합 후 최소 전처리된 전체 데이터

FINAL_DATA_RELATED_ONLY.csv : 코로나/백신 관련 키워드 필터 후 데이터

FINAL_DATA_ROWS_DELETED*.csv : 수작업 검증 및 추가 필터링 반영본

labeled/ :

수동 라벨링 결과 저장

예: KOEL_labeled_binary.csv, final_manual_labels_binary.csv 등

predictions/ :

학습된 모델이 전체 데이터에 대해 예측한 결과 저장

예: predicted_binary_deberta.csv (최종 DeBERTa 이진 분류 결과)

external/ :

공포·탐욕 지수, 코로나 확진자 수 등

외부 지표/타임시리즈 데이터 저장
