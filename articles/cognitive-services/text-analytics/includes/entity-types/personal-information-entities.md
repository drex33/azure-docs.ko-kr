---
title: 개인 정보에 대한 명명된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/11/2021
ms.author: aahi
ms.openlocfilehash: c1b4d8801013eeb25c18b837ef4ff5a0245739e5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528620"
---
> [!NOTE]
> PHI(보호된 상태 정보)를 검색하려면 `domain=phi` 매개 변수 및 모델 버전 `2020-04-01` 이상을 사용합니다.
>
> 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
`/v3.1/entities/recognition/pii` 엔드포인트에 요청을 보낼 때 다음 엔터티 범주가 반환됩니다.


| 범주   |  설명                          |
|------------|-------------|
| [Person](#category-person)      |  사용자의 이름.  |
| [PersonType](#category-persontype) | 사용자가 보유한 작업 유형 또는 역할. |
| [전화 번호](#category-phonenumber) |전화 번호(미국과 유럽 전화 번호만 해당). |
| [조직](#category-organization) |  회사, 그룹, 정부 기관 및 기타 조직.  |
| [주소](#category-address) | 전체 우편 주소.  |
| [Email](#category-email) | 이메일 주소.   |
| [URL](#category-url) | 웹 사이트에 대한 URL입니다.  |
| [IPAddress](#category-ipaddress) | 네트워크 IP 주소.  |
| [DateTime](#category-datetime) | 날짜 및 하루 중 시간. | 
| [수량](#category-quantity) | 숫자 및 숫자 수량.  |
| [Azure 정보](#azure-information) | 식별 가능한 Azure 정보(예: 인증 정보).  |
| [ID](#identification) | 재무 및 국가별 ID.  |

### <a name="category-person"></a>범주: 사람

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        사람

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사용자의 이름. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `Person`를 추가합니다. 검색되는 경우 `Person`가 API 응답에 반환됩니다.
      
    :::column-end:::
    
    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>범주: PersonType

이 범주에는 다음 엔터티가 포함됩니다.


:::row:::
    :::column span="":::
        **엔터티**

        PersonType

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사용자가 보유한 작업 유형 또는 역할.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `PersonType`을 추가합니다. 검색되는 경우 `PersonType`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>범주: PhoneNumber

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전화 번호(미국과 유럽 전화 번호만 해당). `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `PhoneNumber`를 추가합니다. 검색되는 경우 `PhoneNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


### <a name="category-organization"></a>범주: 조직

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        조직

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        회사, 정치 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체. 국적 및 종교는 이 엔터티 형식에 포함되지 않습니다. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `Organization`를 추가합니다. 검색되는 경우 `Organization`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        의료    

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        의료 회사 및 그룹.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `OrganizationMedical`을 추가합니다. 검색되는 경우 `OrganizationMedical`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        증권 거래소

    :::column-end:::
    :::column span="2":::

        증권 거래소 그룹. 

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `OrganizationStockExchange`를 추가합니다. 검색되는 경우 `OrganizationStockExchange`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        스포츠

    :::column-end:::
    :::column span="2":::

        스포츠 관련 단체.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `OrganizationSports`를 추가합니다. 검색되는 경우 `OrganizationSports`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


### <a name="category-address"></a>범주: 주소

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        주소

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전체 우편 주소. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `Address`를 추가합니다. 검색되는 경우 `Address`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-email"></a>범주: 이메일

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        메일

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이메일 주소. `domain=phi` 또한 반환됩니다.
      
        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `Email`를 추가합니다. 검색되는 경우 `Email`가 API 응답에 반환됩니다.

    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


### <a name="category-url"></a>범주: URL

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        URL

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        웹 사이트에 대한 URL입니다. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `URL`를 추가합니다. 검색되는 경우 `URL`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ipaddress"></a>범주: IPAddress

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        IPAddress

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        네트워크 IP 주소. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `IPAddress`를 추가합니다. 검색되는 경우 `IPAddress`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>범주: DateTime

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        DateTime

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        날짜 및 하루 중 시간. 

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `DateTime`을 추가합니다. 검색되는 경우 `DateTime`가 API 응답에 반환됩니다.
      
    :::column-end:::
:::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        Date

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        달력 날짜. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `Date`를 추가합니다. 검색되는 경우 `Date`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>범주: 수량

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        수량

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        숫자 및 숫자 수량.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `Quantity`를 추가합니다. 검색되는 경우 `Quantity`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>하위 범주

이 범주의 엔터티에는 다음과 같은 하위 범주가 있을 수 있습니다.

:::row:::
    :::column span="":::
        **엔터티 하위 범주**

        나이

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        나이. 

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `Age`를 추가합니다. 검색되는 경우 `Age`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="2":::
        **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure 정보

이러한 엔터티 범주에는 인증 정보 및 연결 문자열을 비롯한 식별 가능한 Azure 정보가 포함됩니다. `domain=phi` 매개 변수와 함께 반환되지 않습니다.

:::row:::
    :::column span="":::
        **엔터티**

        Azure DocumentDB 인증 키 

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        Azure Cosmos DB 서버에 대한 권한 부여 키입니다.   

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzureDocumentDBAuthKey`를 추가합니다. 검색되는 경우 `AzureDocumentDBAuthKey`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IAAS 데이터베이스 연결 문자열 및 Azure SQL 연결 문자열.
        

    :::column-end:::
    :::column span="2":::

        Azure IaaS(Infrastructure as a Service) 데이터베이스에 대한 연결 문자열 및 SQL 연결 문자열입니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzureIAASDatabaseConnectionAndSQLString`을 추가합니다. 검색되는 경우 `AzureIAASDatabaseConnectionAndSQLString`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT 연결 문자열  

    :::column-end:::
    :::column span="2":::

        Azure IoT에 대한 연결 문자열입니다. 
      
        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzureIoTConnectionString`을 추가합니다. 검색되는 경우 `AzureIoTConnectionString`이 API 응답에 반환됩니다.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure 게시 설정 암호  

    :::column-end:::
    :::column span="2":::

        Azure 게시 설정에 대한 암호입니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzurePublishSettingPassword`를 추가합니다. 검색되는 경우 `AzurePublishSettingPassword`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache 연결 문자열 

    :::column-end:::
    :::column span="2":::

        Redis 캐시에 대한 연결 문자열입니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzureRedisCacheString`을 추가합니다. 검색되는 경우 `AzureRedisCacheString`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Azure SaaS(Software as a Service)에 대한 연결 문자열입니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzureSAS`를 추가합니다. 검색되는 경우 `AzureSAS`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus 연결 문자열

    :::column-end:::
    :::column span="2":::

        Azure 서비스 버스에 대한 연결 문자열입니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzureServiceBusString`을 추가합니다. 검색되는 경우 `AzureServiceBusString`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage 계정 키 

    :::column-end:::
    :::column span="2":::

        Azure 스토리지 계정에 대한 계정 키입니다. 

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzureStorageAccountKey`를 추가합니다. 검색되는 경우 `AzureStorageAccountKey`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage 계정 키(일반)

    :::column-end:::
    :::column span="2":::

        Azure 스토리지 계정에 대한 일반 계정 키입니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AzureStorageAccountGeneric`을 추가합니다. 검색되는 경우 `AzureStorageAccountGeneric`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server 연결 문자열 

    :::column-end:::
    :::column span="2":::

        SQL Server를 실행하는 컴퓨터의 연결 문자열입니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `SQLServerConnectionString`을 추가합니다. 검색되는 경우 `SQLServerConnectionString`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification(식별)

[!INCLUDE [supported identification entities](./identification-entities.md)]
