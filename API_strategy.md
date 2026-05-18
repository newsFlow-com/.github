# 📡 NewsFlow — 뉴스 데이터 소스 전략

> 개발 단계별 뉴스 API 사용 계획 및 배포 단계 전환 로드맵

---

## ✅ 확정 소스 요약

| 구분 | 소스 | 방식 | 개발 단계 | 배포 단계 |
|------|------|------|----------|----------|
| 국내 뉴스 | 언론사 RSS | RSS/Atom 피드 직접 수집 | ✅ 무료 | ✅ 무료 유지 |
| 해외 뉴스 | NewsAPI.org | REST API | ✅ 무료 (100건/일) | ⚠️ 대체 필요 |
| 트렌드 보조 | PyTrends | 비공식 Google Trends | ✅ 무료 | ✅ 무료 유지 (불안정 주의) |

---

## 1️⃣ 국내 뉴스 — RSS 직접 수집

### 개요
- **방식:** `feedparser` 라이브러리로 RSS/Atom XML 파싱
- **비용:** 무료 (개발/배포 모두)
- **수집 대상 언론사 (초기 세팅)**

| 분류 | 언론사 | RSS 여부 |
|------|--------|---------|
| 공영방송 | KBS, MBC, SBS | ✅ |
| 종합일간지 | 조선일보, 중앙일보, 동아일보, 한겨레, 경향신문 | ✅ |
| 경제지 | 한국경제, 매일경제, 서울경제 | ✅ |
| IT/기술 | 전자신문, IT조선, ZDNet Korea | ✅ |

### 주요 제약
- 기사 **본문 전체가 아닌 요약(description)** 만 제공되는 경우 있음
- 언론사마다 RSS 구조(필드명, 날짜 포맷 등)가 다름 → 파서 정규화 필요
- 일부 언론사는 RSS를 비공개 전환하거나 업데이트를 중단하는 경우 있음

### 참고 리소스
- GitHub: [`akngs/knews-rss`](https://github.com/akngs/knews-rss) — 국내 언론사 RSS 모음

---

## 2️⃣ 해외 뉴스 — NewsAPI.org (개발 단계)

### 개요
- **방식:** REST API (`https://newsapi.org/v2/`)
- **무료 Developer 플랜 제약**

| 항목 | 내용 |
|------|------|
| 요청 제한 | 100건 / 일 |
| 기사 딜레이 | 24시간 지연 |
| 본문 제공 | ❌ 제목 + 요약 + URL만 제공 |
| 프로덕션 사용 | ❌ 불가 (개발/테스트 전용) |
| 과거 데이터 | 최대 1개월 |

### 사용 엔드포인트
```
GET /v2/top-headlines   # 국가·카테고리별 헤드라인
GET /v2/everything      # 키워드·기간 검색
GET /v2/sources         # 뉴스 소스 목록
```

### 개발 단계 활용 전략
- 하루 100건 제한이므로 **Airflow DAG를 1회/일 실행**으로 세팅
- 카테고리별 호출 분산: `business`, `technology`, `science`, `health` 각 25건
- 수집된 데이터로 DB 스키마 검증 및 파이프라인 테스트 용도로 활용

---

## 3️⃣ 트렌드 보조 — PyTrends

### 개요
- **방식:** Google Trends 비공식 Python 라이브러리
- **비용:** 완전 무료 (오픈소스)
- **GitHub:** [`GeneralMills/pytrends`](https://github.com/GeneralMills/pytrends)

### 활용 목적
- 날짜별 급상승 검색어 수집 → 기사 중요도 가중치 산정에 활용
- 키워드 트렌드 시계열 데이터 → 월별·연별 리포트 보조 지표

### 주요 제약
- **비공식 라이브러리** → Google이 정책 변경 시 동작 불안정 가능
- Rate limit 초과 시 `429 TooManyRequests` 발생 → 수집 주기를 **1회/일** 이하로 제한
- 실시간 인기 검색어(`realtime_trending_searches`)는 한국(`KR`) 미지원

### 수집 항목
```python
# 일별 급상승 검색어 (한국)
pytrends.trending_searches(pn='south_korea')

# 키워드 관심도 시계열
pytrends.build_payload(['AI', '반도체'], geo='KR', timeframe='today 3-m')
pytrends.interest_over_time()
```

---

## 🔄 배포 단계 전환 계획

### 전환이 필요한 소스: NewsAPI.org → 대체 API

배포 시 NewsAPI.org 무료 플랜은 프로덕션 사용이 금지되어 있으므로, 아래 중 하나로 전환합니다.

### 대체 옵션 비교

| 옵션 | 비용 | 무료 한도 | 상업 사용 | 특징 |
|------|------|----------|---------|------|
| **GNews API** | 유료 시 $19/월~ | 100건/일 | ✅ (API 데이터 한정) | Google News 랭킹 기반, 간단한 구조 |
| **NewsData.io** | 무료 플랜 있음 | 200건/일 | ✅ 무료도 가능 | 206개국, 89개 언어, 감성 분석 내장 |
| **The Guardian API** | 완전 무료 | 제한 없음 | ✅ | 단일 소스(가디언)이지만 품질 높음 |
| **RSS 확장** | 무료 | 무제한 | ✅ | 해외 언론사 RSS 직접 추가 (BBC, Reuters 등) |

### 권장 전환 시나리오

```
[1안 — 권장] NewsData.io 무료 플랜으로 전환
  → 이유: 상업 사용 허용, 하루 200건, 무료, 별도 비용 없음

[2안 — 보조] 해외 언론사 RSS 직접 추가
  → BBC, Reuters, AP, Bloomberg RSS 피드 추가 수집
  → 비용 0원, 완전한 프로덕션 사용 가능

[3안 — 예산 확보 시] GNews 또는 NewsData.io 유료 플랜
  → 안정적인 대량 수집이 필요해지는 시점에 검토
```

### 전환 시점
- BE-DATA 개발 완료 후 배포 준비 단계 (3단계 FE 완성 전후)
- 수집기 코드에서 API 클라이언트를 **추상화(interface)** 해두어 전환 비용 최소화

---

## 📋 수집기 코드 추상화 전략

배포 단계 전환을 쉽게 하기 위해 수집기를 아래 구조로 설계합니다.

```
collectors/
├── base_collector.py       # 추상 베이스 클래스
├── rss_collector.py        # 국내 RSS 수집기
├── newsapi_collector.py    # NewsAPI.org 수집기 (개발용)
├── newsdata_collector.py   # NewsData.io 수집기 (배포용, 미리 작성)
└── pytrends_collector.py   # PyTrends 트렌드 수집기
```

```python
# base_collector.py 예시
from abc import ABC, abstractmethod

class BaseNewsCollector(ABC):
    @abstractmethod
    def fetch(self, **kwargs) -> list[dict]:
        """기사 목록 반환. 모든 수집기가 동일한 인터페이스 구현."""
        pass
```

> 수집기 교체 시 `newsapi_collector` → `newsdata_collector`로 DAG 설정만 변경하면 됩니다.

---

## 📊 단계별 데이터 소스 현황

| 단계 | 국내 | 해외 | 트렌드 |
|------|------|------|--------|
| **개발 (현재)** | RSS (무료) | NewsAPI.org (무료, 100건/일) | PyTrends (무료) |
| **배포 초기** | RSS (무료) | NewsData.io 무료 or 해외 RSS | PyTrends (무료) |
| **서비스 성장 시** | RSS (무료) | NewsData.io 유료 or GNews 유료 | PyTrends or Google Trends API |
