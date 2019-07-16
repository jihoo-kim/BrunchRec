# BrunchRec
Kakao arena 2nd Competition - Brunch article recommendation

## 1. 간단한 설명

## 2. 재현 방법
### Step 1. 데이터 및 소스 코드 다운로드
#### 1) res 폴더에 들어갈 파일 다운로드
* [카카오 아레나 브런치 데이터셋](https://arena.kakao.com/c/2/data)
* magazine.json, metadata.json, users.json, predict.tar, read.tar 다운로드
* predict.tar, read.tar은 res 폴더에 아래와 같이 압축 해제
* contents 폴더의 data.0 ~ data.6은 다운로드할 필요 없음
#### 2) pickle 폴더에 들어갈 파일 다운로드
* 중간 데이터 파일 (시간 단축)
* [dev.users용](https://drive.google.com/file/d/1lrVDWtpOuc-slylHgw60Lljfoh1HGU2B/view?usp=sharing)
* [test.users용](https://drive.google.com/file/d/1JP1Z6mOxViO1UR3HHosZJdguj4Oli97I/view?usp=sharing)
#### 3) BrunchRec.ipynb 파일 다운로드
* 소스 코드 파일 (Jupyter Notebook)
* [BrunchRec.ipynb](https://github.com/jihoo-kim/BrunchRec/archive/master.zip)

![dir](https://user-images.githubusercontent.com/50820635/61267897-a5ea1e80-a7d4-11e9-8925-300b2783f087.jpg)


### Step 2. 
#### 1) Jupyter Notebook으로 BrunchRec.ipynb 파일 열기
* Jupyter Notebook이 없을 경우 [설치](https://jupyter.org/install)
#### 2) 상단 메뉴에서 'Kernel' -> 'Restart & Run All' 클릭
* 또는 '앞으로 되감기(▶▶)' 아이콘 클릭
* 기본값은 test.users로 설정되어 있음
* dev.users에 대해 추천을 하고 싶다면, 소스 코드 하단에 주석 처리 변경
#### 3) 결과 파일 'recommend.txt' 생성 확인
* 기본값은 동일 디렉터리에 생성
* 다른 곳에 파일을 생성하고 싶다면, recommender 함수의 마지막 입력값에 경로 입력

![main](https://user-images.githubusercontent.com/50820635/61270051-75f24980-a7db-11e9-937e-286efd8f8baf.JPG)
