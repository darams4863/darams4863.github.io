---
title: "도커 기초와 Redis 실습 환경 구축"
date: 2025-07-29
categories:
  - docker
tags:
  - docker
  - redis
  - container
  - virtualization
---

# 도커(Docker)란?
- 도커는 애플리케이션을 컨테이너로 패키징하여 배포하고 실행할 수 있게 해주는 플랫폼입니다.

- 도커에는 핵심 개념으로 `이미지`와 `컨테이너`가 나오는데:
  -	이미지(Image): 실행 가능한 패키지 (설치된 소프트웨어 + 설정)
  -	컨테이너(Container): 이미지를 실행한 상태

- 이때 등장하는 개념이 또 `OS 가상화`이다
- OS 가상화는 하나의 운영체제(OS) 위에서 여러 개의 독립적인 실행 환경을 만드는 기술이다. 


## 도커의 핵심 개념

### 1. 컨테이너(Container)
- **가상화 기술**: 가상머신과 달리 OS를 포함하지 않고, 애플리케이션과 필요한 라이브러리만 포함
- **격리된 환경**: 각 컨테이너는 독립적인 환경에서 실행
- **가벼움**: 가상머신보다 훨씬 빠르게 시작되고 적은 리소스 사용

### 2. 이미지(Image)
- **템플릿**: 컨테이너를 생성하기 위한 읽기 전용 템플릿
- **레이어 구조**: 여러 레이어로 구성되어 효율적인 저장과 공유 가능
- **재사용성**: 한 번 만든 이미지로 여러 컨테이너 생성 가능

### 3. 도커 허브(Docker Hub)
- **이미지 저장소**: 공개/비공개 이미지를 저장하고 공유하는 플랫폼
- **오픈소스**: 많은 공식 이미지들이 무료로 제공됨

## 도커 설치 확인

```bash
# 도커 버전 확인
docker --version

# 도커 정보 확인
docker info
```

## 기본 도커 명령어

### 1. 이미지 관련
```bash
# 이미지 검색
docker search redis

# 이미지 다운로드
docker pull redis

# 로컬 이미지 목록 확인
docker images

# 이미지 삭제
docker rmi redis
```

### 2. 컨테이너 관련
```bash
# 컨테이너 실행
docker run redis

# 백그라운드에서 실행 (-d 옵션)
docker run -d redis

# 포트 매핑 (-p 옵션)
docker run -d -p 6379:6379 redis

# 컨테이너 이름 지정 (--name 옵션)
docker run -d --name my-redis -p 6379:6379 redis

# 실행 중인 컨테이너 목록
docker ps

# 모든 컨테이너 목록 (중지된 것 포함)
docker ps -a

# 컨테이너 중지
docker stop my-redis

# 컨테이너 시작
docker start my-redis

# 컨테이너 재시작
docker restart my-redis

# 컨테이너 삭제
docker rm my-redis

# 실행 중인 컨테이너에 접속
docker exec -it my-redis bash

# 컨테이너 로그 확인
docker logs my-redis
```

## Redis 실습 환경 구축

### 1. Redis 컨테이너 실행
```bash
# Redis 컨테이너를 백그라운드에서 실행
docker run -d --name redis-server -p 6379:6379 redis:latest
```

### 2. Redis 클라이언트로 연결
```bash
# Redis CLI로 직접 연결
docker exec -it redis-server redis-cli

# 또는 로컬에서 연결 (포트 매핑 후)
redis-cli -h localhost -p 6379
```

### 3. 기본 Redis 명령어 테스트
```bash
# Redis CLI에서 실행
127.0.0.1:6379> SET mykey "Hello Redis"
OK
127.0.0.1:6379> GET mykey
"Hello Redis"
127.0.0.1:6379> DEL mykey
(integer) 1
127.0.0.1:6379> EXIT
```

## 도커 컴포즈(Docker Compose) 사용

여러 서비스를 함께 관리할 때 유용합니다.

### docker-compose.yml 예시
```yaml
version: '3.8'
services:
  redis:
    image: redis:latest
    container_name: redis-server
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    restart: unless-stopped

volumes:
  redis_data:
```

### 컴포즈 명령어
```bash
# 서비스 시작
docker-compose up -d

# 서비스 중지
docker-compose down

# 로그 확인
docker-compose logs redis
```

## 실습 환경 정리

```bash
# 컨테이너 중지 및 삭제
docker stop redis-server
docker rm redis-server

# 이미지 삭제 (선택사항)
docker rmi redis:latest

# 사용하지 않는 리소스 정리
docker system prune
```

## 도커의 장점

1. **일관성**: 개발, 테스트, 운영 환경의 일관성 보장
2. **이식성**: 어떤 환경에서든 동일하게 실행
3. **격리**: 애플리케이션 간 충돌 방지
4. **확장성**: 마이크로서비스 아키텍처에 적합
5. **효율성**: 리소스 사용량 최적화

이제 도커를 사용해서 Redis 실습 환경을 쉽게 구축하고 관리할 수 있습니다! 