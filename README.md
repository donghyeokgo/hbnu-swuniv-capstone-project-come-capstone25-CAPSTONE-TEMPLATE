# 한밭대학교 컴퓨터공학과 LLG팀

**팀 구성**
- 20222039 이현정
- 20201779 이병호
- 20201722 고동혁

## <u>Teamate</u> Project Background
- ### 필요성
    -  기업 및 기관에서 활용되는 방대한 PDF 문서 내 정보를 빠르고 정확하게 탐색할 수 있는 시스템을 제공하여 사용자의 정보 접근성을 크게 향상시킬 수 있음

    -  복잡한 문서 구조(표, 이미지, 수식 등)로 인해 검색 시간이 오래 걸리던 문제를 해결하여, 내부 문서 관리 및 검색 프로세스를 자동화함으로써 직원의 업무 효율성을 크게 높일 수 있음

    -  사용자가 원하는 정보를 스스로 탐색하는 데 필요한 시간과 부담을 줄여 비즈니스 의사결정 및 현장 대응 속도를 향상시킬 수 있음
- ### 기존 해결책의 문제점
    - 대부분의 상용 LLM 기반 문서 검색 서비스는 기업 내부 문서의 외부 전송 위험 때문에 도입하기 어렵거나, 높은 사용 비용을 지불해야만 함

    - 오픈소스 LLM 또는 RAG 기술을 직접 구축하려면 AI/서버/검색 엔진 등에 대한 사전 지식이 필수적으로, 기술적 배경이 없는 사용자 또는 조직은 활용하기 어려움

    - 기존 검색 시스템은 키워드 기반 검색에 의존하는 경우가 많아, 정확한 문구를 모르면 원하는 정보를 찾기 힘들고 문서 전체를 다시 읽어야 하는 비효율이 발생
  
## System Design
  - ### System Requirements
    | 구성       | 최소 요구 사항                                |
    | -------- | --------------------------------------- |
    | Python   | 3.9 이상                                  |
    | OS       | Ubuntu / macOS / Windows(WSL2 포함)       |
    | 실행 권장 환경 | 16GB RAM 이상                             |
    | GPU (선택) | NVIDIA GPU + CUDA 11 이상 --> DPR 인코딩 성능 향상 |

- DPR 모델이 존재하지 않는 경우, 모델 디렉토리에 학습된 DPR 모델이 없을 시 자동으로 klue/roberta-base 모델로 fallback 하여 Dense Retrieval 수행
  
<table>
  <tr>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/1c46d5ec-8b4d-459e-8532-3f43036cbbca" width="700"/>
      <br><b>(System) Architecture Overview</b>
    </td>
    <td align="center">
      <img src="https://github.com/user-attachments/assets/210917cd-f43e-4511-9cfd-9de1c9680b0e" width="500"/>
      <br><b>(Sequence) Retrieval Process</b>
    </td>
  </tr>
</table>

- Architecture Overview

  - Hybrid Retrieval 기반 RAG 시스템

    - BM25 키워드 검색 + DPR Dense 검색을 결합하여 더 정확한 문서 검색 수행

    - BM25는 키워드 일치 / DPR은 의미적 유사도 검색 → 점수 결합으로 Top-k 선정

  - PDF 기반 문서 전처리 & 인덱싱

      - PDF → 텍스트 추출 → 의미 단위 청킹(chunking)

      - 각 청크에 metadata(문서 ID, 페이지, 섹션) 부여

      - BM25 인덱스 + FAISS(Dense embedding) 인덱스 동시 구성

  -  질의 입력 후 검색 & LLM 컨텍스트 생성

      -  사용자 질의 → DPR 질문 인코더로 임베딩 → Dense 검색 수행

      -  동시에 BM25 검색 수행 → 두 결과를 하이브리드로 재랭킹

      - Top-k 문서를 LLM 프롬프트로 삽입해 답변 생성

  - LLM 기반 답변 생성

      -  System prompt + 사용자 질문 + 검색된 문서들을 기반으로 LLM 호출

      -   컨텍스트 기반 답변 생성으로 환각(hallucination) 감소

  -  FastAPI 기반 서비스화

      - /rag 엔드포인트 통해 RAG 파이프라인 end-to-end 수행

      -  웹 UI, 내부 업무 시스템, 챗봇 등과 쉽게 연동 가능

      -  확장 가능 구조로 도메인 변경 시에도 동일 파이프라인 사용 가능

## Case Study
  - ### Description
  
  
## Conclusion
- RAG 기반 문서 검색 시스템은 PDF 문서의 복잡성으로 인한 정보 탐색 문제를 효과적으로 해결함

- 기존 키워드 기반 검색 대비 검색 품질·정확도·응답 신뢰도에서 높은 성능을 보임

- 기업·학교·공공기관 등 문서 기반 지식 전달 환경에서 내부 문서를 안전하게 활용할 수 있는 대안이 됨
  - ### 기대효과
    - PDF 문서 검색 시간을 대폭 감소시키고 반복 업무를 줄여 업무 효율성 향상

    - 외부 서버로 문서가 업로드되지 않아 데이터 유출 위험 제거

    - 오픈소스 기반이므로 비용 부담을 줄이면서도 확장 가능한 구조
  
## Project Outcome
- ### 2025 캡스톤디자인 작품전시회
