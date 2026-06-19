# DrawQ AI

> CAD 전기 도면을 자동 인식하여 심볼 수량을 카운팅하고, 견적서까지 자동 생성하는 프로그램

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110+-009688.svg)](https://fastapi.tiangolo.com/)

---

## 소개

DrawQ AI는 전기 설계 도면(`.dwg` / `.dxf` / `.pdf`)을 업로드하면 도면에 포함된 전기 심볼을 자동으로 인식하고, 종류별 수량을 집계한 뒤 단가를 적용해 **견적서를 자동 생성**하는 프로그램입니다.

기존에 며칠씩 걸리던 수작업 심볼 카운팅을 수 분 내로 단축합니다.

---

## 주요 기능

### 1. 도면 자동 인식
- `.dwg`, `.dxf`, `.pdf` 도면 파일 직접 업로드
- CAD 레이어 / 블록 구조 자동 파싱
- 스캔 도면(이미지 PDF)도 비전 인식 지원

### 2. AI 심볼 인식 & 수량 카운팅
- **YOLOv9** 기반 심볼 위치 탐지(Detection)
- **ResNet50** 기반 심볼 종류 분류(Classification)
- KS C 0301 기준 **15종 표준 심볼** 분류 (스위치 / 콘센트 / 방수 콘센트 / 통신 / 통합 콘센트 등)
- 공간 그래프(Spatial Graph) 분석으로 중복·누락 보정
- 심볼 종류별 수량 자동 집계 및 신뢰도(Confidence) 표시

### 3. 견적 자동 생성
- 품목 마스터 단가 자동 매핑
- 룰 엔진(Rule Engine) 기반 부속 자재 자동 산출
- 견적서 자동 빌드 (수량 × 단가 → 합계 / 부가세 포함)
- Excel(`.xlsx`) · PDF 견적서 출력

### 4. 검수 & 보정
- 인식 결과를 도면 위에 오버레이로 시각화
- 오인식 심볼 수동 수정 / 추가 / 삭제
- 보정 결과는 모델 재학습 데이터로 누적

---

## 처리 파이프라인

```
도면 업로드
   ↓
CAD Parser        도면 파싱 / 전처리
   ↓
YOLOv9            심볼 위치 탐지
   ↓
ResNet50          심볼 종류 분류
   ↓
Spatial Graph     공간 관계 분석 / 중복 보정
   ↓
Rule Engine       부속 자재 산출
   ↓
Quote Build       견적서 생성
   ↓
견적서 출력 (Excel / PDF)
```

---

## 시스템 요구사항

| 항목 | 최소 사양 |
|------|-----------|
| OS | Windows 10+ / macOS 12+ / Ubuntu 20.04+ |
| Python | 3.10 이상 |
| RAM | 8GB 이상 (16GB 권장) |
| GPU | NVIDIA CUDA 11.8+ (선택, 없으면 CPU 동작) |
| 디스크 | 5GB 이상 여유 공간 |

---

## 다운로드

### Git으로 받기
```bash
git clone https://github.com/voltix/drawq-ai.git
cd drawq-ai
```

### 또는 압축 파일 다운로드
[Releases](https://github.com/voltix/drawq-ai/releases) 페이지에서 최신 버전 `.zip`을 받아 압축을 해제합니다.

---

## 설치

```bash
# 가상환경 생성 (권장)
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate

# 의존성 설치
pip install -r requirements.txt

# AI 모델 가중치 다운로드
python scripts/download_models.py
```

---

## 실행

### 웹 서버(API) 실행
```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```
브라우저에서 `http://localhost:8000` 접속

### 명령줄(CLI) 실행
```bash
# 단일 도면 견적
python drawq.py --input ./drawings/sample.dxf --output ./output/quote.xlsx

# 폴더 일괄 처리
python drawq.py --input ./drawings/ --output ./output/ --batch
```

---

## 사용 방법

1. **도면 업로드** — 웹 화면에서 도면 파일(`.dwg`/`.dxf`/`.pdf`)을 끌어다 놓습니다.
2. **인식 실행** — `분석 시작` 버튼을 누르면 AI가 심볼을 자동 인식합니다.
3. **결과 검수** — 도면 위에 표시된 인식 결과를 확인하고, 필요 시 수정합니다.
4. **견적 생성** — `견적서 생성`을 누르면 수량 × 단가가 적용된 견적서가 만들어집니다.
5. **다운로드** — Excel 또는 PDF로 견적서를 내려받습니다.

### 단가표 설정
`config/price_table.csv`에 품목별 단가를 입력하면 견적 시 자동 반영됩니다.
```csv
symbol_code,item_name,unit_price
S01,일반 스위치,1200
C01,일반 콘센트,1500
...
```

---

## 라이선스

본 프로젝트는 **MIT License**를 따릅니다.

```
MIT License

