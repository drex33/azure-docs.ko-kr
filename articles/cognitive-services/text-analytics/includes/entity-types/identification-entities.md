---
title: ID 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/09/2021
ms.author: aahi
ms.openlocfilehash: 621d74d37ef8593ee109aa9e5e099bcc2b381383
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672587"
---
### <a name="financial-account-identification"></a>금융 계정 ID

이 엔터티 범주에는 금융 정보 및 공식 ID 양식이 포함되어 있습니다.

#### <a name="category-aba-routing-number"></a>범주: ABA 라우팅 번호

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        ABA 라우팅 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        ABA(American Banker Association) 전송 라우팅 번호. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ABARoutingNumber`를 추가합니다. 검색되는 경우 `ABARoutingNumber` 또한 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="category-swift-code"></a>범주: SWIFT 코드

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        SWIFT 코드

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        지급 지시 정보에 대한 SWIFT 코드. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `SWIFTCode`을 추가합니다. 검색되는 경우 `SWIFTCode`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>범주: 신용 카드

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        신용 카드

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        신용 카드 번호. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CreditCardNumber`을 추가합니다. 검색되는 경우 `CreditCardNumber`가 API 응답에 반환됩니다.

    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>범주: IBAN(국제 은행 계좌 번호) 

이 범주에는 다음 엔터티가 포함됩니다.

:::row:::
    :::column span="":::
        **엔터티**

        국제 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        지급 지시 정보에 대한 IBAN 코드. `domain=phi` 또한 반환됩니다.

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `InternationalBankingAccountNumber`을 추가합니다. 검색되는 경우 `InternationalBankingAccountNumber`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="2":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>정부 및 국가/지역별 ID

> [!NOTE]
> 다음 금융 및 국가별 엔터티는 `domain=phi` 매개 변수에서 반환됩니다.
> * 여권 번호
> * 세금 ID

다음 엔터티는 국가별로 그룹화 및 나열됩니다.

#### <a name="argentina"></a>아르헨티나

:::row:::
    :::column span="":::
        **엔터티**

        DNI(아르헨티나 국가 ID) 번호 

    :::column-end:::
    :::column span="2":::
        **세부 정보** `domain=phi` 또한 반환됩니다.
        
        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ARNationalIdentityNumber`를 추가합니다. 검색되는 경우 `ARNationalIdentityNumber`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>오스트리아

:::row:::
    :::column span="":::
        **엔터티**

        오스트리아 신분증

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ATIdentityCard`를 추가합니다. 검색되는 경우 `ATIdentityCard`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트리아 납세자 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ATTaxIdentificationNumber`를 추가합니다. 검색되는 경우 `ATTaxIdentificationNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트리아 VAT(부가가치세) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ATValueAddedTaxNumber`를 추가합니다. 검색되는 경우 `ATValueAddedTaxNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>오스트레일리아

:::row:::
    :::column span="":::
        **엔터티**

        오스트레일리아 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AUDriversLicenseNumber`를 추가합니다. 검색되는 경우 `AUDriversLicenseNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 사업 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AUBusinessNumber`를 추가합니다. 검색되는 경우 `AUBusinessNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 회사 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AUCompanyNumber`를 추가합니다. 검색되는 경우 `AUCompanyNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 운전 면허증  

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AUDriversLicense`를 추가합니다. 검색되는 경우 `AUDriversLicense`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 의료 계좌 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `AUMedicalAccountNumber`를 추가합니다. 검색되는 경우 `AUMedicalAccountNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ATPassportNumber`를 추가합니다. 검색되는 경우 `ATPassportNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        오스트레일리아 납세자 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ATTaxIdentificationNumber`를 추가합니다. 검색되는 경우 `ATTaxIdentificationNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>벨기에

:::row:::
    :::column span="":::
        **엔터티**

        벨기에 국가 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `BENationalNumber`을 추가합니다. 검색되는 경우 `BENationalNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        벨기에 VAT(부가가치세) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `BEValueAddedTaxNumber`를 추가합니다. 검색되는 경우 `BEValueAddedTaxNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>브라질 

:::row:::
    :::column span="":::
        **엔터티**

        CNPJ(브라질 법인 번호)

        

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `BRLegalEntityNumber`을 추가합니다. 검색되는 경우 `BRLegalEntityNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        브라질 CPF 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `BRCPFNumber`를 추가합니다. 검색되는 경우 `BRCPFNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        브라질 국가 ID 카드(RG)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `BRNationalIDRG`를 추가합니다. 검색되는 경우 `BRNationalIDRG`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>캐나다

:::row:::
    :::column span="":::
        **엔터티**

        캐나다 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CABankAccountNumber`을 추가합니다. 검색되는 경우 `CABankAccountNumber`이 API 응답에 반환됩니다.
    
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 운전 면허증 번호

    :::column-end:::

    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CADriversLicenseNumber`를 추가합니다. 검색되는 경우 `CADriversLicenseNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 보건 의료 번호

        
    :::column-end:::

    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CAHealthServiceNumber`를 추가합니다. 검색되는 경우 `CAHealthServiceNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CAPassportNumber`를 추가합니다. 검색되는 경우 `CAPassportNumber`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 PHIN(개인 건강 식별 번호)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CAPersonalHealthIdentification`을 추가합니다. 검색되는 경우 `CAPersonalHealthIdentification`이 API 응답에 반환됩니다.

        `domain=phi` 또한 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        캐나다 사회 보장 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CASocialInsuranceNumber`를 추가합니다. 검색되는 경우 `CASocialInsuranceNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>칠레 

:::row:::
    :::column span="":::
        **엔터티**

        칠레 신분증 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CLIdentityCardNumber`을 추가합니다. 검색되는 경우 `CLIdentityCardNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>중국

:::row:::
    :::column span="":::
        **엔터티**

        중국 주민등록증(PRC) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CNResidentIdentityCardNumber`을 추가합니다. 검색되는 경우 `CNResidentIdentityCardNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>EU(유럽 연합)

:::row:::
    :::column span="":::
        **엔터티**

        EU 직불 카드 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `EUDebitCardNumber`를 추가합니다. 검색되는 경우 `EUDebitCardNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 운전 면허증 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `EUDriversLicenseNumber`를 추가합니다. 검색되는 경우 `EUDriversLicenseNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU GPU 좌표

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `EUGPSCoordinates`를 추가합니다. 검색되는 경우 `EUGPSCoordinates`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 국가 식별 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `EUNationalIdentificationNumber`를 추가합니다. 검색되는 경우 `EUNationalIdentificationNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `EUPassportNumber`를 추가합니다. 검색되는 경우 `EUPassportNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU SSN(사회 보장 번호) 또는 동등한 ID

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `EUSocialSecurityNumber`를 추가합니다. 검색되는 경우 `EUSocialSecurityNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU TIN(납세자 식별 번호)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `EUTaxIdentificationNumber`를 추가합니다. 검색되는 경우 `EUTaxIdentificationNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>프랑스

:::row:::
    :::column span="":::
        **엔터티**

        프랑스 운전 면허증 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `FRDriversLicenseNumber`를 추가합니다. 검색되는 경우 `FRDriversLicenseNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 건강 보험 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `FRHealthInsuranceNumber`를 추가합니다. 검색되는 경우 `FRHealthInsuranceNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 국가 ID 카드(CNI)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `FRNationalID`를 추가합니다. 검색되는 경우 `FRNationalID`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `FRPassportNumber`를 추가합니다. 검색되는 경우 `FRPassportNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 사회 보장 번호(INSEE)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `FRSocialSecurityNumber`를 추가합니다. 검색되는 경우 `FRSocialSecurityNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 납세자 번호(Numéro SPI)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `FRTaxIdentificationNumber`를 추가합니다. 검색되는 경우 `FRTaxIdentificationNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        프랑스 VAT(부가가치세) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `FRValueAddedTaxNumber`를 추가합니다. 검색되는 경우 `FRValueAddedTaxNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>독일

:::row:::
    :::column span="":::
        **엔터티**

        독일 운전 면허증 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `DEDriversLicenseNumber`를 추가합니다. 검색되는 경우 `DEDriversLicenseNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 신분증 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `DEIdentityCardNumber`를 추가합니다. 검색되는 경우 `DEIdentityCardNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `DEPassportNumber`를 추가합니다. 검색되는 경우 `DEPassportNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 납세자 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `DETaxIdentificationNumber`를 추가합니다. 검색되는 경우 `DETaxIdentificationNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        독일 부가가치세 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `DEValueAddedNumber`을 추가합니다. 검색되는 경우 `DEValueAddedNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>홍콩

:::row:::
    :::column span="":::
        **엔터티**

        홍콩 신분증(HKID) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `HKIdentityCardNumber`을 추가합니다. 검색되는 경우 `HKIdentityCardNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>헝가리

:::row:::
    :::column span="":::
        **엔터티**

        헝가리 개인 식별 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `HUPersonalIdentificationNumber`을 추가합니다. 검색되는 경우 `HUPersonalIdentificationNumber`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        헝가리 납세자 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `HUTaxIdentificationNumber`를 추가합니다. 검색되는 경우 `HUTaxIdentificationNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        헝가리 부가가치세 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `HUValueAddedNumber`를 추가합니다. 검색되는 경우 `HUValueAddedNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>인도

:::row:::
    :::column span="":::
        **엔터티**

        인도 소득세 번호(PAN)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `INPermanentAccount`를 추가합니다. 검색되는 경우 `INPermanentAccount`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        인도 고유 식별(Aadhaar) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `INUniqueIdentificationNumber`을 추가합니다. 검색되는 경우 `INUniqueIdentificationNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>인도네시아

:::row:::
    :::column span="":::
        **엔터티**

        인도네시아 신분증(KTP) 번호

    :::column-end:::
    :::column span="2":::

        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `IDIdentityCardNumber`를 추가합니다. 검색되는 경우 `IDIdentityCardNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>아일랜드

:::row:::
    :::column span="":::
        **엔터티**

        아일랜드 PPS(개인 공공 서비스) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `IEPersonalPublicServiceNumber`를 추가합니다. 검색되는 경우 `IEPersonalPublicServiceNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        아일랜드 PPS(개인 공공 서비스) 번호 v2

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `IEPersonalPublicServiceNumberV2`를 추가합니다. 검색되는 경우 `IEPersonalPublicServiceNumberV2`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>이스라엘

:::row:::
    :::column span="":::
        **엔터티**

        이스라엘 국가 ID

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ILNationalID`을 추가합니다. 검색되는 경우 `ILNationalID`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이스라엘 은행 계좌 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ILBankAccountNumber`를 추가합니다. 검색되는 경우 `ILBankAccountNumber`가 API 응답에 반환됩니다.
    
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>이탈리아

:::row:::
    :::column span="":::
        **엔터티**

        이탈리아 운전 면허증 ID

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ITDriversLicenseNumber`를 추가합니다. 검색되는 경우 `ITDriversLicenseNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이탈리아 회계 코드

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ITFiscalCode`를 추가합니다. 검색되는 경우 `ITFiscalCode`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        이탈리아 부가가치세 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ITValueAddedTaxNumber`를 추가합니다. 검색되는 경우 `ITValueAddedTaxNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>일본

:::row:::
    :::column span="":::
        **엔터티**

        일본 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `JPBankAccountNumber`를 추가합니다. 검색되는 경우 `JPBankAccountNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 운전 면허증 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `JPDriversLicenseNumber`를 추가합니다. 검색되는 경우 `JPDriversLicenseNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 "마이 넘버"(개인)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `JPMyNumberPersonal`을 추가합니다. 검색되는 경우 `JPMyNumberPersonal`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 "마이 넘버"(회사)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `JPMyNumberCorporate`를 추가합니다. 검색되는 경우 `JPMyNumberCorporate`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 주민 등록 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ITValueAddedTaxNumber`를 추가합니다. 검색되는 경우 `ITValueAddedTaxNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 재류 카드 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `JPResidenceCardNumber`를 추가합니다. 검색되는 경우 `JPResidenceCardNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 사회 보장 번호(SIN)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `JPSocialInsuranceNumber`를 추가합니다. 검색되는 경우 `JPSocialInsuranceNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        일본 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `JPPassportNumber`를 추가합니다. 검색되는 경우 `JPPassportNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>룩셈부르크

:::row:::
    :::column span="":::
        **엔터티**

        룩셈부르크 국가 식별 번호(자연인)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `LUNationalIdentificationNumberNatural`을 추가합니다. 검색되는 경우 `LUNationalIdentificationNumberNatural`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        룩셈부르크 국가 식별 번호(비 자연인)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `LUNationalIdentificationNumberNonNatural`을 추가합니다. 검색되는 경우 `LUNationalIdentificationNumberNonNatural`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>몰타

:::row:::
    :::column span="":::
        **엔터티**

        몰타 신분증 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `MTIdentityCardNumber`를 추가합니다. 검색되는 경우 `MTIdentityCardNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        몰타 납세자 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `MTTaxIDNumber`를 추가합니다. 검색되는 경우 `MTTaxIDNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>뉴질랜드

:::row:::
    :::column span="":::
        **엔터티**

        뉴질랜드 은행 계좌 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `NZBankAccountNumber`를 추가합니다. 검색되는 경우 `NZBankAccountNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        뉴질랜드 운전 면허증 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `NZDriversLicenseNumber`를 추가합니다. 검색되는 경우 `NZDriversLicenseNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        뉴질랜드 국세청 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `NZInlandRevenueNumber`를 추가합니다. 검색되는 경우 `NZInlandRevenueNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        뉴질랜드 보건부 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `NZMinistryOfHealthNumber`를 추가합니다. 검색되는 경우 `NZMinistryOfHealthNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       뉴질랜드 사회 복지 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `NZSocialWelfareNumber`를 추가합니다. 검색되는 경우 `NZSocialWelfareNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>필리핀

:::row:::
    :::column span="":::
        **엔터티**

        필리핀 통합 다목적 ID 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `PHUnifiedMultiPurposeIDNumber`을 추가합니다. 검색되는 경우 `PHUnifiedMultiPurposeIDNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>포르투갈 

:::row:::
    :::column span="":::
        **엔터티**

        포르투갈 시민 카드 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `PTCitizenCardNumber`을 추가합니다. 검색되는 경우 `PTCitizenCardNumber`이 API 응답에 반환됩니다.
          
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       포르투갈 납세자 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `PTTaxIdentificationNumber`를 추가합니다. 검색되는 경우 `PTTaxIdentificationNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>싱가포르

:::row:::
    :::column span="":::
        **엔터티**

        싱가포르 국제 등록 ID 카드(NRIC) 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `PTTaxIdentificationNumber`을 추가합니다. 검색되는 경우 `PTTaxIdentificationNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>남아프리카 공화국

:::row:::
    :::column span="":::
        **엔터티**

        남아프리카 공화국 ID 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ZAIdentificationNumber`을 추가합니다. 검색되는 경우 `ZAIdentificationNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>대한민국

:::row:::
    :::column span="":::
        **엔터티**

        대한민국 주민 등록 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `KRResidentRegistrationNumber`을 추가합니다. 검색되는 경우 `KRResidentRegistrationNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>스페인

:::row:::
    :::column span="":::
        **엔터티**

        스페인 DNI

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ESDNI`을 추가합니다. 검색되는 경우 `ESDNI`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스페인 사회 보장 번호(SSN)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ESSocialSecurityNumber`를 추가합니다. 검색되는 경우 `ESSocialSecurityNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        스페인 납세자 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `ESTaxIdentificationNumber`를 추가합니다. 검색되는 경우 `ESTaxIdentificationNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>스위스

:::row:::
    :::column span="":::
        **엔터티**

        스위스 사회 보장 번호(AHV)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `CHSocialSecurityNumber`을 추가합니다. 검색되는 경우 `CHSocialSecurityNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>대만 

:::row:::
    :::column span="":::
        **엔터티**

        대만 국가 ID

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `TWNationalID`을 추가합니다. 검색되는 경우 `TWNationalID`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       대만 거류증(ARC/TARC)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `TWResidentCertificate`를 추가합니다. 검색되는 경우 `TWResidentCertificate`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        대만 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `TWPassportNumber`를 추가합니다. 검색되는 경우 `TWPassportNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>영국

:::row:::
    :::column span="":::
        **엔터티**

        영국 운전 면허증 번호

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `UKDriversLicenseNumber`을 추가합니다. 검색되는 경우 `UKDriversLicenseNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       영국 선거인 명부 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `UKNationalInsuranceNumber`를 추가합니다. 검색되는 경우 `UKNationalInsuranceNumber`가 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국 국가 보건 서비스(NHS) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `UKNationalHealthNumber`를 추가합니다. 검색되는 경우 `UKNationalHealthNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국 사회 보장 번호(NINO)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `UKNationalInsuranceNumber`를 추가합니다. 검색되는 경우 `UKNationalInsuranceNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국 또는 미국 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `USUKPassportNumber`를 추가합니다. 검색되는 경우 `USUKPassportNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       영국 고유 납세자 참조 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `UKUniqueTaxpayerNumber`를 추가합니다. 검색되는 경우 `UKUniqueTaxpayerNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>미국

:::row:::
    :::column span="":::
        **엔터티**

        사회 보장 번호(SSN)

    :::column-end:::
    :::column span="2":::
        **세부 정보**

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `USSocialSecurityNumber`을 추가합니다. 검색되는 경우 `USSocialSecurityNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::
      **지원 문서 언어**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 운전 면허증 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `USDriversLicenseNumber`를 추가합니다. 검색되는 경우 `USDriversLicenseNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 또는 영국 여권 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `USUKPassportNumber`를 추가합니다. 검색되는 경우 `USUKPassportNumber`가 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 개인 납세자 식별 번호(ITIN)

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `USIndividualTaxpayerIdentification`을 추가합니다. 검색되는 경우 `USIndividualTaxpayerIdentification`이 API 응답에 반환됩니다.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 마약단속국(DEA) 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `DrugEnforcementAgencyNumber`를 추가합니다. 검색되는 경우 `DrugEnforcementAgencyNumber`이 API 응답에 반환됩니다.
      
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       미국 은행 계좌 번호

    :::column-end:::
    :::column span="2":::

        이 엔터티 범주를 가져오려면 `piiCategories` 매개 변수에 `USBankAccountNumber`를 추가합니다. 검색되는 경우 `USBankAccountNumber`가 API 응답에 반환됩니다.
        
        `domain=phi` 또한 반환됩니다.
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
