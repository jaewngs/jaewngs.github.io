---



title:  "Bigdata intro & HTML 기초"



layout: post



---


## [오늘 할 내용]
1. 빅데이터의 특성
2. Web 시작하기
3. HTML 기초

***

## 빅데이터
### 빅데이터의 특성이라 불리는 3V를 실제 빅데이터의 모습에서 확인한다.
- 대용량(Volume), 빠르기(Velocity), 다양성(Variety)

### 정보와 데이터
- 데이터를 믿고 분석에 사용할 수 있는가? (정보 유용성 점검)
	- 입수 가능한가?(데이터를 공개하는 SNS만 가능)
	- 다수 의견으로 보기에 충분한가?(대선이나 총선 규모에서는 어느 정도 충분하지만, 지역구 수준에서는 부족)
	- 보편적인가?(SNS 사용자는 특정 나이대, 성향에 편중)
	- 뻔하지 않고 원하는 수준의 내용인가?(숨겨진 의미가 드러나면 더 이상 신비롭지 않다.)
	- 분석으로 정확한 정보가 나오는가?(긍정, 부정 구분 조차도 정확하지 않다.)

### 비정형 데이터와 정보
- 비정형 데이터는 분석 목적에 얼마나 의미가 잇는가?
	 - 메타 데이터 > 연관 데이터 > 추정 데이터

### 빅데이터 분석의 시대적 흐름
- Gartner에서 발표하는 10대 전략 기술 동향에서 빅데이터의 위치, 중요성을 파악한다.
	- 빅데이터는 2010년대 초반 급부상 후 점차 사라짐
	- 주요 기술은 이미 빅데이터와 연관, 융합되어 있음

### 빅데이터 활용 관점의 동향
- 빅데이터 분석 활용 방향이 어떻게 변화하고 있는지 파악한다.
	- 빅데이터를 분석해서 어떻게 보여주는가
		- 일반 차트
		- 통계 전문 차트
		- 시각화 차트(그래픽)
		- 리포트 -> 대시보드
	- 빅데이터 분석으로 어떤 비즈니스를 하는가
		- 기업에서의 비즈니스 변화(데이터 + 알고리즘)
		- 비즈니스 체계의 변화(시장경제 vs 계획경제 by 마윈)

### 공공데이터 개방 동향
- 공공데이터 개방이 추구하는 가치를 알아보고, 어떤 방향으로 나아가야 하는지 생각해 본다.

### 실제 기업에서 원하는 분석
- 어떤 분석이 필요한지 기업 입장에서 생각한다.
	- 통계 분석 vs 단순한 리포트
		- 통계 분석 : ex) 지점 간 매출 실적에 차이가 있는지 여부에 대한 통계적인 유의성 검정
		- 단순한 리포트 : ex) 지점별 전년 동월 대비 매출액 증가율 리포트
		- 분석 자체 보다는 목표달성을 위해 조직을 움직이게 하는 목적
	- 데이터 마이닝 vs 시각화
	- 온라인 분석

### 개념적인 분석 기법 리뷰
- CRM, 데이터베이스 마케팅에서의 고객분석에서 알아야 할 것은 무엇인가?
	- 고객의 가치(과거, 현재, 잠재 가치)
	- 우수고객의 의미
	- 고객 세분화
	- 고객등급의 변화

***

### 데이터 분석 방법의 구분
- 데이터 분석 방법들을 구분 지어서 특징을 파악한다.
	- 데이터 의미 표현을 위한 분석(OLAP, Reporting, 시각화)
	- 추측과 의미 파악을 위한 분석(통계, 마이닝, 기계학습)
	- 의사결정에 직접 활용하기 위한 분석(최적화, 예측, 시뮬레이션)

### OLAP과 Reporting
- OLAP분석 기능과 다차원 분석 개념을 익힌다.
	- 동적인 OLAP(On-Line Analytical Processing)
		- 다차원 비정형 분석
		- Alert 분석(Highlighting)
		- Drill 분석
			- Drill Up/Down
			- Drill Across
			- Drill to Detail
		- 필터링 분석
		- 다중합계 분석
		- 상하위 분석
		- 서브데이터 집합 분석
	- 정적인 Reporting

### 대시보드(d3.js가 가장 예쁘게 나옴)
- 대시보드의 목적과 대시보드로 분석하는 방법을 아아본다.
	- 종합적인 정보 조회
	- 이종 데이터 소스로부터 구성
	- D3.js차트가 가장 예쁘게 나옴
	- 대시보드 분석
		- 필터링 분석
		- 공통 필터, 마스터 필터, 범위 필터
		- 추적분석(Tracking Analysis), 스토리(Story) 분석

### 시각화
- 전통적 차트와는 다른, 시각화 그래픽을 이용하는 이유를 이해한다.
	- 미시 시각화(Micro Visualization)
		- 탐색(Discovery) 중심
		- 빅데이터의 세부 부분을 여러 관점에서 분석하기 위해 다양한 차트를 조합, 연계하여 탐색
	- 거시 시각화(Macro Visualization)
		- 직관적인 표현(Presenatation) 중심
		- 빅데이터의 전체적인 의미를 직관적으로 인지시키기 위해 적합한 유형의 그래픽을 이용

***

### 분석 Workflow
- 실제 적용 시 알아야 할 전체 작업 구조를 파악한다.
- Workflow
	- 데이터 입력
	- 데이터 전처리
	- 분석 모델 전제사항 검증
	- 분석 모델 적용
	- 분석 모델 평가 -> 예측 모델 등록
	- 분석 결과 데이터 출력

- 교과서에서 배우는 분석 모델은 전체 과정 중 일부
- 실제로 적용하기 위해서는 입력부터 출력까지 모든 단계를 거쳐야 됨
- 이론 뿐만 아니라 각 단계별 사용법과 명령어를 알아야 함
- 작업 내용의 재활요도 중요

***

## HTML

1. 정적 페이지 연동을 구현할 수 있다.
2. html 문서를 이용하여 브라우저에서 생성된 코드를 이용하여 탐색할 수 있다.
3. html 엘리먼트와 속성값을 www.w3c.org에서 확인 할 수 있다.

- html = head[title] + body
- body (hn, font, div, br, pre...) = .css(tag, id, class)
- html, 문단 태그, 목록 태그
- a, img, table, form

~~~ html
index.html --> <LINK rel="Index" href="index.html">
test01.html --> <LINK rel="Next" href="Res.jsp">
Res.jsp --> <LINK rel="Prev" href="test01.html">
~~~

ex) index.html 찾기 또는 검색어 창에 찾을 단어를 입력하게 되면
	- index.html -> test01.html -> Res.jsp

ex) 오라클 사이트에서 단어 검색하게 되면 해당 사이트만 보여준다.
	- 메일 > java 검색 > java 들어있는 메일 추출하고 상단 오른쪽에 > 앞으로 | 목록 | 뒤로

***

### 클라이언트에서 서버에 페이지 요청[<**a**>, <**link**>, <**form action**>]
- 폼 데이터 전송시[submit] get, post 방식이 발생(데이터 사이즈, 보안)
- get
	- 페이지 요청시에 쿼리스트링을 통해서 값 전달
	- 주소창에 132자까지만 들어갈 수 있음
	- ex) https://search.naver.com/search.naver?sm=top_hty&fbm=0&ie=utf8&query=daum
	-  name = sm, value = top_hyt
	-  name = fbm value = 0
- post
	- 폼의 데이터를 이진화로 비공개형식으로 전체 전달

