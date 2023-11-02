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

앞서 만들었던 React, Flask, Mysql Database를 이용해 3-tier-SPA를 구축했습니다. 배포 자동화를 위해서 과정을 설명하겠습니다.

# Docker Container
## Mysql Database 컨테이너화
### Database를 Mysql Workbench의 Forwarding tool로 DB DDL 추출
```sql
CREATE SCHEMA IF NOT EXISTS `auction` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci ;
USE `auction` ;

-- -----------------------------------------------------
-- Table `auction`.`user`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `auction`.`user` (
  `id` VARCHAR(50) NOT NULL,
  `phone` VARCHAR(50) NOT NULL,
  `password` VARCHAR(50) NOT NULL,
  `nickname` VARCHAR(50) NULL DEFAULT NULL,
  PRIMARY KEY (`id`))
ENGINE = InnoDB
DEFAULT CHARACTER SET = utf8mb4
COLLATE = utf8mb4_0900_ai_ci;
-- -----------------------------------------------------
-- Table `auction`.`history`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `auction`.`history` (
  `id` int NOT NULL AUTO_INCREMENT,
  `user_id` varchar(50) NOT NULL,
  `item_id` int NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=27 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
-- -----------------------------------------------------
-- Table `auction`.`item`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `auction`.`item` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `endTime` DATETIME NOT NULL,
  `startTime` DATETIME NOT NULL,
  `name` VARCHAR(100) NOT NULL,
  `content` TEXT NULL DEFAULT NULL,
  `price` DOUBLE NOT NULL,
  `user_id` VARCHAR(50) NOT NULL,
  `image` VARCHAR(200) NOT NULL,
  PRIMARY KEY (`id`),
  INDEX `fk_item_user_idx` (`user_id` ASC) VISIBLE,
  CONSTRAINT `fk_item_user`
    FOREIGN KEY (`user_id`)
    REFERENCES `auction`.`user` (`id`)
    ON DELETE CASCADE
    ON UPDATE CASCADE)
ENGINE = InnoDB
AUTO_INCREMENT = 32
DEFAULT CHARACTER SET = utf8mb4
COLLATE = utf8mb4_0900_ai_ci;


-- -----------------------------------------------------
-- Table `auction`.`history`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `auction`.`prehistory` (
  `id` int NOT NULL AUTO_INCREMENT,
  `user_id` varchar(50) NOT NULL,
  `item_id` int NOT NULL,
  `endTime` datetime NOT NULL,
  PRIMARY KEY (`id`),
  KEY `fk_prehistory_user1_idx` (`user_id`),
  KEY `fk_prehistory_item1_idx` (`item_id`),
  CONSTRAINT `fk_prehistory_item1` FOREIGN KEY (`item_id`) REFERENCES `item` (`id`),
  CONSTRAINT `fk_prehistory_user1` FOREIGN KEY (`user_id`) REFERENCES `user` (`id`)
)
ENGINE = InnoDB
AUTO_INCREMENT = 6
DEFAULT CHARACTER SET = utf8mb4
COLLATE = utf8mb4_0900_ai_ci;

CREATE USER 'user1'@'%' IDENTIFIED BY '1234';
GRANT ALL ON auction.* TO 'user1'@'%';
```
### Mysql Database Dockerfile 작성
C:\miniProjectFront\Dockerfile
```Dockerfile
FROM mysql:8.0
# ENV MYSQL_ROOT_USER=root
ENV MYSQL_ROOT_PASSWORD=1234
COPY ./init.sql /docker-entrypoint-initdb.d
```
### 작성한 Dockerfile로 mysql db 이미지 빌드
C:\Users\User\Desktop\miniProjectBack>docker image build -t mysql:2.0 -f Dockerfile-mysql .
```
[+] Building 1.7s (8/8) FINISHED                                                                         docker:default
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => [internal] load build definition from Dockerfile-mysql                                                         0.0s
 => => transferring dockerfile: 165B                                                                               0.0s
 => [internal] load metadata for docker.io/library/mysql:8.0                                                       1.6s
 => [auth] library/mysql:pull token for registry-1.docker.io                                                       0.0s
 => [internal] load build context                                                                                  0.0s
 => => transferring context: 30B                                                                                   0.0s
 => [1/2] FROM docker.io/library/mysql:8.0@sha256:974cac08fff819ea2dfeb83fed4d2eb5100bb79603aff6148bdc53d8be4895f  0.0s
 => CACHED [2/2] COPY ./init.sql /docker-entrypoint-initdb.d                                                       0.0s
 => exporting to image                                                                                             0.0s
 => => exporting layers                                                                                            0.0s
 => => writing image sha256:a8d83556830d94594cfec7bee72cfd8950c86c0698d63c4150ab8e29ccfa393b                       0.0s
 => => naming to docker.io/library/mysql:2.0
```
## Flask 컨테이너화
### Flask Dockerfile 작성
C:\miniProjectFront\Dockerfile
```Dockerfile
FROM python:3.11
RUN apt-get update && apt-get install -y cron
WORKDIR /app
COPY . .
RUN pip install jwt
RUN pip install --no-cache-dir -r requirements.txt
#COPY crontabFile /etc/cron.d/cronfile
#RUN chmod 0644 /etc/cron.d/cronfile
RUN crontab -l | { cat; echo "* * * * * /usr/local/bin/python /app/historyUpdate.py >> /var/log/cron.log 2>&1"; } | crontab -
CMD ["sh", "-c", "cron && python app.py"]
```
### 작성한 Dockerfile로  flask 이미지 빌드
C:\Users\User\Desktop\miniProjectBack>docker image build -t flask:2.0 -f Dockerfile-flask .
```
[+] Building 31.1s (6/12)                                                                                docker:default
 => [internal] load build definition from Dockerfile-flask                                                         0.0s
 => => transferring dockerfile: 465B                                                                               0.0s
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => [internal] load metadata for docker.io/library/python:3.11                                                     2.6s
 => [auth] library/python:pull token for registry-1.docker.io                                                      0.0s
 => [1/7] FROM docker.io/library/python:3.11@sha256:6deadd529bed8232c98895a58fa8d689bdba285e9ceb92cda1f5fd8fa4a7  26.9s
 => => resolve docker.io/library/python:3.11@sha256:6deadd529bed8232c98895a58fa8d689bdba285e9ceb92cda1f5fd8fa4a78  0.0s
 => => sha256:325c5bf4c2f26c11380501bec4b6eef8a3ea35b554aa1b222cbcd1e1fe11ae1d 64.13MB / 64.13MB                   8.8s
 => => sha256:6deadd529bed8232c98895a58fa8d689bdba285e9ceb92cda1f5fd8fa4a78fa1 2.14kB / 2.14kB                     0.0s
 => => sha256:99cb81c1d8e4d6fe275c1f5127c770ad86a64286533e06991e1887d3e18aa812 2.01kB / 2.01kB                     0.0s
 => => sha256:3f7984adbac453e46f5063ac7193e461419ad446f25ecbd84f9a38edf36e4ce4 7.53kB / 7.53kB                     0.0s
 => => sha256:8457fd5474e70835e4482983a5662355d892d5f6f0f90a27a8e9f009997e8196 49.58MB / 49.58MB                   8.1s
 => => sha256:13baa2029dde87a21b87127168a0fb50a007c07da6b5adc8864e1fe1376c86ff 24.05MB / 24.05MB                   2.4s
 => => sha256:7e18a660069fd7f87a7a6c49ddb701449bfb929c066811777601d36916c7f674 211.06MB / 211.06MB                13.5s
 => => sha256:98a59f0ffedebe05b4e1ada824cb5389ff0552e77d501d89554a0d705b642d73 6.39MB / 6.39MB                    10.8s
 => => extracting sha256:8457fd5474e70835e4482983a5662355d892d5f6f0f90a27a8e9f009997e8196                          5.0s
 => => sha256:3a5444633a3348f11441df77b3fe52d2315b6ba0292a683a5b00ee77bd55581c 19.79MB / 19.79MB                  13.6s
 => => sha256:bbbc9b405dabdd464f2648e63d47bcb6233868b806ef20c51111bfe88a37829e 244B / 244B                        11.1s
 => => sha256:d9992232ef9b080bff5a001d720504d9fab61a70d5c5fef2b51c918b06758de9 3.11MB / 3.11MB                    12.3s
 => => extracting sha256:13baa2029dde87a21b87127168a0fb50a007c07da6b5adc8864e1fe1376c86ff                          0.7s
 => => extracting sha256:325c5bf4c2f26c11380501bec4b6eef8a3ea35b554aa1b222cbcd1e1fe11ae1d                          3.1s
 => => extracting sha256:7e18a660069fd7f87a7a6c49ddb701449bfb929c066811777601d36916c7f674                          7.6s
 => => extracting sha256:98a59f0ffedebe05b4e1ada824cb5389ff0552e77d501d89554a0d705b642d73                          0.3s
 => => extracting sha256:3a5444633a3348f11441df77b3fe52d2315b6ba0292a683a5b00ee77bd55581c                          0.7s
 => => extracting sha256:bbbc9b405dabdd464f2648e63d47bcb6233868b806ef20c51111bfe88a37829e                          0.0s
 => => extracting sha256:d9992232ef9b080bff5a001d720504d9fab61a70d5c5fef2b51c918b06758de9                          0.3s
 => [internal] load build context                                                                                  0.2s
 => => transferring context: 284.08kB
```
## React 컨테이너화
### React Dockerfile 작성
C:\miniProjectBack\Dockerfile
```Dockerfile
FROM    node AS builder
RUN     mkdir /my-app
WORKDIR /my-app
COPY    . .
RUN     npm install
RUN     npm run build


FROM    nginx AS runtime
COPY    --from=builder /my-app/build/ /usr/share/nginx/html/
CMD     ["nginx", "-g", "daemon off;"]
```

### 작성한 Dockerfile로 React 이미지 빌드
C:\Users\User\Desktop\miniProjectFront>docker image build -t react:2.0 .
```
[+] Building 6.0s (8/15)                                                                                 docker:default
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => [internal] load build definition from Dockerfile                                                               0.0s
 => => transferring dockerfile: 291B                                                                               0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                    2.6s
 => [internal] load metadata for docker.io/library/node:latest                                                     2.6s
 => [auth] library/nginx:pull token for registry-1.docker.io                                                       0.0s
 => [auth] library/node:pull token for registry-1.docker.io                                                        0.0s
 => [builder 1/6] FROM docker.io/library/node@sha256:0052410af98158173b17a26e0e2a46a3932095ac9a0ded660439a8ffae65  3.3s
 => => resolve docker.io/library/node@sha256:0052410af98158173b17a26e0e2a46a3932095ac9a0ded660439a8ffae65b1e3      0.2s
 => => sha256:c30e0acec6d561f6ec4e8d3ccebdeedbe505923a613afe3df4a6ea0a40f2240a 3.37kB / 3.37kB                     0.3s
 => => sha256:6cc2ae4489b295d2ae25b6931f9d5523479c7e6e85f66dde06b150ec33e4003f 49.64MB / 49.64MB                   3.0s
 => => sha256:0052410af98158173b17a26e0e2a46a3932095ac9a0ded660439a8ffae65b1e3 1.21kB / 1.21kB                     0.0s
 => => sha256:a59381eeb372ade238dcde65dce1fb6ad48c4eda288bf4e3e50b94176ee67d60 2.00kB / 2.00kB                     0.0s
 => => sha256:b612cbc8128d478d7f30c1bae6b2890433ae510c200907f4590e228e774e6160 7.53kB / 7.53kB                     0.0s
 => => sha256:2c7b705ed4b2417684e41f5b27d5edeb4978bc50ebdc5c085f0c90725cbbb610 2.23MB / 2.23MB                     1.0s
 => => extracting sha256:c30e0acec6d561f6ec4e8d3ccebdeedbe505923a613afe3df4a6ea0a40f2240a                          0.0s
 => => sha256:36b2736268362c5a83ffe8fd2520b48572d80b9511ee0b84d13e7f63befcbdd0 450B / 450B                         1.0s
 => => extracting sha256:6cc2ae4489b295d2ae25b6931f9d5523479c7e6e85f66dde06b150ec33e4003f
```

### 빌드된 이미지들 확인
C:\Users\User\Desktop\miniProjectFront>docker image ls
```
REPOSITORY                                TAG                                        IMAGE ID       CREATED         SIZE
flask                                     2.0                                        c673b4d2fbd5   5 minutes ago   1.34GB
react                                     2.0                                        c0ab8f938e1d   17 hours ago    190MB
mysql                                     1.0                                        a8d83556830d   17 hours ago    582MB
mysql                                     2.0                                        a8d83556830d   17 hours ago    582MB
```
## Mysql, flask, React 컨테이너 각각 생성
C:\Users\User\Desktop\miniProjectFront>docker container run -d mysql
```
f0febcc12648bcd89369413a16ff2a5114fc95d1de8f82c29a15d0a5bc470b23
```
C:\Users\User\Desktop\miniProjectFront>docker container run -d flask:2.0
```
b8e8f7ed452a4c129d549575cb31379c0c86447fd82deb61f71bc4e2d37fd07e
```
C:\Users\User\Desktop\miniProjectFront>docker container run -d react:2.0
```
0225e62d95f994a4c13b29c0eeb10be6f8d02e2ad920a7ea7dd17b9cecf51fe6
```
## 생성된 컨테이너들 확인
C:\Users\User\Desktop\miniProjectFront>docker container ls
```
CONTAINER ID   IMAGE                       COMMAND                   CREATED              STATUS              PORTS     NAMES
0225e62d95f9   react:2.0                   "/docker-entrypoint.…"   About a minute ago   Up About a minute   80/tcp    exciting_hertz
b8e8f7ed452a   flask:2.0                   "sh -c 'cron && pyth…"   About a minute ago   Up About a minute             eager_chaum
```
## Mysql Database와 Flask Backend 결합하기
- Docker-compose.yaml 작성해서 결합하기 
- 네트워크 생성 후 Database와 Flask를 같은 네트워크에 배정

둘 중 네트워크 생성해서 컨테이너들을 배정하는 방법 채택

## 네트워크 생성하기 
C:\Users\User\Desktop\miniProjectFront>docker network create lastdance
```
862453dd5b76ea62c6cfac2871638f23e894b516866984a804753111fdff12ff
```
## 생성한 네트워크 확인
C:\Users\User\Desktop\miniProjectFront>docker network ls
```
NETWORK ID     NAME        DRIVER    SCOPE
59aa4a23c272   bridge      bridge    local
ff5b36b6f513   host        host      local
862453dd5b76   lastdance   bridge    local
1b11b7589d74   mynetwork   bridge    local
c5c8c316b476   network-a   bridge    local
e161b435858e   none        null      local
96b0798a4e9c   test        bridge    local
```
## 생성했던 Mysql 컨테이너 CLI 환경에서 실행 
```
C:\Users\User\Desktop\miniProjectFront>docker container exec -it 4661265c2ff7 /bin/bash
					       				
bash-4.4# mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.35 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use auction
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-------------------+
| Tables_in_auction |
+-------------------+
| history           |
| item              |
| prehistory        |
| user              |
+-------------------+
4 rows in set (0.00 sec)

mysql> desc history
    -> ;
+---------+-------------+------+-----+---------+----------------+
| Field   | Type        | Null | Key | Default | Extra          |
+---------+-------------+------+-----+---------+----------------+
| id      | int         | NO   | PRI | NULL    | auto_increment |
| user_id | varchar(50) | NO   |     | NULL    |                |
| item_id | int         | NO   |     | NULL    |                |
+---------+-------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)
```
## 생성했던 Network에 컨테이너들 각각 배정

C:\Users\User\Desktop\miniProjectFront>docker container run -d -p 5000:5000 --network lastdance --name flask flask:2.0
```
3fb802a031d42ae70d23933a8effc952f7cd34331d06481766f69b31905b6681
```
C:\Users\User\Desktop\miniProjectFront>docker container run -d --network lastdance --name mysql mysql:2.0
```
fdda530a8261b26d81466d035825c605105e53c89616e14bceef64ede3855eb7
```
C:\Users\User\Desktop\miniProjectFront>docker container run -d -p 3000:80 --network mynetwork --name react react:2.0
```
c4719ce8fd73681f6786b81c66053fa3512cb86d594b1fdad3f25a66e08d3d3a
```
C:\Users\User\Desktop\miniProjectFront>docker container ls
```
CONTAINER ID   IMAGE                       COMMAND                   CREATED              STATUS              PORTS                    NAMES
fdda530a8261   mysql:2.0                   "docker-entrypoint.s…"   6 seconds ago        Up 5 seconds        3306/tcp, 33060/tcp      mysql
c4719ce8fd73   react:2.0                   "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:3000->80/tcp     react
3fb802a031d4   flask:2.0                   "sh -c 'cron && pyth…"   3 minutes ago        Up 3 minutes        0.0.0.0:5000->5000/tcp   flask
```

# Kubernetes
## 쿠버네티스 클러스터 셋업
### 가상머신 생성 
c:\kubernetis\vagrant-kubeadm-kubernetes>vagrant up
```
Bringing machine 'master' up with 'virtualbox' provider...
Bringing machine 'node01' up with 'virtualbox' provider...
Bringing machine 'node02' up with 'virtualbox' provider...
==> master: Checking if box 'bento/ubuntu-22.04' version '202309.08.0' is up to date...
==> master: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> master: flag to force provisioning. Provisioners marked to run always will still run.
==> node01: Checking if box 'bento/ubuntu-22.04' version '202309.08.0' is up to date...
==> node01: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> node01: flag to force provisioning. Provisioners marked to run always will still run.
==> node02: Checking if box 'bento/ubuntu-22.04' version '202309.08.0' is up to date...
==> node02: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> node02: flag to force provisioning. Provisioners marked to run always will still run.
```
### master-node 에 접속
c:\kubernetis\vagrant-kubeadm-kubernetes>vagrant ssh master
```
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-83-generic x86_64)
```
### VSCode 확장연결
![1698901610067](image/1조윤석현/1698901610067.png)
### 시크릿 생성 및 확인
#### 시크릿 생성 예시(없을 경우)
```
vagrant@master-node:~$ kubectl create secret docker-registry regcred --docker-server=https://index.docker.io/v1/ --docker-username=****** --docker-password=****** --docker-email=******
```
#### 생성된 시크릿 내용 조회
```
vagrant@master-node:~$ kubectl get secret regcred --output=yaml
apiVersion: v1
data:
  .dockerconfigjson: eyJhdXRocyI6eyJodHRwczovL2luZGV4LmRvY2tlci5pby92MS8iOnsidXNlcm5hbWUiOiJteWFuamluaSIsInBhc3N3b3JkIjoiQGN3bnU5MjE4IiwiZW1haWwiOiJteWFuamluaUBnbWFpbC5jb20iLCJhdXRoIjoiYlhsaGJtcHBibWs2UUdOM2JuVTVNakU0In19fQ==
kind: Secret
metadata:
  creationTimestamp: "2023-10-06T02:20:17Z"
  name: regcred
  namespace: default
  resourceVersion: "94204"
  uid: 17919b56-c624-4a43-84fa-3eb551b0b2cc
type: kubernetes.io/dockerconfigjson
```
#### 암호화된 시크릿 복호화
```
vagrant@master-node:~$ kubectl get secret regcred --output="jsonpath={.data.\.dockerconfigjson}" | base64 --decode
{"auths":{"https://index.docker.io/v1/":{"username":"myanjini","password":"*****","email":"myanjini@gmail.com","auth":"bXlhbmppbmk6QGN3bnU5MjE4"}}}
```
## mysql-deployment.yaml 파일 작성
/home/vagrant/mysql-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      name: mysql-pod
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: docker.io/shyun815/mysql:3.0
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "1234"        
        ports:
        - containerPort: 3306
        volumeMounts:
        - name: mysql-data
          mountPath: /var/lib/mysql  
          subPath: mysql
      imagePullSecrets:
      - name: regcred
      volumes:
      - name: mysql-data
        persistentVolumeClaim:
          claimName: mysql-pvc
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  type: LoadBalancer        # 서비스 타입(기본값이 ClusterIP)
  ports:                    
  - name: mysql
    port: 3306              # 서비스의 IP에 접근할 때 사용할 포트
    targetPort: 3306        # selector 항목에서 정의한 라벨에 의해 접근 대상이 된 파드 내부에서 사용하는 포트 
  selector:                 # 어떤 라벨의 파드에 접근할 수 있게 만들 것인지 결정
    app: mysql              # 파드의 라벨
```

## flask-deployment.yaml 파일 작성
/home/vagrant/flask-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: flask
  template:
    metadata:
      name: flask-pod
      labels:
        app: flask
    spec:
      containers:
      - name: flask
        image: docker.io/shyun815/flask:1.0
        ports:
        - containerPort: 5000
      imagePullSecrets:
      - name: mysecret  
---       
apiVersion: v1
kind: Service
metadata:
  name: flask-service
spec:
  type: LoadBalancer
  loadBalancerIP: 10.0.0.4
  ports:
  - name: flask
    port: 5000
    targetPort: 5000
  selector:
    app: flask
```
## react-deployment.yaml 파일 작성
/home/vagrant/react-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: react-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: react
  template:
    metadata:
      name: react-pod
      labels:
        app: react
    spec:
      containers:
      - name: react
        image: docker.io/shyun815/react:1.0
        ports:
        - containerPort: 3000
      imagePullSecrets:
      - name: regcred 
---       
apiVersion: v1
kind: Service
metadata:
  name: react-service
spec:
  type: LoadBalancer  
  ports:             
  - name: react
    port: 3000       
    targetPort: 80      
  selector:
    app: react
```    
## 통신 
- POD 끼리의 통신을 위해서, 기존 방식은 cluster ip를 이용하기 때문에 외부에서 접근 불가 
- NodePort :파드에 접근할 수 있는 포트를 클러스터의 모든 노드에 동일하게 개방
			외부에서 파드에 접근할 수 있는 서비스 타입
			접근할 수 있는 포트는 랜덤으로 정해지지만, 특정 포트로 접근하도록 설정할 수 있음
- LoadBalancer : 클라우드 플랫폼에서 제공하는 로드벨런서를 동적으로 프로비저닝해 파드에 연결
			NodePort 타입과 마찬가지로 외부에서 파드에 접근할 수 있는 서비스 타입
			일반적으로 AWS, GCP 등과 같은 클라우스 플랫폼 환경에서 사용
- ExternalName : 외부 서비스를 쿠버네티스 내부에서 호출하고자 할 때 사용 
			클러스터 내의 파드에서 외부 IP 주소에 서비스의 이름으로 접근할 수 있음
 
External name을 설정해서 서비스 이름으로 접근할 수 있게끔 적절한 서비스 타입 정의가 필요
LoadBalancer를 이용해서 통신을 구현해보자 
LB는 클라우드에서 주로 사용하며, 프로젝트는 on premise 환경에서 구현하기 때문에 metal lb를 사용

### 서비스 기능 추가
#### 서비스 기능
- 여러 개의 파드에 쉽게 접근할 수 있도록 고유한 도메인 이름을 부여
- 여러 개의 파드에 접근할 때, 요청을 분산하는 로드 밸런서 기능을 수행 
- 클라우드 플랫폼의 로드 밸런서, 클러서 노드의 포트 등을 통해 파드를 외부에 노출

### 볼륨 기능 추가 
#### 퍼시스턴트 볼륨.yaml 생성
/home/vagrant/mysql-pv.yaml
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 2Gi            # 볼륨의 크기 1G
  accessModes:
    - ReadWriteOnce         # 하나의 파드(또는 인스턴스)에 의해서만 마운트 될 수 있음
  hostPath:
    type: DirectoryOrCreate # 해당 디렉터리를 탐색하고, 없으면 생성
    path: /data/mysql       # 워커 노드에서 볼륨이 생성될 디렉터리
```

#### 퍼시스턴트 볼륨 크레임 생성
/home/vagrant/mysql-pvc.yaml
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

### POD 간 통신 구현
```
vagrant@master-node:~$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.17.0.1   <none>        443/TCP   17d
```
#### 퍼시스턴트 볼륨과 볼륨크레임 배포
```
vagrant@master-node:~$ kubectl apply -f mysql-pv.yaml
persistentvolume/mysql-pv created
vagrant@master-node:~$ kubectl apply -f mysql-pvc.yaml
persistentvolumeclaim/mysql-pvc created
```
#### deployment/service 배포
```
vagrant@master-node:~$ kubectl apply -f mysql-deployment.yaml
deployment.apps/mysql-deployment created
service/mysql-service created
vagrant@master-node:~$ kubectl apply -f flask-deployment.yaml
deployment.apps/flask-deployment created
service/flask-service created
vagrant@master-node:~$ kubectl apply -f mysql-deployment.yaml
deployment.apps/mysql-deployment unchanged
service/mysql-service unchanged
vagrant@master-node:~$ kubectl get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/flask-deployment-db55dcbd7-nwbks    1/1     Running   0          14s
pod/mysql-deployment-5b587d9457-gcqg7   1/1     Running   0          19s

NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/flask-service   LoadBalancer   172.17.53.73    10.0.0.4      5000:31274/TCP   14s
service/kubernetes      ClusterIP      172.17.0.1      <none>        443/TCP          17d
service/mysql-service   LoadBalancer   172.17.43.138   10.0.0.3      3306:30999/TCP   19s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flask-deployment   1/1     1            1           14s
deployment.apps/mysql-deployment   1/1     1            1           19s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/flask-deployment-db55dcbd7    1         1         1       14s
replicaset.apps/mysql-deployment-5b587d9457   1         1         1       19s
vagrant@master-node:~$ kubectl apply -f react-deployment.yaml
deployment.apps/react-deployment created
service/react-service created
```
#### POD들 External IP와 통신 확인 
```
vagrant@master-node:~$ kubectl get all
NAME                                    READY   STATUS    RESTARTS   AGE
pod/flask-deployment-db55dcbd7-nwbks    1/1     Running   0          55s
pod/mysql-deployment-5b587d9457-gcqg7   1/1     Running   0          60s
pod/react-deployment-65d6df987c-4lr5z   1/1     Running   0          5s

NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/flask-service   LoadBalancer   172.17.53.73    10.0.0.4      5000:31274/TCP   55s
service/kubernetes      ClusterIP      172.17.0.1      <none>        443/TCP          17d
service/mysql-service   LoadBalancer   172.17.43.138   10.0.0.3      3306:30999/TCP   60s
service/react-service   LoadBalancer   172.17.27.20    10.0.0.5      3000:32307/TCP   5s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/flask-deployment   1/1     1            1           55s
deployment.apps/mysql-deployment   1/1     1            1           60s
deployment.apps/react-deployment   1/1     1            1           5s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/flask-deployment-db55dcbd7    1         1         1       55s
replicaset.apps/mysql-deployment-5b587d9457   1         1         1       60s
replicaset.apps/react-deployment-65d6df987c   1         1         1       5s
```
