# 한밭대학교 컴퓨터공학과 LLG팀

**팀 구성**
- 20222039 이현정
- 20201779 이병호
- 20201722 고동혁

## <u>Teamate</u> Project Background
- ### 필요성
  - 본 프로젝트는 복잡한 PDF 문서에서 필요한 정보를 쉽고 빠르게 찾을 수 있는 시스템을 제공함으로써, 정보 검색에 어려움을 겪는 사용자들의 디지털 접근성을 크게 향상 할 수 있음
  - 기업 내부 문서 관리 및 검색 시간을 획기적으로 단축하여 직원들의 업무 효율성을 크게 향상시킬 수 있음
- ### 기존 해결책의 문제점
  - 기존의 LLM 시스템은 기업 내부 문서 유출의 문제로 사용이 불가능하거나 비용을 지불하고 사용해야만 함
  - Background가 없는 사용자는 오픈소스인 LLM이나 RAG 서비스 제공, 사용이 어려움
  
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
