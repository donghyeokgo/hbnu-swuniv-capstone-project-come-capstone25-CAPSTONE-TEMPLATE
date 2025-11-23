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

- DPR 모델이 존재하지 않는 경우, 모델 디렉토리에 학습된 DPR 모델이 없을 시 자동으로 klue/roberta-base 모델로 fallback 하여 Dense Retrieval 수행되도록 설계.
- 
<img width="645" height="299" alt="(System) PDF 문서 파싱 기반 RAG 파이프라인_컴퓨터공학과" src="https://github.com/user-attachments/assets/1c46d5ec-8b4d-459e-8532-3f43036cbbca" />

<img width="885" height="604" alt="(Sequence Diagram) PDF 문서 파싱 기반 RAG 파이프라인_컴퓨터공학과_Sequence Diagram" src="https://github.com/user-attachments/assets/210917cd-f43e-4511-9cfd-9de1c9680b0e" />


- Architecture Overview

  - BM25 기반 키워드 검색 + DPR 기반 Dense Retrieval을 조합한 Hybrid RAG 시스템

  - PDF 문서 --> 텍스트 추출 --> 청킹(chunking) --> 인덱싱

  - 사용자가 질의를 입력하면 Retriever가 유사도가 높은 문서를 탐색

  - 상위 문서를 LLM에게 컨텍스트로 제공하여 답변 생성

  - FASTAPI 기반 서버 제공으로 웹, 내부 시스템과 연동 용이


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
