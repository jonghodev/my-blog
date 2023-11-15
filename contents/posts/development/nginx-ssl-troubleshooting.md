---
title: nginx ssl troubleshooting
date: 2021-10-28 18:07:00
update: 2021-10-28 18:07:00
tags:
 - development
description:
---

폐쇄망에 가서 Nginx 를 Docker 사용해서 배포를 하는데 Local 에선 분명이 잘 됐던 Nginx 에 HTTPS 요청을 하니까 SSL 관련 에러가 났다.

curl 을 날려서 에러 메시지를 잘 보니 -k (insecure) 옵션을 주면 일단 될거라고 해서 -k 옵션을 주고 요청을 해보니 됐다.

문제를 해결하기 위해서 여러가지 레퍼런스를 살펴봤는데

기존의 SSL 인증서는 comodo 라는 인증서를 사용하고 그것이 Linux CA 에 잘 추가가 되어있는데 내가 사용하는 Sectigo 인증서는 기본적으로 리눅스 CA 에 추가가 되지 않기도 한다는 것이다.

그래서 curl 에 -v 옵션을 주어서 ca 파일의 경로를 찾아 내 인증서의 Public Key 를 추가해줬다. (Edit /etc/pki/tls/certs/ca-bundle.crt)

이렇게 하니 local 에서 띄우고 local 에 테스트할 때는 잘 됐는데, 다른 서버에서 내 서버로 요청할 때 똑같은 에러가 발생했다. 당연히 그 서버에도 그 인증서가 CA 에 등록이 안 되었던 것이었다.

그래서 그 다른 서버에서도 같은 작업을 하고 curl 을 날려보니 잘 되는 것을 확인했는데 그 JAVA Application 으로 요청할 때 SSL 에러가 났다. 그 이유는 JRE 가 사용하는 CA 가 따로 있었기 때문이다. 이것까지 수정해보려다가 너무 복잡하기도 하고 잘 안 되어서 다른 해결책을 생각해봤다. (사실 One to One 커넥션만 생길 예정이라서 다른 서버가 요청할 일은 없어서 확장성을 고려할 필요는 없었지만...)

그래서 [이 글](https://velog.io/@twkim8548/Nginx%EC%97%90%EC%84%9C-SSL-%EC%A0%81%EC%9A%A9%ED%95%B4%EC%84%9C-Https-%EB%A1%9C-%EC%A0%91%EC%86%8D-%EB%90%98%EA%B2%8C-%ED%95%B4%EB%B3%BC%EA%B9%8C)을 보니까 SSL 인증서들을 합치면 된다고 하는 것을 보고 따라했더니 됐다.

내가 사전에 인증서 폴더를 전달받았을 때 그 안에는 비슷해보이는 인증서들이 여럿 있었는데 나는 그중 적당한걸 골라서 사용했었다. 하지만 그렇게 하면 안 됐었고 다음과 같은 방법으로 도메인 인증서, 체인 인증서, 루트 인증서를 합처서 사용해야 했던 것이다.

```bash
cat [도메인인증서] [체인인증서] [루트인증서] > [새로운 인증서]
```

위 방법을 사용한 후 BEGIN CERTIFICATE, END CERTIFICATE 과 같은 글자들은 전부 개행을 해주어야 하고 순서도 맞춰야 한다. 그렇지 않으면 Private Key 로 복호화 할 때 에러가 난다.

그래서 이렇게 내 SSL 고군분투는 끝이 난다...

## References

https://louky0714.tistory.com/entry/Linux-sectigo-rootchain-%EC%9D%B8%EC%A6%9D%EC%84%9C-%EC%B6%94%EA%B0%80

https://velog.io/@twkim8548/Nginx%EC%97%90%EC%84%9C-SSL-%EC%A0%81%EC%9A%A9%ED%95%B4%EC%84%9C-Https-%EB%A1%9C-%EC%A0%91%EC%86%8D-%EB%90%98%EA%B2%8C-%ED%95%B4%EB%B3%BC%EA%B9%8C
