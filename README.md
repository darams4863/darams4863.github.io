# darams4863 개발 블로그

이 저장소는 [Minimal Mistakes Jekyll 테마](https://mmistakes.github.io/minimal-mistakes/) 기반의 개인 기술 블로그입니다.  
백엔드 개발자로서의 성장 과정, 실무 문제 해결 경험, 포트폴리오 프로젝트를 정리하며  
장기적으로 지식 기반을 축적하고 공유하기 위해 운영 중입니다.

## 소개

- **저자:** 다람스 (darams4863)
- **목적:** 실무 중심 기술 정리, 성장 기록, 포트폴리오 관리
- **핵심 기술:** Python, FastAPI, PostgreSQL, 로깅 시스템, DevOps, AWS

## 커스텀 사항 요약

- **로고/프로필 이미지**: `assets/images/` 폴더 내 이미지 커스텀
- **사이드바/배경**: 프로필 배경 및 배너 이미지 적용
- **네비게이션 메뉴**: `_data/navigation.yml` 구성
- **소개 페이지**: `_pages/about.md`에서 자기소개 정리
- **푸터 문구**: `_includes/footer/custom.html`에서 수정 가능

## 로컬 개발 환경

1. **필수 설치**
   - Ruby (권장: 2.7 ~ 3.2)
   - Bundler

2. **설치 및 실행**
   ```bash
   bundle install
   bundle exec jekyll serve --livereload
   ```
   → [http://localhost:4000](http://localhost:4000) 접속

## GitHub Pages 배포

- 변경사항 푸시 시 자동으로 GitHub Pages를 통해 배포됨
- 테마는 remote_theme 방식 사용

## 주요 디렉토리

| 경로                        | 설명                    |
|---------------------------|-----------------------|
| `_config.yml`             | 사이트 전체 설정        |
| `_pages/about.md`         | 소개 페이지             |
| `_data/navigation.yml`    | 상단 메뉴 구성          |
| `assets/images/`          | 이미지 리소스           |
| `_includes/footer/`       | 하단 커스텀 영역         |
| `_sass/`, `assets/css/`   | 스타일 및 테마 커스텀     |

## 라이선스

- Minimal Mistakes 테마 및 본 저장소는 MIT 라이선스를 따릅니다.

---

### 문의

- Email: dev.daram4863@gmail.com
