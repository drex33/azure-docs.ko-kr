---
title: Linux용 OpenSSL을 구성하는 방법
titleSuffix: Azure Cognitive Services
description: Linux용 OpenSSL을 구성하는 방법을 알아봅니다.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ROBOTS: NOINDEX
ms.openlocfilehash: ec2bd2cb46ff96602ed39cf3c9c4e41ddcf5ab33
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132155987"
---
# <a name="configure-openssl-for-linux"></a>Linux용 OpenSSL 구성

Speech SDK 버전 1.19.0 이상에서는 [OpenSSL](https://www.openssl.org) 가 호스트 시스템 버전에 동적으로 구성 됩니다. 이전 버전에서 OpenSSL은 SDK의 핵심 라이브러리에 정적으로 연결 됩니다.

연결을 보장하려면 OpenSSL 인증서가 시스템에 설치되어 있는지 확인합니다. 다음 명령을 실행합니다.
```bash
openssl version -d
```

Ubuntu/Debian 기반 시스템의 출력은 다음과 같습니다.
```
OPENSSLDIR: "/usr/lib/ssl"
```

`certs` 하위 디렉터리가 OPENSSLDIR 아래에 있는지 확인합니다. 위의 예제에서는 `/usr/lib/ssl/certs`입니다.

* `/usr/lib/ssl/certs`가 있고 여기에 많은 개별 인증서 파일(`.crt` 또는 `.pem` 확장명 사용)이 포함되어 있는 경우 추가 작업이 필요하지 않습니다.

* OPENSSLDIR이 `/usr/lib/ssl`이 아니고/아니거나 여러 개별 파일 대신 단일 인증서 번들 파일이 있는 경우 인증서를 찾을 수 있는 위치를 나타내는 적절한 SSL 환경 변수를 설정해야 합니다.

## <a name="examples"></a>예

- OPENSSLDIR은 `/opt/ssl`입니다. `.crt` 또는 `.pem` 파일이 많은 `certs` 하위 디렉터리가 있습니다.
Speech SDK를 사용하는 프로그램을 실행하기 전에 `/opt/ssl/certs`를 가리키도록 `SSL_CERT_DIR` 환경 변수를 설정합니다. 예를 들면 다음과 같습니다.
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR은 `/etc/pki/tls`입니다(RHEL/CentOS 기반 시스템에서와 같이). 인증서 번들 파일(예: `ca-bundle.crt`)이 있는 `certs` 하위 디렉터리가 있습니다.
Speech SDK를 사용하는 프로그램을 실행하기 전에 해당 파일을 가리키도록 `SSL_CERT_FILE` 환경 변수를 설정합니다. 예를 들면 다음과 같습니다.
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>인증서 해지 확인
Speech Service에 연결할 때 Speech SDK는 Speech Service에서 사용하는 TLS 인증서가 해지되지 않았는지 확인합니다. 이 확인을 수행하려면 Speech SDK가 Azure에서 사용하는 인증 기관의 CRL 배포 지점에 액세스해야 합니다. 가능한 CRL 다운로드 위치 목록은 [이 문서](../../security/fundamentals/tls-certificate-changes.md)에서 확인할 수 있습니다. 인증서가 해지되었거나 CRL을 다운로드할 수 없는 경우 Speech SDK에서 연결을 중단하고 '취소됨' 이벤트가 실행됩니다.

Speech SDK를 사용하는 네트워크에서 CRL 다운로드 위치에 대한 액세스를 허용하지 않는 방식으로 구성된 경우 CRL 확인을 사용하지 않도록 설정하거나 CRL을 검색할 수 없는 경우 실패하지 않도록 설정할 수 있습니다. 이 구성은 Recognizer 개체를 만드는 데 사용되는 구성 개체를 통해 수행됩니다.

CRL을 검색할 수 없는 경우 연결을 계속하려면 OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE 속성을 설정합니다.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
"true"로 설정하면 CRL을 검색하려고 시도하고, 검색이 성공하면 인증서가 해지되었는지 확인하고, 검색이 실패하면 연결을 계속할 수 있습니다.

인증서 해지 확인을 완전히 사용하지 않도록 설정하려면 OPENSSL_DISABLE_CRL_CHECK 속성을 "true"로 설정합니다.
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Linux의 일부 배포에는 TMP 또는 TMPDIR 환경 변수도 정의되어 있지 않습니다. 이로 인해 CRL이 만료될 때까지 다시 사용할 수 있도록 Speech SDK에서 CRL(인증서 해지 목록)을 디스크에 캐시하지 않고 매번 CRL(인증서 해지 목록)을 다운로드합니다. 초기 연결 성능을 향상시키기 위해 [TMPDIR이라는 환경 변수를 만들고, 선택한 임시 디렉터리의 경로로 설정](https://help.ubuntu.com/community/EnvironmentVariables)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech SDK 정보](speech-sdk.md)
