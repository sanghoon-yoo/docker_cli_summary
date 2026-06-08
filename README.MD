🐳 Docker 명령어 정리

📄 Dockerfile 기본 구조

```
FROM {도커 이미지}

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY app.js ./

ENTRYPOINT ["node", "app.js"]
```

🔨 이미지 빌드 & 관리

이미지 빌드

`docker build -f {도커파일명} --no-cache -t {레포지토리명:태그명} .`

이미지 목록 조회

`docker images`

최신 버전 태그 관리

`docker tag {레포지토리명:태그명} {레포지토리명:latest}`

이미지 삭제

`sudo docker rmi {이미지ID}`

▶️ 컨테이너 실행

기본 실행

> 데몬(서버) 이미지 실행 - 포트 매핑 사용
> - docker run -d -p 8080:7000 {레포지토리명:태그명}

> 일반 이미지 실행 - 포트 없이 -t 옵션 사용
> - docker run -t {레포지토리명:태그명}
> - ⚠️ 참고: 데몬이 아닌 이미지는 -p 대신 -t로 포트 없이 실행

바인드 마운트와 함께 실행

> docker run -v /호스트/경로:/컨테이너/경로 {이미지명}
> - 예시)
> docker run -v /home/user/data:/app/data -p 8080:7000 atlassian-api

📋 컨테이너 프로세스 관리

실행 중인 프로세스 목록

> docker ps -a

로그 확인

> docker logs {도커컨테이너ID}

컨테이너 CLI 접속

> docker exec -it {도커컨테이너ID} bash

프로세스 종료

> docker kill {도커컨테이너ID}

프로세스 삭제

> docker rm {도커컨테이너ID}

📁 파일 이동
> 호스트 → 컨테이너 (역방향도 가능)
> - sudo docker cp {파일명} {컨테이너ID}:{컨테이너경로}
> - 예시)
> sudo docker cp openlogic-openjdk-21.0.11+10-linux-x64.tar.gz 689485fb92fb:/root

🌐 네트워크
> 💡 도커 IP 체계는 bridge 172.17.0.0 대역 사용
> - sudo docker network ls
> - sudo docker network inspect bridge

🔗 도커 컨테이너 간 통신이 필요한 경우 Docker Compose 활용

🌍 외부와 통신하는 애플리케이션은 NAT 처리 됨

💾 볼륨 관리
```
sudo docker volume ls
sudo docker volume create app-volume
sudo docker inspect app-volume
```

💿 컨테이너 커밋

⚠️ 주의: 커밋은 부모 이미지에 대해 레이어 의존성을 생성하므로, Dockerfile 빌드 방식을 권장
> docker commit {컨테이너ID} {새이미지명:태그}
> - 예시)
> docker commit 689485fb92fb atlassian-api:saved

🗜️ 자식 이미지 평탄화 (Flatten)

레이어 의존성을 제거하고 독립적인 이미지로 만드는 작업

> 1️⃣ 자식 이미지로 컨테이너 실행
> - docker run -d --name temp_container {자식이미지ID}

> 2️⃣ 컨테이너 파일시스템 export
> - docker export temp_container > flat_image.tar

> 3️⃣ 새 독립 이미지로 import
> - docker import flat_image.tar my_new_image:latest

> 4️⃣ 임시 컨테이너 정리
> - docker rm temp_container
