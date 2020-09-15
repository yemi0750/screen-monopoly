# screen-monopoly
2018-2 KHU Python 텀프로젝트 : 대기업의 배급과 상영 겸업, 스크린 독과점 분석
<br><br>

### 가설 정의
#### "대기업의 배급과 상영 겸업, 스크린 독과점에 영향을 줄 것이다."<br>
기업의 목적은 이윤추구이다. 대기업이 영화의 배급과 상영을 겸업할 경우, 더 큰 이익 창출을 목적으로 자사영화에 스크린을 더 배정할 가능성이 있다. ``배급과 상영을 겸업하는 대기업이 제작한 영화의 스크린 점유율``을 분석하고, 이를 ``평균 스크린 점유율``과 비교하여 대기업의 수직계열화가 스크린 쏠림 현상에 영향을 주는지에 대한 분석 결과를 도출할 것이다. 만일 이러한 가설이 사실일 경우, 영화계에서 문제가 되고 있는 스크린 독과점 현상에 대한 개정안이 사실에 입각한 타당한 제안임을 증명할 수 있다.
<br><br>

### 데이터 획득
- 공공데이터포털, 영화진흥위원회 제공 OPEN API<br>
1) 일일 박스오피스 : 일별 영화명, 영화코드, 스크린 수, 누적관객수<br>
https://www.data.go.kr/search/index.do?index=OPENAPI&query=%EC%98%81%ED%99%94%EC%A7%84%ED%9D%A5%EC%9C%84%EC%9B%90%ED%9A%8C&currentPage=1&countPerPage=10 <br>
http://www.kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.xml

- 영화진흥위원회 웹페이지 크롤링<br>
2) 영화 검색 화면 : 해당 영화의 배급사 이름<br>
http://www.kobis.or.kr/kobis/business/mast/mvie/searchMovieDtl.do
http://www.kobis.or.kr/kobis/business/mast/mvie/searchMovieList.do
<br>-> 영화명 검색 - 영화제목 클릭시 뜨는 팝업창 - 영화사 - 배급사
<br><br>

### 분석을 위한 데이터의 가공
##### 1) 영화진흥위원회 오픈 API
REST 방식. 일별 박스오피스 자료 호출 (응답방식 : XML)<br>
- 발급받은 키, 조회일자(yyyymmdd형식)를 파라미터로 rest요청
- beautiful soup 모듈을 이용하여 데이터 가져옴
- 영화명, 영화코드, 해당일자 스크린 수, 누적 관객 수 정보 추출
- 1. 영화명, 영화코드를 각각 movienm, moviecd 리스트에 저장
- 2. 해당일자 스크린 수를 이용하여 영화별 <br>최대 스크린 수(더 큰 스크린 수 정보가 들어오면 갱신), <br>평균 스크린 점유율(스크린 점유율의 평균을 계산), <br>누적 스크린 수 정보(전일 스크린 수와 합산)<br>로 재구성 후 각각 b_screennum, a_sh, t_screennum 리스트에 저장
- 3. 누적 관객수는 갱신하여 t_audiencenum 리스트에 저장

##### 2) 영화진흥위원회 웹페이지 크롤링
POST 방식. 1에서 추출한 영화코드를 파라미터로, 배급사 정보를 가진 url에 접근<br>
- 영화 정보를 불러올 url에 영화코드와 타입을 파라미터로 접근
- beauriful soup 모듈을 이용하여 데이터 가져옴
- 1. div태그_class='moreArea'로 거시적 검색 구역 지정
- 2. li태그_class='companyDtl1'의 내용이 '배급사'인 경우, <br>li태그_class='companyDtl2'의 내용으로 배급사 이름 추출<br>(배급사 정보가 없는 경우 예외처리)
- 배급사 정보를 companynm 리스트에 저장

##### 3) 각각의 리스트에 저장된 정보를 csv파일로 저장
- 영화코드, 영화명, 제작사명, 최고 스크린수, 평균 스크린 점유율, 누적 스크린 수, 누적 관객 수 리스트를 column로 가지는 csv 생성
- 파일로 저장
<br><br>

### 차트로 시각화
