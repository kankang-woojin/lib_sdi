전국 사회적박탈지수 - 도서관 대출 회귀분석을 위한 데이터 준비
=============
### 데이터 범위
	2014년-2018년, 전국 시군구 229개 지역
	시군구 중 구 단위의 경우 자치구(특별시, 광역시 산하의 행정구)만 적용, 비자치구는 소속 시단위로 변환하였음 (ex, 수원시 장안구 -> 수원시)

### 데이터 리스트
1. 독립변인(사회적 박탈지수)
	0) 변경사항
		이전 연구에서 사용한 EQ-5D 데이터의 경우, 전국단위의 PCA를 수행한 결과, 재현율이 낮게 나와 지표를 변경하였음
		경제, 교육, 건강과 더불어 주거 관련 지표를 추가하였음
	1) 국민연금공단_자격 시구신고 평균소득월액 = 경제 (2014-2018) 
		출처: https://www.data.go.kr/data/3046077/fileData.do 공공데이터 포털: 국민연금공단
	2) 성, 연령 및 교육정도별 인구(6세이상,내국인)-시군구 = 교육 (2015)
		출처: https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=DT_1PM1501&conn_path=I2 통계청
	3) 대사증후군 = 건강 (2014-2018) 
		출처: https://kosis.kr/statHtml/statHtml.do?orgId=350&tblId=DT_35007_N135&conn_path=I2 통계청
	4) 주거시설 형태별 가구 = 주거 (2015)
		출처: https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=DT_1PE1506&conn_path=I2 통계청
	5) 사회적 박탈지수(SDI, Social Deprivation Index)
		평균소득월액(경제), 교육정도별 인구(교육), 대사증후군(건강), 주거시설가구(주거) 4가지 지표에 대하여, 4가지의 지표 모두 양의 상관관계를 가지도록 지표를 조정한 후, Standard Scaling 후 PCA를 실행함
		PCA의 First Component만 이용하며, 교육, 건강, 주거, 경제 사정이 좋아질 수록 사회경제적 박탈지수가 낮아지는 구조로 -1을 곱하도록 함 
		
2. 통제 변인
	0) 변경사항
		버스정류장의 경우, 전국단위로 확장하는 과정에서 일부 시군구가 불규칙하게 누락되어, 접근성을 판단하는 버스정류장 데이터 대신 다른 접근성 데이터로 변환함
	1) 인구정태: 주민등록연앙인구 = 총인구, 여성인구 (2014-2018)
		출처: https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=DT_1B040M5&conn_path=I2 통계청
	2) 인구동태: 인위인구증가율
		시군구별 이동자수 (2014-2018)
			출처: https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=DT_1B26001_A01&conn_path=I2 통계청
		인위인구증가율 수식 = (총전입자수 - 총전출자수) / (인구 / 1000) 
	3) 인구동태: 자연인구증가율
		출생자수 (2014-2018)
			출처: https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=INH_1B81A01&conn_path=I2 통계청
		사망자수 (2014-2018)
			출처: https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=INH_1B82A01&conn_path=I2 통계청
		자연인구증가율 수식 = (출생자수 - 사망자수) / (인구 / 1000) 
	4) 인구 10만명 당 문화시설 수 (2014-2018)
		문화기반시설: 「도서관법」상 도서관, 「박물관 및 미술관 진흥법」상 박물관, 미술관, 문예회관(종전 「문화예술진흥법」상 문화예술회관) 및 「지방문화원진흥법」상 지방문화원, 문화의 집
		출처: https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=DT_1YL20931&conn_path=I2 통계청
	5) 재정자립도 (2014-2018)
		출처:  https://kosis.kr/statHtml/statHtml.do?orgId=101&tblId=DT_1YL20921&conn_path=I2 통계청
	6) 교통접근성 (2017)
		교육-초등학교, 교육-중학교, 교육-고등학교, 의료-공공의료시설, 의료-병·의원, 의료-종합병원, 판매-대규모점포, 판매-전통시장 15분이내 접근 가능한 시군구 인구 비율
		출처: https://www.ktdb.go.kr/www/selectTrnsportTreeView.do?key=32 국가교통DB
	7) 면적 (2014-2018)
		행정구역 면적 및 공원 면적
		출처: https://kosis.kr/statHtml/statHtml.do?orgId=116&tblId=DT_MLTM_2300&conn_path=I2
	8) 공공도서관 장서수, 도서관 연식, 면적, 예산, 직원 정규직 수 (2014-2018)
		출처: https://www.libsta.go.kr/ 문화체육관광부:국가도서관통계시스템

3. 종속 변인
	1) 도서관 총대출권수, 분야별 대출권수 (2014-2018)
		출처: https://www.libsta.go.kr/ 문화체육관광부:국가도서관통계시스템

### 데이터 생성 과정
1. 독립 변인 데이터 수집 (경제, 보건, 교육, 주거)
	데이터 수집 -> 데이터 검토 -> 데이터 선정
2. 사회적 박탈 지수 생성
	경제, 보건, 교육 데이터를 standard scale한 뒤 PCA 실행 (61~66% 재현율)
3.  종속, 통제 변인 수집
	데이터 시군구 매칭 후 데이터 매칭을 위해 행정동 법정동 코드사용 
	시군구 면적, 연앙인구, 도서관 데이터 수집

### 최상위 폴더 설명
1. data 폴더 = 데이터 저장 파일
2. ipynb 폴더 = python jupyter ipynb 파일 (순서별로 숫자로 라벨링)
3. img 폴더 = 데이터 생성과정 시각화 파일 

### 회귀 데이터 세트 위한 data 폴더 설명
1.  /data/0/shp/ , 데이터 전체 과정(시군구 매칭, 전처리 및 시각화)에서 활용한 시군구 SHP 데이터
2. /data/1/result/, 사회적박탈지수 데이터(독립변인) 폴더
3. /data/2/result/, 지역사회 통제 변인 데이터 폴더
4. /data/3/result/, 도서관 통제 변인 및 종속 변인 데이터 폴더
5. /data/4/result/, 최종 데이터 세트 폴더

### 최종 데이터 세트
1. /data/4/result/dataset.xlsx , 시군구별 2014-2018 데이터
2. /data/3/result/_library_info.csv , 2014-2018 각 도서관의 정보 데이터

### 시각화
1. /img/1/histplot/, 데이터 분포 결과 시각화
2. /img/1/pca/, PCA 누적설명률 시각화
3. /img/1/sdi/, 사회경제적 박탈지수 전국 단위 시각화 (빨갈 수록 박탈지수가 높은것, 파랄 수록 박탈지수가 낮음)