## Kakao arena 2nd Competition
# "브런치 사용자를 위한 글 추천 대회"
### brunch 데이터를 활용해 사용자의 취향에 맞는 글을 예측하는 대회
* 공식 홈페이지: https://arena.kakao.com/c/2
* 베이스 코드: https://github.com/kakao-arena/brunch-article-recommendation

### BrunchRec 
* designed by **datartist**
* 깃헙 주소: https://github.com/jihoo-kim/BrunchRec  

## 1. 간단한 설명
### 1. 개요
* user의 article 소비 경향을 반영하여 추천

### 2. 추천 방식
#### 1) collaborative filtering 추천
* item based CF (아이템 기반 협업필터링)
* *ncsu(not_cold_start_users)*: 일정 기간(2019.02.15 ~ 2019.02.28) 동안 읽은 article의 수가 평균보다 높은 users
* *nlti(not_long_tail_items)*: 일정 기간(2019.02.15 ~ 2019.02.28) 동안 view가 상위 5%인 article
* **Step 1**: *ncsu*와 *nlti*에 대해서만 item-user matrix 생성
* **Step 2**: item-user martix에서 item에 대해 cosine similarity 구하기
* **Step 3**: 가장 비슷한 100개의 item의 weighted mean을 이용해 predict
* **Step 4**: 각 user에 대해 weighted mean이 높은 상위 100개 article을 저장

#### 2) popularity based 추천
* 파레토 법칙(Pareto Principle): 전체 결과의 80%가 전체 원인의 20%에서 일어나는 현상
* 인기 많은(=조회수 높은) 상위 20%의 article들이 많이 소비되는 경향이 있음
* popularity_based_recommend: 일정 기간(2019.02.15 ~ 2019.02.28) 동안 조회수가 높은 인기 글을 추천
* popularity_based_recommend2: 전체 기간(처음 ~ 2019.02.28) 동안 조회수가 높은 인기 글을 추천

#### 3) following based 추천
* 브런치 플랫폼 특성상 구독하는 작가의 글을 많이 읽는 경향이 있음
* 전체 user의 98%가 구독하는 작가가 있음 (평균 8.6명)
* 일정 기간(2019.02.15 ~ 2019.02.28) 동안 읽은 글 중에서 구독 작가별 빈도수를 저장 ('recent_following')
* 전체 기간(처음 ~ 2019.02.28) 동안 읽은 글 중에서 구독 작가별 빈도수를 저장 ('read_following')
* following_based_recommend: 읽은 글 중에서 구독작가 글의 비율을 고려하여 추천 (비율이 클수록 많이 추천)
* following_based_recommend2: 구독하는 작가의 글을 추천 (읽지 않아서 추천되지 않는 경우에 대비)

#### 4) magazine based 추천
* 같은 매거진의 글을 많이 읽는 경향이 있음
* 일정 기간(2019.02.15 ~ 2019.02.28) 동안 읽은 글 중에서 매거진별 빈도수를 저장 ('recent_magazine')
* 전체 기간(처음 ~ 2019.02.28) 동안 읽은 글 중에서 매거진별 빈도수를 저장 ('read_magazine')
* magazine_based_recommend: 읽은 글 중에서 매거진 글의 비율을 고려하여 추천

#### 5) tag based 추천
* 읽은 글들의 태그와 같은 태그를 가진 글을 읽는 경향이 있음
* 읽은 글들의 태그는 user의 관심 키워드라고 볼 수 있음
* 일정 기간(2019.02.15 ~ 2019.02.28) 동안 읽은 글의 빈도수 상위 6개를 user의 관심 키워드로 저장 ('recent_interest')
* 전체 기간(처음 ~ 2019.02.28) 동안 읽은 글의 빈도수 상위 6개를 user의 관심 키워드로 저장 ('read_interest')
* tag_based_recommend: 읽은 글들에서 자주 나오는 태그를 고려하여 추천 (관심 키워드와 2개 이상 겹치는 경우)


## 3. 추천 알고리즘
* hybrid_recommned: 사용자의 소비 경향을 반영하여 종합적으로 추천
* recent 모드: 최근 읽은 글이 많을 경우, 최근 소비 경향을 반영
* read 모드: 최근 읽은 글이 적을 경우, 전체 소비 경향을 반영
#### 7) recommender
* 시간 단축을 위해 metadata의 전체가 아닌 일부만 탐색
* metadata_all -> 처음 ~ 2019.03.14 동안 발행된 글을 제외한 metadata
* metadata_reg -> 2018.09.15 ~ 2019.03.14 동안 발행된 글의 metadata
* metadata_pop -> 일정 기간 동안 view가 상위 20%인 글의 metadata
* metadata_hot -> 추천 기간 동안 발행되었고, view가 상위 20%인 글의 metadata

## 4. 재현 방법
### Step 1. 데이터 및 소스 코드 다운로드
#### 1) res 폴더에 들어갈 파일 다운로드
* [카카오 아레나 브런치 데이터셋](https://arena.kakao.com/c/2/data)
* magazine.json, metadata.json, users.json, predict.tar, read.tar 다운로드
* predict.tar, read.tar은 res 폴더에 아래의 그림과 같이 압축 해제
* contents 폴더의 data.0 ~ data.6은 다운로드할 필요 없음
#### 2) pickle 폴더에 들어갈 파일 다운로드
* 중간 데이터 파일 (시간 단축)
* [dev.users용](https://drive.google.com/file/d/16IZSKdK8sFzeHx0Z07oH3HcbbYQZAkQJ/view?usp=sharing)
* [test.users용](https://drive.google.com/file/d/1dbptjhjp7mduPnhchTfuRQe7wTNtgHfz/view?usp=sharing)
#### 3) BrunchRec.ipynb 파일 다운로드
* 소스 코드 파일 (Jupyter Notebook)
* [BrunchRec.ipynb](https://github.com/jihoo-kim/BrunchRec/archive/master.zip)

![dir_small](https://user-images.githubusercontent.com/50820635/61771159-01965680-ae2a-11e9-960f-162638459a3d.jpg)

***

### Step 2. 소스 코드 실행 및 결과 파일 확인
#### 1) Jupyter Notebook으로 BrunchRec.ipynb 파일 열기
* Jupyter Notebook이 없을 경우 [설치](https://jupyter.org/install)
#### 2) 상단 메뉴에서 'Kernel' -> 'Restart & Run All' 클릭
* 기본값은 test.users로 설정되어 있음
* dev.users에 대해 추천을 하고 싶다면, 소스 코드 하단에 주석 처리 변경
#### 3) 결과 파일 'recommend.txt' 생성 확인
* 기본값은 동일 디렉터리에 결과 파일 생성
* 다른 곳에 결과 파일을 생성하고 싶다면, recommender 함수의 마지막 입력값에 경로 입력
