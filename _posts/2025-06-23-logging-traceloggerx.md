---
title: "rich.logging을 걷어낸 이유: 실전 디버깅이 만든 나만의 로깅 유틸리티"
excerpt: "UnicodeEncodeError부터 구조적 불편까지. 실무 프로젝트에서 겪은 rich.logging의 한계와 직접 만든 로깅 시스템 traceloggerx 이야기"
layout: single
author_profile: true
read_time: true
categories:
  - Python
tags:
  - logging
  - 실무 디버깅
  - FastAPI
  - PyPI
  - traceloggerx
header:
  teaser: "/assets/images/logging-debug.jpg"
---

## ❓ 처음엔 멋져 보였다: `rich.logging`

Python의 [`rich`](https://github.com/Textualize/rich)는 콘솔에서 예쁜 출력으로 유명하다.  
나 역시 처음에는 traceback 하이라이팅과 색상 출력이 매력적으로 느껴져, `RichHandler`를 사용했었다.

```python
from rich.logging import RichHandler
import logging

logging.basicConfig(
    level=logging.INFO,
    handlers=[RichHandler()]
)
```

하지만 **서비스를 운영하면서 예상치 못한 문제에 부딪혔다.**

---

## 🧨 원인을 알 수 없는 스케줄러 중단

FastAPI 기반의 실무 프로젝트에서 중요한 스케줄러가 자주 중단되기 시작했다.  
이 스케줄러가 멈추면 주문 처리가 중단되는 치명적인 문제였고, 로그에는 다음과 같은 에러가 반복적으로 찍혔다:

```bash
UnicodeEncodeError: 'utf-8' codec can't encode character '\ud83d'
```

당시엔 단순히 아래 환경 변수만 설정하면 해결될 줄 알았다:

```bash
PYTHONIOENCODING=utf-8
```

하지만 문제는 예상보다 훨씬 깊었다.

### 🔍 문제의 본질은 Surrogate Pair + FileHandler

- rich는 콘솔 출력에 특화되어 있어, 내부적으로 `sys.stderr`에 직접 쓰는 방식
- 그러나 파일 핸들러는 locale에 따라 인코딩이 달라지며, 이모지(😅) 등 surrogate pair가 포함될 경우 `UnicodeEncodeError` 발생
- 콘솔 출력은 멀쩡한데, 로그 파일 저장 중 오류가 발생

결국 `rich`를 걷어내고, 다시 기본 `logging`으로 돌아갈 수밖에 없었다.

---

## ⚙️ 그런데 기본 `logging`도 불편했다

`rich` 없이도 로깅은 가능했지만, 금세 다른 문제들이 보이기 시작했다.

- 로그가 전부 text로 섞여 있어 분석이 어렵고
- 각 요청의 `trace_id`, `user_id`를 로그마다 일일이 붙이기도 귀찮고
- 이왕이면 예쁘게 색도 입히고 싶었고
- 무엇보다도 **“지금 무슨 일이, 어디서 났는지”**를 명확하게 남기고 싶었다

처음엔 그냥 커스텀 핸들러 몇 개 짜보자는 생각이었는데, 어느새 내가 필요한 모든 기능을 담은 작은 로깅 유틸리티를 만들고 있었다.

---

## 🔨 그래서 만들었다: `traceloggerx`

이왕 만드는 김에 **회사든 개인 프로젝트든 언제든 붙일 수 있도록** 정리해버렸다.  
내가 원하는 것만 담은 실무형 로깅 툴, `traceloggerx`가 그렇게 만들어졌다.

```bash
pip install traceloggerx
```

### ✅ 주요 기능

- `colorlog` 기반 콘솔 출력 (rich 대체)
- JSON 파일 핸들러 (logrotate 완전 호환)
- `trace_id`, `user_id`, `tag` 등 context 필드 자동 전달
- `sys.excepthook` 및 asyncio 예외 자동 캡처
- FastAPI + Docker + systemd 환경에서 안정 작동

---

## 💡 실무 적용 예시

```python
from traceloggerx import get_logger

logger = get_logger(
    name="scheduler",
    trace_id="abc123",
    user_id="u456",
    tag="order-check"
)

logger.error("주문 확인 실패", extra={"order_id": "A-1291"})
```

이 구조 덕분에 다음이 가능해졌다:

- 로그에 담긴 `trace_id`, `user_id`, `tag`를 기반으로 Slack 알림에 **유의미한 정보** 전달
- 에러 발생 시 **Slack 실시간 알림 연동**  
- 동일 `trace_id` 기준으로 전체 요청 흐름 분석 가능
- JSON 로그 기반으로 검색, 필터링, 통계 분석 등 후처리 작업이 **매우 용이**

---

## 🔄 PyPI 배포로 확장

처음엔 나만 쓰려 했지만,  
지금은 `trace_insight`, `slack-bot-server` 등 여러 사내 프로젝트에 도입되어 운영되고 있다.

- 📦 [traceloggerx on PyPI](https://pypi.org/project/traceloggerx)
- 🔗 [GitHub Repository](https://github.com/darams4863/traceloggerx)

---

## 🧩 정리하며

예쁜 로그는 시선을 끌지만,  
**운영에서는 문제를 잡아주는 로그가 더 중요하다.**

서버가 터졌을 때, 로그가 말이 안 통하면  
디버깅은 지옥이 된다.

그래서 나는 `rich`를 걷어내고,  
나만의 실전 로깅 유틸리티를 만들었다.

> 로그는 개발자의 블랙박스다.  
> 신뢰할 수 있는, 내가 필요한 데이터를 담아 직접 만든다.
