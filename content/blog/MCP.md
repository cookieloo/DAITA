---
title: "MCP Protocol"
date: 2025-04-30
draft: false
---


# MCP 소개

[Model Context Protocol](https://modelcontextprotocol.io/introduction) (MCP)는 [Anthropic에서 2024년 말에 도입한](https://www.anthropic.com/news/model-context-protocol)   새로운 개방형 표준으로, AI 애플리케이션이 다양한 외부 도구, 데이터 소스, 시스템과 원활히 상호 작용할 수 있도록 설계되었습니다. 이는 기존의 파편화된 통합 방식을 대체하고, 도구와 데이터 소스에 대한 보편적인 프레임워크를 제공하여 AI 시스템이 다양한 컨텍스트에 쉽게 접근하도록 지원합니다. MCP는 AI 통합을 위한 **USB-C**와 같은 역할을 하며, 기존의 복잡한 M×N 문제를 단순한 M+N 문제로 변환합니다.

![api](https://www.philschmid.de/static/blog/mcp-introduction/api.png)

<p align="center">
  <a href="https://www.philschmid.de/mcp-introduction">출처</a>
</p>


# MCP의 구조와 작동 원리
MCP는 Host-Client-Server의 3층 구조로 설계되었으며 JSON-RPC로 통신합니다.
- **Host**: 사용자가 직접 상호작용하는 애플리케이션으로, 여러 클라이언트를 관리하고 보안 정책을 적용합니다.
- **Client**: 호스트 내부에서 MCP 서버와의 연결을 관리하며 기능 협상 및 보안 경계를 유지합니다.
- **Server**: 외부 시스템과 MCP를 연결하는 다리 역할로, 표준 API를 통해 도구, 리소스, 프롬프트를 제공합니다.
![overview](https://www.philschmid.de/static/blog/mcp-introduction/overview.png)

<p align="center">
  <a href="https://www.philschmid.de/mcp-introduction" align="center">출처</a>
</p>

# MCP의 혁신적 기술 및 특징
## 표준화된 도구 인터페이스

MCP는 다음의 세 가지 인터페이스를 표준화하여 제공합니다:
- **Tools (Model-controlled)**: AI가 특정 작업 수행을 위해 호출하는 함수(API)
- **Resources (Application-controlled)**: AI가 접근 가능한 데이터 소스
- **Prompts (User-controlled)**: 특정 작업 수행을 위한 사전 정의된 템플릿
![overview](https://www.philschmid.de/static/blog/mcp-introduction/overview.png)

<p align="center">
  <a href="https://www.philschmid.de/mcp-introduction" align="center">출처</a>
</p>

## N×M 문제 해소
기존의 복잡한 통합 문제를 해결해 MCP 서버 하나만 개발하면 여러 AI 클라이언트와 통합이 가능합니다.
## 재현성
모든 데이터셋, 환경 설정, 하이퍼파라미터 등을 통합 관리하여 결과의 재현성을 높입니다.
## 동적 검색
AI 모델이 사용 가능한 도구를 동적으로 검색하고 활용할 수 있게 합니다.
## 양방향 및 보안
MCP는 실시간 양방향 통신을 지원하며 OAuth 2.1 인증 프레임워크를 통해 강력한 보안과 규정 준수를 보장합니다.
## 확장성
컨텍스트 관리를 표준화하여 LLM의 효율성을 높이고, 명확한 역할 분리로 보안 제어를 강화합니다.
### 기존 API와 MCP의 차이
- **설계 목적의 차이**
    - 기존 API(OpenAPI, GraphQL, SOAP 등): 특정 기능 수행 및 데이터 검색 중심
    - MCP: AI 에이전트 및 언어 모델(LLM)의 컨텍스트 활용과 추론 능력 향상에 특화
- **통신 방식의 차이**
    - 기존 API: 단방향 요청-응답 방식
    - MCP: 실시간 양방향 통신 지원, 복잡한 워크플로 관리 및 자율적 작업 수행 가능

# MCP는 어떻게 동작하나요?

![architecture](https://www.philschmid.de/static/blog/mcp-introduction/architecture.png)

<p align="center">
  <a href="https://www.philschmid.de/mcp-introduction" align="center">출처</a>
</p>

다음은 **"사용자가 서울로 주말 여행을 떠나고 싶어 하며, 날씨 정보, 인기 여행지, 현지 맛집, 대중교통 정보를 고려해 여행 계획을 수립하는 경우"**를 가정한 MCP 동작 방식 예시입니다:

### 🧭 MCP를 활용한 여행 계획 수립 예시
**가정: 사용자가 주말 동안 서울을 여행하고 싶어 하며, 다음과 같은 정보를 포함하여 여행 일정을 계획하려 합니다.**
- 서울의 주말 날씨 정보
- 서울의 인기 관광 명소
- 현지인이 추천하는 서울 맛집
- 서울 내 교통편 및 대중교통 정보
    
이 가정을 바탕으로 MCP의 동작 흐름은 다음과 같습니다:
1. **초기화 (Initialization)**
	- Host 앱(예: Claude Desktop)이 시작되면 MCP 클라이언트가 생성되어 MCP 서버와 연결을 설정합니다.
2. **탐색 (Discovery)**
	- 클라이언트는 서버가 제공하는 기능(도구, 리소스, 프롬프트)을 확인합니다.
	- 서버는 "날씨 정보 조회", "관광지 추천", "맛집 리스트", "대중교통 조회" 등의 기능을 제공합니다.
3. **컨텍스트 제공 (Context Provision)**
	- 사용자가 "서울에서 주말 여행을 계획하고 싶어요"라고 입력하면, 호스트는 해당 컨텍스트를 MCP가 이해할 수 있는 형태로 제공합니다.
4. **호출 (Invocation)**
	- AI 모델이 "서울의 이번 주말 날씨 정보", "서울의 인기 관광지 추천", "서울 현지 맛집 목록", "서울 교통수단 정보"가 필요하다고 판단하고 요청을 생성합니다.
	- Host는 이를 MCP 클라이언트를 통해 서버로 전달합니다.
5. **실행 (Execution)**
	- MCP 서버는 요청을 받고 각각의 정보를 수집합니다. 예를 들어 날씨 API, 관광정보 데이터베이스, 집 추천 플랫폼, 대중교통 API 등에서 데이터를 조회합니다.
6. **응답 (Response)**   
	- 서버는 수집된 날씨 정보, 인기 관광지 목록, 맛집 정보, 교통 정보를 클라이언트를 통해 Host로 반환합니다.
7. **완료 (Completion)**
	- Host는 전달받은 정보를 AI 모델 컨텍스트에 통합하여, 사용자에게 최적화된 서울 여행 일정을 추천합니다.
        
# 왜 이렇게 화제가 되었을까? MCP가 승리한 이유는?
- **AI-Native 설계**
    - MCP는 AI 에이전트 요구에 맞춰 설계됨
    - 도구(Tools), 리소스(Resources), 프롬프트(Prompts)의 명확한 역할 구분
- **우수한 오픈 명세와 강력한 후원**
    - 뛰어난 공개 명세 제공 (명세 자체로 경쟁자 압도)
- **검증된 기술 기반**
    - 기존의 Language Server Protocol(LSP), JSON-RPC 2.0 등 신뢰성 높은 표준 활용
- **강력한 초기 생태계와 직접 활용(Dogfooding)**
    - Anthropic이 Claude Desktop, git, Slack 등 다양한 내부 레퍼런스 구현 제        
    - MCP Inspector, SDK(Python, TypeScript, Java, Kotlin, C# 등) 지원으로 초기 확산 가속화
- **네트워크 효과로 인한 확산**
    - Cursor, Windsurf, Composio 등의 기업과 커뮤니티가 빠르게 MCP를 통합 및 확장
    - OpenAI를 포함한 주요 기업들도 MCP 지원 발표
    - GitHub, Slack, 데이터베이스, Docker 등 수천 개의 MCP 커뮤니티 서버 구축 활성화