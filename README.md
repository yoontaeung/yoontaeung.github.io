# Taeung Yoon — Personal website

사이트: [yoontaeung.github.io](https://yoontaeung.github.io/)

Jekyll로 빌드하고 GitHub Pages에서 배포합니다.

## 수정할 파일

| 파일 | 역할 |
| --- | --- |
| [`_pages/about.md`](_pages/about.md) | 현재 홈페이지 전체 내용: bio, 연구 관심사, 논문, 뉴스, 서비스, 강의 |
| [`_layouts/researcher.html`](_layouts/researcher.html) | 페이지 구조, 메뉴, footer |
| [`assets/css/researcher.css`](assets/css/researcher.css) | 글꼴, 여백, 색상, 모바일 배치 |
| [`_config.yml`](_config.yml) | 이름, 이메일, Scholar·GitHub 링크, 대표 사진, 사이트 설정 |
| [`images/photo.jpg`](images/photo.jpg) | 왼쪽 프로필 사진 |
| [`images/institutions/`](images/institutions/) | 학교 로고와 출처 |
| [`_pages/404.md`](_pages/404.md) | 없는 주소를 방문했을 때 표시할 페이지 |

**홈페이지 내용을 바꿀 때는 `_pages/about.md`를 편집합니다.**
최종 `index.html`은 Jekyll이 이 파일과 레이아웃을 조합해 생성합니다.

## 로컬에서 확인

Ruby 3.3 이상과 Bundler가 설치된 환경에서 실행합니다.

```sh
bundle install
bundle exec jekyll serve --livereload
```

미리보기 주소: <http://localhost:4000>
빌드 결과는 `_site/`에 생성됩니다.

## 배포

`master` 브랜치에 push하면 GitHub Pages가 자동으로 빌드·배포합니다.
[Actions](https://github.com/yoontaeung/yoontaeung.github.io/actions)에서 결과를 확인할 수 있습니다.

임시 파일과 빌드 결과는 `.gitignore`와 Jekyll 제외 설정으로 관리합니다.
