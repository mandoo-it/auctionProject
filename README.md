# AuctionProject

# 👖 미니 웹프로젝트2 - 중고 물품 경매 웹사이트👖

## 목차

- [개요 및 팀 소개](#개요)
- [진행 과정](#프로젝트-진행-과정)
- [구현 화면](#구현-페이지)

## 개요

- 프로젝트 이름: 중고 물품 경매 웹사이트
- 프로젝트 목적 :

  - 1차 프로젝트때 주제가 명확하지 않아 변경
  - 한정판 같은 물건들을 경매사이트에 올림으로 소비자, 구매자 모두 만족 가능
- 프로젝트 기간: 2023.11.26 - 2013.11.02
- 개발 엔진 및 언어: Python Flask, React, MySQL, Docker, Kubernetes
- 멤버: 김상훈, 윤규희, 윤석현, 최인혁, 전경원

## 👨‍👨‍👦 팀소개

|                                                                                                                            김상훈                                                                                                                            |                                                                                                                                        윤규희                                                                                                                                        |                                                               윤석현                                                               |                                                            최인혁                                                            |                                                            전경원                                                            |
| :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------: |
| ![Static Badge](https://img.shields.io/badge/팀장-%23FE642E) ![Static Badge](https://img.shields.io/badge/경매페이지-%235882FA) ![Static Badge](https://img.shields.io/badge/마이페이지-%235882FA) ![Static Badge](https://img.shields.io/badge/배포자동화-%235882FA) | ![Static Badge](https://img.shields.io/badge/로그인&회원가입-%235882FA)  ![Static Badge](https://img.shields.io/badge/Backend-%235882FA)    ![Static Badge](https://img.shields.io/badge/실시간데이터업데이트-%232552FA) ![Static Badge](https://img.shields.io/badge/이미지업로드-%232552FA) | ![Static Badge](https://img.shields.io/badge/마이페이지-%235882FA)  ![Static Badge](https://img.shields.io/badge/git형상관리-1235882FA) | ![Static Badge](https://img.shields.io/badge/메인페이지-%235882FA) ![Static Badge](https://img.shields.io/badge/디자인-1235882FA) | ![Static Badge](https://img.shields.io/badge/디자인-%235882FA) ![Static Badge](https://img.shields.io/badge/배포자동화-1235882FA) |

### ⚙️ 기술 스택

#### FRONTEND

![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)

#### BACKEND

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![MySQL](https://img.shields.io/badge/mysql-%2300f.svg?style=for-the-badge&logo=mysql&logoColor=white)

#### 자동 배포화 도구

<img src="https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=Docker&logoColor=white"/>

![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)

## 프로젝트 진행 과정

|       일별       | 내용                                                                   |
| :--------------: | ---------------------------------------------------------------------- |
|  1일차 `<br>`  | - 주제 선정, 기술 스택 결정, 데이터 베이스 설계                        |
| 2~6일차 `<br>` | - CRUD 기능 개발                                                       |
|  7일차 `<br>`  | - 도커를 사용하여 이미지를 빌드하고, 쿠버네티스를 통해 클러스터에 배포 |
|  8일차 `<br>`  | - 프로젝트 보고서 작성 및 발표                                         |

## 프로젝트 구조 및 특징

### ER DIAGRAM

![1698895705067](image/README/1698895705067.png)

### BACKEND

- API명을 카멜 케이스로 정의하여 네이밍 컨벤션을 지키도록 하였습니다.

```
📦backend
 ┣ 📂node_modules
 ┃ ┣ 📂flask-cors
 ┃ ┃ ┗ 📜package.json
 ┃ ┗ 📜.package-lock.json
 ┣ 📂resources # 프론트엔드에서 사용자가 업로드한 이미지를 저장
 ┃ ┣ 📜image.jpeg
 ┃ ┣ 📜me.png
 ┃ ┣ 📜김상훈.JPEG
 ┃ ┣ 📜우영미.JPEG
 ┃ ┗ 📜캡처.JPG
 ┣ 📂__pycache__
 ┃ ┗ 📜database.cpython-312.pyc
 ┣ 📜app.py
 ┣ 📜database.py
 ┣ 📜Dockerfile-flask
 ┣ 📜Dockerfile-mysql
 ┣ 📜historyUpdate.py #주기적으로 낙찰내역을 업데이트하는 cron에 사용되는 파일
 ┣ 📜init.sql
 ┗ 📜requirements.txt
```

- `JWT`을 만들어서 로그인 정보 있을 시 `localStorage`에 저장 -
  - 이 방법은 보안 상 취약 -> Access Token을 유효 기간을 짧게 설정하고,  만료되면 Refresh Token을 발급받도록 하는 방식으로 개선 가

```python
from flask_jwt_extended import JWTManager
from flask_jwt_extended import create_access_token
import os


app.config["JWT_SECRET_KEY"] = "super-secret"
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
jwt = JWTManager(app)

@app.route('/login', methods = ["GET", "POST"])
def login():
    if request.method == 'POST':
        userId = request.json.get('id')
        password = request.json.get('password')
   
        isid = database.idCheck(userId, password)
        if(isid) :
            access_token = create_access_token(identity=userId)
            return jsonify({'token': access_token, 'userId':userId}), 200
        else : 
            return jsonify({'message': '잘못된 로그인 정보입니다. 다시 입력해주세요.'}), 401

``` 
- 백그라운드에서 주기적으로 실행되는 자동화 작업
- 매분 매초에 crontab을 실행하며 경매 내역에 있는 데이터의 만료시간이 현재시간을 지났을 경우 history(낙찰 테이블)에 자동으로 넣는 python 파일 실행한다. 
    - frontend에서도 현재시간을 지났으면 낙찰 못하게 버튼 비활성화하게 개선 가능 
 
``` docker
RUN crontab -l | { cat; echo "* * * * * /usr/local/bin/python /app/historyUpdate.py >> /var/log/cron.log 2>&1"; } | crontab -
```

- backend resource에 이미지 저장 
    - 설정 변수에 넣어놓은 절대 경로에 이미지 저장
    - 데이터베이스에 넣을 때는 해당 파일에 접근할 수 있는 URL을 생성 후 INSERT
    
``` python
file.save(os.path.join(app.config['UPLOAD_FOLDER'], file.filename))
image_url = 'http://10.0.0.9:5000/resources/' + file.filename

```
### FRONTEND

- 컴포넌트와 페이지를 분리함으로, 여러 페이지에서 사용는 카드, 헤더 등을 재사용할 수 있게 되었습니다.

```
📦src
 ┣ 📂components
 ┃ ┣ 📜Card.js
 ┃ ┣ 📜Card.module.css
 ┃ ┣ 📜CreatePage.module.css
 ┃ ┣ 📜DetailPage.module.css
 ┃ ┣ 📜Footer.js
 ┃ ┣ 📜Footer.module.css
 ┃ ┣ 📜Header.js
 ┃ ┣ 📜Header.module.css
 ┃ ┣ 📜LoginPage.module.css
 ┃ ┣ 📜MainPage.module.css
 ┃ ┗ 📜MyPage.module.css
 ┣ 📂pages
 ┃ ┣ 📜CreatePage.js
 ┃ ┣ 📜DetailPage.js
 ┃ ┣ 📜LoginPage.js
 ┃ ┣ 📜MainPage.js
 ┃ ┣ 📜MyPage.js
 ┃ ┗ 📜SignupPage.js
 ┣ 📜App.css
 ┣ 📜App.js
 ┣ 📜App.test.js
 ┣ 📜index.css
 ┣ 📜index.js
 ┣ 📜logo.svg
 ┣ 📜reportWebVitals.js
 ┗ 📜setupTests.js
```

- Axios를 사용하여 서버와의 HTTP 요청을 만들고 응답을 처리

```javascript
axios
      .put(`http://10.0.0.9:5000/detail/${id}`, { price: price })
      .then((response) => {
        setData({ ...data, price: price });
        console.log("가격이 업데이트되었습니다.");

        const datas = new FormData();
        datas.append('itemId', data.id);
        datas.append('userId', purchaseId);
        datas.append('endTime', data.endTime);
        console.log(data.id)
        console.log(purchaseId)

        return axios.post(`http://10.0.0.9:5000/history`, datas);
      })
      .catch((error) => {
        console.error(error);
      });
```
- async/await: async함수 내부에서 await로 서버와 통신하고 promise가 끝나면 
처리 결과에 따라 다른 결과 반환
- 비동기 함수를 사용하여 다른 작업을 병렬로 실행할 수 있어 시스템 성능 향상 가능 


```javascript

 const handlerLogin = async () => {
    try {
      const response = await fetch(`http://10.0.0.9:5000/login`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ id, password })
      });
      console.log(response)

      if (response.ok) {
        console.log('로그인 성공! 토큰:', token);
      } else {
        console.log('로그인 실패!');
      }
    } catch (error) {
      console.error('로그인 중 오류 발생:', error);
    }
};

```
