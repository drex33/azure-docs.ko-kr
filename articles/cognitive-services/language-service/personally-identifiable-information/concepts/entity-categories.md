---
title: 언어에 대 한 Azure 인식 서비스에서 개인 식별이 가능한 정보 (검색)에서 인식 하는 엔터티 범주
titleSuffix: Azure Cognitive Services
description: PII 기능이 구조화 되지 않은 텍스트에서 인식할 수 있는 엔터티에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/15/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 099926359944c1f9e9a673f3c754019c761e6220
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519537"
---
# <a name="supported-personally-identifiable-information-pii-entity-categories"></a>지원 되는 PII (개인 식별이 가능한 정보) 엔터티 범주

이 문서를 사용 하 여 [PII 검색 기능](../how-to-call.md)에서 반환할 수 있는 엔터티 범주를 찾을 수 있습니다. 이 기능은 예측 모델을 실행 하 여 입력 문서에서 중요 한 정보를 식별, 범주화 및 교정 합니다.

PII 기능에는 개인 ( `PII` ) 및 상태 () 정보를 검색 하는 기능이 포함 되어 있습니다 `PHI` .

## <a name="entity-categories"></a>엔터티 범주

> [!NOTE]
> PHI(보호된 상태 정보)를 검색하려면 `domain=phi` 매개 변수 및 모델 버전 `2020-04-01` 이상을 사용합니다.
>
> 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
API 요청 PII 기능을 전송 하는 경우 다음 엔터티 범주가 반환 됩니다.

## <a name="category-person"></a>범주: 사람

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        사람

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사용자의 이름. PII와 no로 반환 됩니다.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `Person`을 추가합니다. 검색되는 경우 `Person`가 API 응답에 반환됩니다.
      
    :::column-end:::
    
    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

## <a name="category-persontype"></a>범주: PersonType

이 범주에는 다음 엔터티가 포함됩니다.


:::row:::
    :::column span="":::
        **엔터티**

        PersonType

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        사용자가 보유한 작업 유형 또는 역할.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `PersonType`을 추가합니다. 검색되는 경우 `PersonType`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

## <a name="category-phonenumber"></a>범주: PhoneNumber

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전화 번호(미국과 유럽 전화 번호만 해당). PII와 no로 반환 됩니다.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `PhoneNumber`을 추가합니다. 검색되는 경우 `PhoneNumber`이 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


## <a name="category-organization"></a>범주: 조직

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        조직

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        회사, 정치 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체. 국적 및 종교는 이 엔터티 형식에 포함되지 않습니다. PII와 no로 반환 됩니다.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `Organization`를 추가합니다. 검색되는 경우 `Organization`이 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `OrganizationMedical`을 추가합니다. 검색되는 경우 `OrganizationMedical`가 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `OrganizationStockExchange`를 추가합니다. 검색되는 경우 `OrganizationStockExchange`가 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `OrganizationSports`를 추가합니다. 검색되는 경우 `OrganizationSports`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


## <a name="category-address"></a>범주: 주소

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        주소

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        전체 우편 주소. PII와 no로 반환 됩니다.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `Address`을 추가합니다. 검색되는 경우 `Address`이 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

## <a name="category-email"></a>범주: 이메일

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        메일

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이메일 주소. PII와 no로 반환 됩니다.
      
        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `Email`을 추가합니다. 검색되는 경우 `Email`이 API 응답에 반환됩니다.

    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


## <a name="category-url"></a>범주: URL

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        URL

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        웹 사이트에 대한 URL입니다. PII와 no로 반환 됩니다.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `URL`을 추가합니다. 검색되는 경우 `URL`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

## <a name="category-ip"></a>범주: IP

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        IP

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        네트워크 IP 주소. PII와 no로 반환 됩니다.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `IP`을 추가합니다. 검색되는 경우 `IP`가 API 응답에 반환됩니다.
      
    :::column-end:::

    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

## <a name="category-datetime"></a>범주: DateTime

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        DateTime

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        날짜 및 하루 중 시간. 

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `DateTime`을 추가합니다. 검색되는 경우 `DateTime`이 API 응답에 반환됩니다.
      
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

        달력 날짜. PII와 no로 반환 됩니다.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `Date`를 추가합니다. 검색되는 경우 `Date`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

## <a name="category-quantity"></a>범주: 수량

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        수량

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        숫자 및 숫자 수량.

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `Quantity`를 추가합니다. 검색되는 경우 `Quantity`이 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `Age`를 추가합니다. 검색되는 경우 `Age`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="2":::
        **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure 정보

이러한 엔터티 범주에는 인증 정보 및 연결 문자열을 비롯한 식별 가능한 Azure 정보가 포함됩니다. 화 안 함으로 반환 되지 않습니다.

:::row:::
    :::column span="":::
        **엔터티**

        Azure DocumentDB 인증 키 

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        Azure Cosmos DB 서버에 대한 권한 부여 키입니다.   

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzureDocumentDBAuthKey`를 추가합니다. 검색되는 경우 `AzureDocumentDBAuthKey`이 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzureIAASDatabaseConnectionAndSQLString`을 추가합니다. 검색되는 경우 `AzureIAASDatabaseConnectionAndSQLString`이 API 응답에 반환됩니다.
      
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
      
        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzureIoTConnectionString`을 추가합니다. 검색되는 경우 `AzureIoTConnectionString`이 API 응답에 반환됩니다.

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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzurePublishSettingPassword`를 추가합니다. 검색되는 경우 `AzurePublishSettingPassword`가 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzureRedisCacheString`을 추가합니다. 검색되는 경우 `AzureRedisCacheString`이 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzureSAS`를 추가합니다. 검색되는 경우 `AzureSAS`가 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzureServiceBusString`을 추가합니다. 검색되는 경우 `AzureServiceBusString`이 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzureStorageAccountKey`를 추가합니다. 검색되는 경우 `AzureStorageAccountKey`가 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `AzureStorageAccountGeneric`을 추가합니다. 검색되는 경우 `AzureStorageAccountGeneric`이 API 응답에 반환됩니다.
      
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

        이 엔터티 범주를 가져오려면 `pii-categories` 매개 변수에 `SQLServerConnectionString`을 추가합니다. 검색되는 경우 `SQLServerConnectionString`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification(식별)

[!INCLUDE [supported identification entities](../includes/identification-entities.md)]


## <a name="next-steps"></a>다음 단계

* [NER 개요](../overview.md)
