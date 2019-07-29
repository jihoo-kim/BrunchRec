## Kakao arena 2nd Competition
# "브런치 사용자를 위한 글 추천 대회"
### brunch 데이터를 활용해 사용자의 취향에 맞는 글을 예측하는 대회
* 공식 홈페이지: https://arena.kakao.com/c/2
* 베이스 코드: https://github.com/kakao-arena/brunch-article-recommendation

### BrunchRec v9.0.1 
* designed by **datartist**
* 깃헙 주소: https://github.com/jihoo-kim/BrunchRec  

## [1] 모델 설명
### 1. 개요
* user의 article 소비 경향을 반영하여 추천 (최근 소비 경향, 전체 소비 경향)
* 다양한 추천 방식을 이용하여 여러 측면에서 추천 (5개의 추천 방식)
* 시간 단축을 위해 metadata의 일부만 탐색 (4개의 metadata)

***

### 2. 추천 방식
#### 1) collaborative filtering 추천
* *ncsu(not_cold_start_users)*: 일정 기간(2019.02.15 ~ 2019.02.28) 동안 읽은 article의 수가 평균보다 높은 users
* *nlti(not_long_tail_items)*: 일정 기간(2019.02.15 ~ 2019.02.28) 동안 view가 상위 5%인 articles
* Step 1: *ncsu*와 *nlti*에 대해서만 item-user matrix 생성
* Step 2: item-user martix에서 item에 대해 cosine similarity 구하기
* Step 3: 가장 비슷한 100개의 item의 weighted mean을 이용해 predict
* Step 4: 각 user에 대해 weighted mean이 높은 상위 100개 article을 저장
* **collaborative filtering**: 아이템 기반 협업필터링

#### 2) popularity based 추천
* 파레토 법칙(Pareto Principle): 전체 결과의 80%가 전체 원인의 20%에서 일어나는 현상
* 인기 많은(=조회수 높은) 상위 20%의 article들이 많이 소비되는 경향이 있음
* **popularity_based_recommend**: 일정 기간(2019.02.15 ~ 2019.02.28) 동안 조회수가 높은 인기 글을 추천
* **popularity_based_recommend2**: 전체 기간(처음 ~ 2019.02.28) 동안 조회수가 높은 인기 글을 추천

#### 3) following based 추천
* 브런치 플랫폼 특성상 구독하는 작가의 글을 많이 읽는 경향이 있음
* 전체 user의 98%가 구독하는 작가가 있음 (평균 8.6명)
* 일정 기간(2019.02.15 ~ 2019.02.28) 동안 읽은 글 중에서 구독 작가별 빈도수를 저장 ('recent_following')
* 전체 기간(처음 ~ 2019.02.28) 동안 읽은 글 중에서 구독 작가별 빈도수를 저장 ('read_following')
* **following_based_recommend**: 읽은 글 중에서 구독작가 글의 비율을 고려하여 추천
* **following_based_recommend2**: 구독하는 작가의 글을 추천 (읽지 않아서 추천되지 않는 경우에 대비)

#### 4) magazine based 추천
* 같은 매거진의 글을 많이 읽는 경향이 있음
* 일정 기간(2019.02.15 ~ 2019.02.28) 동안 읽은 글 중에서 매거진별 빈도수를 저장 ('recent_magazine')
* 전체 기간(처음 ~ 2019.02.28) 동안 읽은 글 중에서 매거진별 빈도수를 저장 ('read_magazine')
* **magazine_based_recommend**: 읽은 글 중에서 매거진 글의 비율을 고려하여 추천

#### 5) tag based 추천
* 읽은 글들의 태그와 같은 태그를 가진 글을 읽는 경향이 있음
* 읽은 글들의 태그는 user의 관심 키워드라고 볼 수 있음
* 일정 기간(2019.02.15 ~ 2019.02.28) 동안 읽은 글의 빈도수 상위 6개를 user의 관심 키워드로 저장 ('recent_interest')
* 전체 기간(처음 ~ 2019.02.28) 동안 읽은 글의 빈도수 상위 6개를 user의 관심 키워드로 저장 ('read_interest')
* **tag_based_recommend**: user의 관심 키워드와 common_num(=2개) 이상 겹치는 태그를 가진 글을 추천

***

### 3. 추천 알고리즘
#### 1) 최근 읽은 글의 수에 따라 맞춤형 추천
* recent 모드: 최근 읽은 글의 수가 상위 80%인 user들의 경우 -> 최근 소비(recent) 경향을 반영
* read 모드: 최근 읽은 글이 하위 20%인 user들의 경우 -> 전체 소비(read) 경향을 반영

#### 2) 시간 단축을 위해 metadata의 전체가 아닌 일부만 탐색
* metadata_all -> 추천 기간 이후에 발행된 글을 제외한 metadata (629,252개)
* metadata_reg -> 추천 기간을 포함한 최근 6개월 동안 발행된 글의 metadata (127,218개)
* metadata_pop -> 최근 view가 상위 20%인 글의 metadata (126,666개)
* metadata_hot -> 추천 기간 동안 발행되었고, 최근 view가 상위 20%인 글의 metadata (5,011개)

#### 3) 위의 5가지 추천 방식을 아래와 같은 순서로 추천
* **f1**: metadata_hot에 대해 **following_based_recommend**을 이용하여 'recent_view(최근 조회수)' 순으로 추천 
* **f2**: metadata_reg에 대해 **following_based_recommend**을 이용하여 'reg_ts(발행 시간)' 순으로 추천
* **f3**: metadata_hot에 대해 **following_based_recommend2**을 이용하여 'recent_view(최근 조회수)' 순으로 추천 
* **f4**: metadata_reg에 대해 **following_based_recommend2**을 이용하여 'reg_ts(발행 시간)' 순으로 추천
* **p1**: metadata_hot에 대해 **popularity_based_recommend**을 이용하여 추천
* **cf**: metadata_all에 대해 **collaborative filtering**을 이용하여 추천
* **m1**: metadata_hot에 대해 **magazine_based_recommend**을 이용하여 'recent_view(최근 조회수)'** 순으로 추천 
* **m2**: metadata_all에 대해 **magazine_based_recommend**을 이용하여 'reg_ts(발행 시간)' 순으로 추천
* **t**: metadata_hot에 대해 **tag_based_recommend**을 이용하여 'recent_view(최근 조회수)' 순으로 추천 
* **p2**: metadata에 대해 **popularity_based_recommend2**을 이용하여 추천 (100개가 되지 않았을 경우)

![recommend](https://user-images.githubusercontent.com/50820635/62028650-bd38fb00-b21b-11e9-9c53-fe80203d0000.JPG)


## [2] 재현 방법
### 1. 개발 환경 및 필요 라이브러리
#### 1) 개발 환경
* Windows 10, i7-9700 CPU, RAM 16.0GB, 64bit
* conda 4.7.5, Python 3.7.3, IPython 7.4.0, Jupyter Notebook 5.7.8
#### 2) 필요 라이브러리
* numpy 1.16.2
* pandas 0.24.2
* tqdm 4.31.1
* sklearn 0.20.3

***

### 2. 데이터 및 소스 코드 다운로드
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

### 3. 소스 코드 실행 및 결과 파일 확인
#### 1) Jupyter Notebook으로 BrunchRec.ipynb 파일 열기
* Jupyter Notebook이 없을 경우 [설치](https://jupyter.org/install)
#### 2) 상단 메뉴에서 'Kernel' -> 'Restart & Run All' 클릭
* 약 1시간 50분 소요
* 기본값은 test.users로 설정되어 있음
* dev.users에 대해 추천을 하고 싶다면, 소스 코드 하단에 주석 처리 변경
#### 3) 결과 파일 'recommend.txt' 생성 확인
* 기본값은 동일 디렉터리에 결과 파일 생성
* 다른 곳에 결과 파일을 생성하고 싶다면, recommender 함수의 마지막 입력값에 경로 입력

***

### 4. 추천 결과 확인
* recommend_result: user의 article 소비 경향 및 추천 글 top 30개 출력
* fr(f_ratio) -> target user가 본(또는 추천된) article 중에서 following의 비율
* mr(m_ratio) -> target user가 본(또는 추천된) article 중에서 magazine의 비율
* pr(p_ratio) -> target user가 본(또는 추천된) article 중에서 인기가 많은 글의 비율
* rr(r_ratio) -> target user가 본(또는 추천된) article 중에서 최근 발행된 글의 비율

![rec_result](https://user-images.githubusercontent.com/50820635/62028536-6e8b6100-b21b-11e9-81b6-7a2ba7d2b793.jpg)
