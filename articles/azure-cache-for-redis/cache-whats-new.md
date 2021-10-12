---
title: Azure Cache for Redis의 새로운 기능
description: Azure Cache for Redis의 최근 업데이트
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: reference
ms.date: 10/11/2021
ms.openlocfilehash: c836ed432cb4e138524f3724cb3aa0530039240c
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859364"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Azure Cache for Redis의 새로운 기능

## <a name="october-2021"></a>2021년 10월

### <a name="azure-cache-for-redis-60-ga"></a>Redis 6.0 GA 용 Azure 캐시

Redis 6.0에 대 한 Azure 캐시가 이제 일반 공급 됩니다. 새 버전에는 다음이 포함 됩니다.

- Redis 스트림, 새 데이터 형식
- 성능 향상
- 향상 된 개발자 생산성
- 보안 강화

이제 추가 전용 데이터 구조 인 Redis 스트림를 사용 하 여 지속적으로 생성 되는 데이터를 수집, 관리 및 이해할 수 있습니다.

또한 Redis 6.0에 대 한 Azure Cache에는 `STRALGO` ,, `ZPOPMIN` `ZPOPMAX` 및 `HELP` 성능 및 사용 편의성을 위한 새 명령이 도입 되었습니다.

Redis 6.0에 대 한 Azure Cache를 시작 하 고, 캐시를 만드는 동안 Redis 6.0를 선택 합니다. 또한 기존 Redis 4.0 cache 인스턴스를 업그레이드할 수 있습니다. 자세한 내용은 [Set Redis version For Azure Cache For Redis](cache-how-to-version.md)를 참조 하세요.

### <a name="diagnostics-for-connected-clients"></a>연결 된 클라이언트에 대 한 진단

이제 azure Cache for Redis가 Azure 진단 설정과 통합 되어 모든 클라이언트 연결에 대 한 정보를 캐시에 기록 합니다. 이 진단 설정을 기록 하 고 분석 하면 캐시에 연결 하는 사용자와 해당 연결의 타임 스탬프를 이해 하는 데 도움이 됩니다. 이 데이터는 보안 위반의 범위 및 보안 감사를 식별 하는 데 사용할 수 있습니다. 사용자는 저장소 계정 또는 이벤트 허브와 같은 선택한 대상으로 이러한 로그를 라우팅할 수 있습니다.

자세한 내용은 [진단 설정을 사용 하 여 Redis 데이터에 대 한 Azure 캐시 모니터링](cache-monitor-diagnostic-settings.md)을 참조 하세요.

### <a name="azure-cache-for-redis-enterprise-update"></a>Redis 용 Azure Cache Enterprise 업데이트

활성 지역 복제 공개 미리 보기는 이제 다음을 지원 합니다.

- RediSearch 모듈: 활성 지역 복제를 사용 하 여 RediSearch 배포
- 복제 그룹의 5 개 캐시. 이전에는 두 개의 캐시가 지원 되었습니다.
- OSS 클러스터링 정책-고성능 워크 로드에 적합 하며 더 나은 확장성을 제공 합니다.

## <a name="october-2020"></a>2020년 10월

### <a name="azure-tls-certificate-change"></a>Azure TLS 인증서 변경

Microsoft는 다른 CA(인증 기관) 집합의 TLS 서버 인증서를 사용하도록 Azure 서비스를 업데이트하고 있습니다. 이 변경은 2020년 8월 13일부터 2020년 10월 26일(예상)까지부터 단계적으로 배포됩니다. [현재 CA 인증서가 CA/브라우저 포럼 기준 요구 사항 중 하나가 아니기](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951) 때문에 Azure에서 이 변경을 수행합니다. 이 문제는 2020년 7월 1일에 보고되었으며, 전 세계적으로 인기 있는 여러 PKI(공개 키 인프라) 공급자에게 적용됩니다. 현재 Azure 서비스에서 사용하는 대부분의 TLS 인증서는 *Baltimore CyberTrust Root* PKI에서 제공됩니다. Azure Cache for Redis 서비스는 Baltimore CyberTrust Root에 계속 연결됩니다. 그러나 2020년 10월 12일부터는 새로운 ICA(중간 인증 기관)가 해당 TLS 서버 인증서를 발급합니다.

> [!NOTE]
> 이 변경은 퍼블릭 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)의 서비스로 제한됩니다. 소버린(예: 중국) 또는 정부 클라우드는 제외됩니다.
>
>

#### <a name="does-this-change-affect-me"></a>이 변경이 내게 영향을 주나요?

대부분의 Azure Cache for Redis 고객은 변경의 영향을 받지 않을 것으로 예상됩니다. 허용되는 인증서 목록을 명시적으로 지정하는 경우(“인증서 고정”이라고 함) 애플리케이션이 영향을 받을 수 있습니다. Baltimore CyberTrust Root 대신 중간 또는 리프 인증서에 고정된 경우에는 **즉시 작업을 수행** 하여 인증서 구성을 변경해야 합니다.

다음 표에서는 배포되는 인증서에 대한 정보를 제공합니다. 애플리케이션에서 사용하는 인증서에 따라 Azure Cache for Redis 인스턴스에 대한 연결 끊김을 방지하기 위해 업데이트해야 할 수도 있습니다.

| CA 유형 | 현재 | 배포(2020년 10월 12일) 이후 | 작업 |
| ----- | ----- | ----- | ----- |
| Root | 지문: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> 만료: 2025년 5월 12일 월요일 오후 4:59:00<br><br> 주체 이름:<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | 변경되지 않음 | 없음 |
| 중간 | 지문:<br> CN = Microsoft IT TLS CA 1<br> 지문: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> 지문: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> 지문: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> 지문: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> 만료: 2024년 5월 20일 금요일 오전 5:52:38<br><br> 주체 이름:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | 지문:<br> CN = Microsoft RSA TLS CA 01<br> 지문: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> 지문: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> 만료: 2024년 10월 8일 화요일 오전 12:00:00<br><br> 주체 이름:<br> O = Microsoft Corporation<br> C = US<br> | 필수 |

#### <a name="what-actions-should-i-take"></a>어떤 작업을 수행해야 하나요?

애플리케이션에서 운영 체제 인증서 저장소를 사용하거나 다른 인증서 중에서 Baltimore 루트를 고정하는 경우에는 아무 작업도 필요하지 않습니다.

애플리케이션에서 중간 또는 리프 TLS 인증서를 고정하는 경우 다음 루트를 고정하는 것이 좋습니다.

| 인증서 | Thumbprint |
| ----- | ----- |
| [Baltimore Root CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Digicert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> 중간 및 리프 인증서는 둘 다 자주 변경될 것으로 예상됩니다. 두 인증서에 의존하지 않는 것이 좋습니다. 대신, 배포 빈도가 더 낮은 루트 인증서에 애플리케이션을 고정합니다.
>
>

중간 인증서를 계속 고정하려면 고정된 중간 인증서 목록에 다음 항목을 추가합니다. 향후 변경을 최소화하기 위해 추가 항목은 목록에 몇 가지 더 포함됩니다.

| CA의 일반 이름 | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS Issuing CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS Issuing CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS Issuing CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS Issuing CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

애플리케이션에서 코드를 통해 인증서 유효성을 검사하는 경우 새로 고정된 인증서의 속성(예: 발급자, 지문)을 인식하도록 수정해야 합니다. 미래 경쟁력을 보장하기 위해 고정된 모든 인증서에 이 추가 확인을 적용해야 합니다.

## <a name="next-steps"></a>다음 단계

추가 질문이 있는 경우 [지원](https://azure.microsoft.com/support/options/)을 통해 문의하세요.  
