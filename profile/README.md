# 📰 NewsFlow

> 뉴스를 찾는 번거로움 없이, 내가 원하는 기사만 — 날짜별·산업별·맞춤형으로.

---

## 🗂 프로젝트 소개

NewsFlow는 흩어진 뉴스를 한 곳에서 탐색할 수 있는 **뉴스 큐레이션 플랫폼**입니다.

산업군별 필터링, 실시간 인기 기사, 맞춤 추천은 물론  
**달력 형식으로 날짜별 기사를 열람하고, 일·월·연 단위 핵심 기사를 자동으로 도출**하는 것이 핵심 기능입니다.

---

## ✨ 핵심 기능

| 기능 | 설명 |
|------|------|
| 📅 **달력 뷰** | 날짜를 클릭하면 해당일의 기사 목록 열람 |
| 🏆 **핵심 기사 도출** | 일별·월별·연별 가장 중요한 기사 자동 선별 |
| 🔥 **실시간 인기 기사** | Redis 기반 실시간 조회수·반응 집계 |
| 🎯 **맞춤 추천** | 사용자 관심사 기반 개인화 기사 추천 |
| 🏭 **산업군 필터** | IT, 금융, 정치, 사회 등 카테고리별 탐색 |

---

## 🏗 아키텍처

```
[ newsFlow-FE ]          Next.js 14 + TypeScript
      │
      │  REST API
      ▼
[ newsFlow-BE-API ]      Spring Boot 3 + Java 17
      │                  집계 · 캐싱 · 인증 · 트래픽 처리
      ├─── Redis          실시간 인기 기사 · 조회수 캐싱
      ├─── PostgreSQL     기사 · 사용자 데이터
      │
      │  내부 통신 (WebClient)
      ▼
[ newsFlow-BE-AI ]       FastAPI + Python 3.11
      │                  맞춤 추천 · 핵심 기사 도출 · 기사 요약
      │
      ▼
[ newsFlow-BE-DATA ]     Scrapy + Airflow + Python 3.11
                         뉴스 수집 · 정제 · 분류 · DB 적재
```

---

## 📦 레포지토리 구성

| 레포지토리 | 역할 | 기술 스택 |
|------------|------|-----------|
| [newsFlow-FE](https://github.com/newsFlow-com/newsFlow-FE) | 프론트엔드 UI/UX | Next.js · TypeScript · Tailwind CSS |
| [newsFlow-BE-API](https://github.com/newsFlow-com/newsFlow-BE-API) | 메인 백엔드 API 서버 | Spring Boot · Java 17 · Redis · PostgreSQL |
| [newsFlow-BE-AI](https://github.com/newsFlow-com/newsFlow-BE-AI) | AI 추천 · 분석 엔진 | FastAPI · Python · Claude API · scikit-learn |
| [newsFlow-BE-DATA](https://github.com/newsFlow-com/newsFlow-BE-DATA) | 데이터 수집 파이프라인 | Scrapy · Airflow · PostgreSQL |

---

## 🛠 전체 기술 스택

**Frontend**  
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![React Query](https://img.shields.io/badge/React_Query-FF4154?style=flat-square&logo=reactquery&logoColor=white)

**Backend API**  
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Java](https://img.shields.io/badge/Java_17-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)

**AI / Data**  
![Python](https://img.shields.io/badge/Python_3.11-3776AB?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat-square&logo=fastapi&logoColor=white)
![Airflow](https://img.shields.io/badge/Airflow-017CEE?style=flat-square&logo=apacheairflow&logoColor=white)
![Claude](https://img.shields.io/badge/Claude_API-D97757?style=flat-square&logo=anthropic&logoColor=white)

**Infrastructure**  
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)

---

## 📅 개발 현황

> 현재 초기 시안 개발 단계입니다.

| 단계 | 레포 | 상태 |
|------|------|------|
| 1️⃣ 데이터 수집 파이프라인 구축 | `newsFlow-BE-DATA` | 🔄 진행 중 |
| 2️⃣ API 서버 구조 설계 | `newsFlow-BE-API` | 📋 예정 |
| 3️⃣ 프론트엔드 프로토타입 | `newsFlow-FE` | 📋 예정 |
| 4️⃣ AI 추천·도출 엔진 | `newsFlow-BE-AI` | 📋 예정 |
