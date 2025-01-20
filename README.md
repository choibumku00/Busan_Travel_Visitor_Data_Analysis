# Busan_Travel_Visitor_Data_Analysis
데이터 출처: 국내 여행로그 데이터(동부권) (2023) (AIHub)  
https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&dataSetSn=71778


## 사용 파일
![image](https://github.com/user-attachments/assets/89b2978a-ce11-4c48-9257-02bd80ebeef2)  
사진 데이터 때문에 용량이 커서, Other 데이터와 training – 라벨링데이터만 다운로드  
Other: POI의 정보가 담겨져 있지만, 모든 데이터가 담겨있지는 않아 사용하지 않음  
training – 라벨링데이터: 사용자 정보, 이동 경로, 이동 수단, 결제 내역, 숙박비용 등등 여행과 관련된 데이터가 포함  
폴더는 크게 TL_csv와 TL_gps_data가 존재  
먼저 TL_gps_data는 각 사용자별로 파일이 만들어져 있으며, 각 사용자별 이동 기기의 GPS 데이터가 담겨져있다.  
 

![image](https://github.com/user-attachments/assets/8174810d-3e4a-49f5-a291-009ab7aad8f9)
GPS의 원본 데이터(TL_gps_data)는 왼쪽 사진과 같이 모든 이동 경로가 있지만, 오른쪽 사진 처럼 정제된 데이터는 TL_csv/tn_activity_his_활동내역_F.csv 파일에 나와있다.  
 
![데이터 테이블 스키마.png](https://github.com/user-attachments/assets/b261fc91-0822-4073-af2b-c267ba3ad514)  
[데이터 테이블 스키마.png]  
  
이동 경로 데이터로 TL_csv/tn_activity_his_활동내역_F.csv 파일을 사용  
사용자의 여행지가 어딘지 알기 위해 /TL_csv/tn_travel_여행_F.csv파일을 사용  
사용자의 정보를 받아오기 위해 TL_csv/tn_traveller_master_여행객 Master_F.csv 데이터를 사용  

---

## 데이터 분석
부산_방문지_정보_필터링_작업.ipynb 파일에 데이터 전처리와 분석 내용이 담겨 있음  
아래 과정은 부산_방문지_정보_필터링_작업.ipynb 내용과 동일  

### 데이터 전처리
-	/TL_csv/tn_travel_여행_F.csv를 통해 부산을 여행가는 사람만 필터링
--	이 데이터를 통해 /TL_csv/tn_visit_area_info_방문지정보_F.csv에 부산 여행을 가는 행만 남김
-	/TL_csv/tn_visit_area_info_방문지정보_F.csv를 통해 이동 과정인 ‘휴게소’는 제거
-	장소나 도로명 주소에 ‘부산’이라는 단어가 포함된 행만 추출
--		부산을 들렸다가 다른 지역으로 가는 경우도 있기 때문
-	시작 장소가 ‘집’인 경우가 대다수라 이런 행은 제외
-	각 사용자 별로 들린 장소가 1개라면 제외
--	위 단계에서 부산을 여행하는게 아닌 들린 장소인데 필터링 안된 경우를 제외하는 것
-	위치를 알 수 없는 장소라면 제외
-	이렇게 전처리된 파일을 부산_방문지_정보.csv로 저장

### 시각화 
-	장소는 알지만 위도, 경도를 알지 못하는 행 제외
-	연령별 관광객 수를 알기 위해 /TL_csv/tn_traveller_master_여행객 Master_F.csv를 사용
-	연령별 관광객의 이동 경로를 알기 위한 시각화
--	경로가 너무 많이 보여 아래 방식으로 시각화 함
-	연령별 관광객의 방문 장소를 알기 위한 시각화
-	연령별 관광 장소 클러스터링 시각화
-	성별 관광객의 방문 장소 시각화
-	월별 방문 장소 시각화
-	동부/서부 나누어 시각화(시청/부산항)
-	Poi 벡터 기준으로 유사도 top k개 출력

![image](https://github.com/user-attachments/assets/02f5396d-ddc2-4115-a12f-021a3e7adef8)  
[tourists_by_age_group.png]

![image](https://github.com/user-attachments/assets/e67cf72b-d055-45e2-862d-dc441a54c921)  

[age_group_travel_routes.html]  
오른쪽 상단 레이어를 클릭하여, 연령별로 경로를 필터링 가능  

![image](https://github.com/user-attachments/assets/4cb09d41-a125-47f6-ba59-4acc93763fcf)  
age_group_travel_locations.html  

![image](https://github.com/user-attachments/assets/1a5121cf-2efe-423e-b3b3-642f2d447d3d)

20대 클러스터링: 다양한 장소를 방문, 교통편(특히 지하철)이 있는 곳을 방문

![image](https://github.com/user-attachments/assets/b37cc307-b002-4f44-b352-979200788253)

30대 클러스터링: 20대와 달리 서부산과 일광에 클러스터링 생성 (서부산은 적음)

![image](https://github.com/user-attachments/assets/1b2288e2-f873-40bc-a853-274ef0faf515)

40대 클러스터링: 노포, 철마쪽에 클러스터링 생성, 부산대쪽 클러스터링이 없어짐

![image](https://github.com/user-attachments/assets/08d8f61b-62ac-4dbd-b4c2-f76f0322f60a)

50대 클러스터링: 철마와 정관에 새로운 클러스터링 발생, 서면, 덕천에 클러스터링 없어짐. 사하구도 매우 감소

![image](https://github.com/user-attachments/assets/34479502-5383-4d43-b446-8290c0d8d575)

60대 클러스터링: 다시 서면에 클러스터링 생성, 사람이 많은 장소에 주로 활동

---

![image](https://github.com/user-attachments/assets/8bebb953-cba9-4a29-aa52-cf4d2c4901af)

성별 관광지 방문지: 남자가 1447곳, 여자가 2616곳 방문했다. 조사된 여자 여행객은 1485명, 남자 여행객은 1075명이기 때문에 남자보다 여자가 여러 장소에 방문한다.

![image](https://github.com/user-attachments/assets/6a3b8145-90d9-47b7-bcc1-29feff853e06)
 
월별 방문지: 의미 있는 정보는 포착하지 못했다. 4~9월 데이터가 존재. 4월은 데이터 측정이 늦게 돼서 3일밖에 없음  

---

![image](https://github.com/user-attachments/assets/0af39e50-30ca-42f0-9464-016cb1cdae47)

시청 기준 동서 나누기:
시청 기준 동쪽 방문 지점 개수: 2368
시청 기준 서쪽 방문 지점 개수: 1695
 
![image](https://github.com/user-attachments/assets/21e74d31-623b-42b3-beae-42f02dc2a565)

부산항 기준 동서 나누기:
부산항 기준 동쪽 방문 지점 개수: 2974
부산항 기준 서쪽 방문 지점 개수: 1089
무슨 기준으로 해도 동부에 방문한 지점이 많다.

---

![image](https://github.com/user-attachments/assets/4cd067dd-429b-44f0-877c-5187bd080e4a)

POI 벡터별 유사도 top k개: POI * 사용자로 행렬을 만들어서 방문한 장소면 1을 추가

![image](https://github.com/user-attachments/assets/29fa49bc-6ecd-4ca5-b3ef-890925f03f67)

그림과 같이 방문자가 비슷한 경향이 있는 K개의 POI 출력
