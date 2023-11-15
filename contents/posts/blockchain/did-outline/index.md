---
title: did 에 대해
date: 2021-05-08 18:00:00
update: 2021-05-08 18:00:00
tags:
 - blockchain
description:
---

DID(Decentralized Identity) 란 탈중앙화된 신원증명입니다. 개인정보를 개인 단말기에 저장해 개인 정보 인증시 필요한 정보만을 제출할 수 있게 해줍니다. 그리고 인증 정보에 관한 정보를 블록체인에 저장합니다.

DID 는 DID 와 DID Document 한 쌍으로 존재합니다.

DID 는 아래와 같은 형태를 갖고

`did:meta:000000000000000000000000000000000000000000000000000000000000c141`

그에 매핑되는 DID Document 는 다음과 같은 형태를 갖습니다.

```json
{
      "@context":"https://w3id.org/did/v0.11",
      "id":"did:meta:000000000000000000000000000000000000000000000000000000000000c141",
      "publicKey":[
         {
            "id":"did:meta:000000000000000000000000000000000000000000000000000000000000c141#MetaManagementKey#4fa7a0b19acd54b072961ed404ff6df0c6307706",
            "type":"EcdsaSecp256k1VerificationKey2019",
            "controller":"did:meta:000000000000000000000000000000000000000000000000000000000000c141",
            "publicKeyHex":"04b9ac63f0470ee657220d435b7461ab9b738f56684de3bad97e866ca79cfcada4fb0854efa5a42bbcd01cbc5f8816db9ae4e8b08a89cf2644bd515e1c8d593323"
         },
         {
            "id":"did:meta:000000000000000000000000000000000000000000000000000000000000c141#7e928682-4887-11ea-972f-0a0f3ad235f2#06ecdcfefd2c781a99975c75afff16fb1d6ef371",
            "type":"EcdsaSecp256k1VerificationKey2019",
            "controller":"did:meta:000000000000000000000000000000000000000000000000000000000000c141",
            "publicKeyHash":"06ecdcfefd2c781a99975c75afff16fb1d6ef371"
         },
					...
      ],
      "authentication":[
         "did:meta:000000000000000000000000000000000000000000000000000000000000c141#MetaManagementKey#4fa7a0b19acd54b072961ed404ff6df0c6307706",
         "did:meta:000000000000000000000000000000000000000000000000000000000000c141#7e928682-4887-11ea-972f-0a0f3ad235f2#06ecdcfefd2c781a99975c75afff16fb1d6ef371",
	       ...
      ],
      "service":[
         {
            "id":"did:meta:0000000000000000000000000000000000000000000000000000000000000527",
            "publicKey":"did:meta:000000000000000000000000000000000000000000000000000000000000c141#MetaManagementKey#4fa7a0b19acd54b072961ed404ff6df0c6307706",
            "type":"identityHub",
            "serviceEndpoint":"https://datahub.metadium.com"
         }
      ]
 }
```

DID Spec 에 관한 것은 아래 링크에서 구체적으로 찾아볼 수 있습니다.

[https://www.w3.org/TR/did-core/](https://www.w3.org/TR/did-core/)

우리는 DID 값만 알게 되는 경우가 많은데 DID 에 매핑되는 DID Document 을 얻기 위해서는 Resolver 에 요청하면 얻을 수 있습니다. DID Resolver 는 RestAPI 서버로 간단히 웹브라우저에 DID 를 넣어서 요청할 수 있습니다. Resolver 의 URL 은 다음과 같습니다.

[https://resolver.metadium.com/1.0/identifiers/did:meta:000000000000000000000000000000000000000000000000000000000000c141](https://resolver.metadium.com/1.0/identifiers/did:meta:000000000000000000000000000000000000000000000000000000000000c141)

아래 Java code 도 참고해주세요.

[https://github.com/METADIUM/did-resolver-java-client/blob/master/src/main/java/com/metaidum/did/resolver/client/DIDResolverAPI.java#L72](https://github.com/METADIUM/did-resolver-java-client/blob/master/src/main/java/com/metaidum/did/resolver/client/DIDResolverAPI.java#L72)

## 간단히 짚어보는 인증 플로우

DID 는 블록체인의 스마트 컨트랙트에 저장됩니다. Smart Contract 에는 DID 와 DID Document 가 Mapping 되어있고 그 DID Document 에는 Public key 가 적혀있습니다.

사용자가 DID 인증을 하기 위해 DID 를 보내면 인증하는 서버에서는 DID 를 Resolver 에 요청해서 DID Document 를 얻고 거기에 있는 Public Key 와, 함께 보낸 Signature 로부터 복구한 Public Key 와 비교해서 검증을 합니다. 이것을 검증하는 방법은 구체적으로 다른 섹션에서 알아보겠습니다.

## References

[https://github.com/METADIUM/meta-DID/blob/master/doc/DID-method-metadium](https://github.com/METADIUM/meta-DID/blob/master/doc/DID-method-metadium)
