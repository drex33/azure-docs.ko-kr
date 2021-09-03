---
title: 자격 증명 엔터티 만들기
titleSuffix: Azure Cognitive Services
description: 자격 증명 엔터티를 만들어 자격 증명을 안전하게 관리하는 방법입니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: mbullwin
ms.openlocfilehash: 0e16b92ff4a2316042e2177f9525734ba1949db1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342059"
---
# <a name="how-to-create-a-credential-entity"></a>방법: 자격 증명 엔터티 만들기

데이터 피드를 온보딩하는 경우 인증 유형을 선택해야 합니다. ‘Azure SQL 연결 문자열’, ‘서비스 주체’와 같은 일부 인증 유형의 경우 자격 증명을 안전하게 관리하기 위해 자격 증명 관련 정보를 저장할 자격 증명 엔터티가 필요합니다.  이 문서에서는 Metrics Advisor에서 다양한 자격 증명 유형에 대한 자격 증명 엔터티를 만드는 방법을 설명합니다.
    

## <a name="basic-procedure-create-a-credential-entity"></a>기본 절차: 자격 증명 엔터티 만들기

자격 증명 관련 정보를 저장할 **자격 증명 엔터티** 를 만들어 데이터 원본에 인증하는 데 사용할 수 있습니다. 자격 증명 엔터티를 다른 사용자와 공유하여 실제 자격 증명을 공유하지 않고도 사용자가 데이터 원본에 연결하도록 허용할 수 있습니다. ‘데이터 피드 추가’ 탭이나 ‘자격 증명 엔터티’ 탭에서 만들 수 있습니다. 특정 인증 유형에 대한 자격 증명 엔터티를 만든 후 새 데이터 피드를 추가할 때 만든 자격 증명 엔터티를 선택하기만 하면 되므로 여러 데이터 피드를 만들 때 유용합니다. 자격 증명 엔터티를 만들고 사용하는 일반적인 절차는 아래와 같습니다.

1. ‘데이터 피드 추가’ 탭에서 ‘+’를 선택하여 새 자격 증명 엔터티를 만듭니다(‘자격 증명 엔터티 피드’ 탭에서도 만들 수 있음).

   ![자격 증명 엔터티 만들기](../media/create-credential-entity.png)
 
2. 자격 증명 엔터티 이름, 설명(필요한 경우), 자격 증명 유형(‘인증 유형’과 같음), 기타 설정을 지정합니다.

   ![자격 증명 엔터티 설정](../media/set-credential-entity.png)
 
3. 자격 증명 엔터티를 만든 후 인증 유형을 지정할 때 선택할 수 있습니다.

   ![자격 증명 엔터티 선택](../media/choose-credential-entity.png)
 
Metrics Advisor에는 Azure SQL 연결 문자열, Azure Data Lake Storage Gen2 공유 키 엔터티, 서비스 주체, Key Vault 서비스 주체라는 **네 가지 자격 증명 유형** 이 있습니다. 다양한 자격 증명 유형 설정에 대해서는 다음 지침을 참조하세요.

## <a name="azure-sql-connection-string"></a>Azure SQL 연결 문자열

**이름** 과 **연결 문자열** 을 설정하고 ‘만들기’를 선택해야 합니다.

![SQL 연결 문자열에 대한 자격 증명 엔터티 설정](../media/credential-entity/credential-entity-sql-connection-string.png)

## <a name="azure-data-lake-storage-gen2-shared-key-entity"></a>Azure Data Lake Storage Gen2 공유 키 엔터티

**이름** 과 **계정 키** 를 설정하고 ‘만들기’를 선택해야 합니다. 계정 키는 **액세스 키** 설정의 Azure Storage 계정(Azure Data Lake Storage Gen2) 리소스에서 찾을 수 있습니다.

<!-- 增加basic说明，tips是错的；增加一下怎么管理；加一个step1的link
-->
![Data Lake에 대한 자격 증명 엔터티 설정](../media/credential-entity/credential-entity-data-lake.png)

## <a name="service-principal"></a>서비스 사용자

데이터 원본의 서비스 주체를 만들려면 [다양한 데이터 원본 연결](../data-feeds-from-different-sources.md)의 자세한 지침을 따릅니다. 서비스 주체를 만든 후 자격 증명 엔터티에서 다음 구성을 입력해야 합니다.

![sp 자격 증명 엔터티](../media/credential-entity/credential-entity-service-principal.png)

* **이름:** 서비스 주체 자격 증명 엔터티의 이름을 설정합니다.
* **테넌트 ID 및 클라이언트 ID:** Azure Portal에서 서비스 주체를 만든 후 **개요** 에서 `Tenant ID`와 `Client ID`를 찾을 수 있습니다.

    ![sp 클라이언트 ID 및 테넌트 ID](../media/credential-entity/sp-client-tenant-id.png)

* **클라이언트 비밀:** Azure Portal에서 서비스 주체를 만든 후에는 **인증서 및 비밀** 로 이동하여 새 클라이언트 비밀을 만들어야 하며, 해당 **값** 을 자격 증명 엔터티의 `Client Secret`로 사용해야 합니다. 참고: 이 값은 한 번만 표시되므로 어딘가에 저장하는 것이 좋습니다.


    ![sp 클라이언트 비밀 값](../media/credential-entity/sp-secret-value.png)

## <a name="span-idsp-from-kvservice-principal-from-key-vaultspan"></a><span id="sp-from-kv">Key Vault의 서비스 주체</span>

키 자격 증명 모음에서 서비스 주체를 만드는 몇 가지 단계가 있습니다.

**1단계. 서비스 주체를 만들고 데이터베이스 액세스 권한을 부여합니다.** 각 데이터 원본의 서비스 주체 만들기 섹션에서 [다양한 데이터 원본 연결](../data-feeds-from-different-sources.md)의 자세한 지침을 따릅니다. 

Azure Portal에서 서비스 주체를 만든 후 **개요** 에서 `Tenant ID`와 `Client ID`를 찾을 수 있습니다. 자격 증명 엔터티 구성에서 **디렉터리(테넌트) ID** 는 `Tenant ID`여야 합니다.

![sp 클라이언트 ID 및 테넌트 ID](../media/credential-entity/sp-client-tenant-id.png)

**2단계. 새 클라이언트 비밀을 만듭니다.** **인증서 및 비밀** 로 이동하여 새 클라이언트 비밀을 만들어야 하며, 해당 **값** 은 다음 단계에서 사용됩니다. 참고: 이 값은 한 번만 표시되므로 어딘가에 저장하는 것이 좋습니다.

![sp 클라이언트 비밀 값](../media/credential-entity/sp-secret-value.png)

**3단계. 키 자격 증명 모음을 만듭니다.** [Azure Portal](https://ms.portal.azure.com/#home)에서 **키 자격 증명 모음** 을 선택하여 새로 만듭니다.

![Azure Portal에서 키 자격 증명 모음 만들기](../media/credential-entity/create-key-vault.png)

키 자격 증명 모음을 만든 후 MA(Metrics Advisor) 자격 증명 엔터티에서 **자격 증명 모음 URI** 는 `Key Vault Endpoint`입니다.

![키 자격 증명 모음 엔드포인트](../media/credential-entity/key-vault-endpoint.png)

**4단계. Key Vault의 비밀을 만듭니다.** Azure Portal의 키 자격 증명 모음 **설정->비밀** 에서 두 개의 비밀을 생성합니다.
첫 번째 비밀은 `Service Principal Client Id`용, 두 번째 비밀은 `Service Principal Client Secret`용으로, 두 이름 모두 자격 증명 엔터티 구성에서 사용됩니다.

![비밀 생성](../media/credential-entity/generate-secrets.png)

* **서비스 주체 클라이언트 ID:** 이 비밀의 `Name`을 설정합니다. 이름은 자격 증명 엔터티 구성에서 사용되며, 값은 **1단계** 의 서비스 주체 `Client ID`여야 합니다.

    ![secret1: sp 클라이언트 ID](../media/credential-entity/secret-1-sp-client-id.png)

* **서비스 주체 클라이언트 비밀:** 이 비밀의 `Name`을 설정합니다. 이름은 자격 증명 엔터티 구성에서 사용되며, 값은 **2단계** 의 서비스 주체 `Client Secret Value`여야 합니다.

    ![secret2: sp 클라이언트 비밀](../media/credential-entity/secret-2-sp-secret-value.png)

이제 서비스 주체의 ‘클라이언트 ID’와 ‘클라이언트 비밀’이 Key Vault에 저장되었습니다.  다음으로, 키 자격 증명 모음을 저장할 다른 서비스 주체를 만들어야 합니다. 따라서 **두 개의 서비스 주체를 만들어야** 합니다. 하나는 키 자격 증명 모음에 저장되는 클라이언트 ID와 클라이언트 비밀을 저장하는 데 사용되고, 다른 하나는 키 자격 증명 모음을 저장하는 데 사용됩니다.

**5단계. 키 자격 증명 모음을 저장할 서비스 주체를 만듭니다.** 

1. [Azure Portal AAD(Azure Active Directory)](https://portal.azure.com/?trace=diagnostics&feature.customportal=false#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)로 이동하여 새 등록을 만듭니다.

    ![새 등록 만들기](../media/credential-entity/create-registration.png)

    서비스 주체를 만든 후 개요의 **애플리케이션(client) ID** 는 자격 증명 엔터티 구성의 `Key Vault Client ID`가 됩니다.

2. **관리->인증서 및 비밀** 에서 ‘새 클라이언트 비밀’을 선택하여 클라이언트 비밀을 만듭니다. 이 값은 한 번만 표시되므로 **값을 적어 두어야** 합니다. 해당 값이 자격 증명 엔터티 구성의 `Key Vault Client Secret`입니다.

    ![클라이언트 비밀 추가](../media/credential-entity/add-client-secret.png)

**6단계. Key Vault에 대한 액세스 권한을 서비스 주체에 부여합니다.**  만든 키 자격 증명 모음 리소스로 이동한 다음, **설정->액세스 정책** 에서 ‘액세스 정책 추가’를 선택하여 키 자격 증명 모음과 **5단계** 의 두 번째 서비스 주체 간 연결을 설정하고 ‘저장’을 선택합니다.

![sp에 키 자격 증명 모음에 대한 액세스 권한 부여](../media/credential-entity/grant-sp-to-kv.png)


## <a name="configurations-conclusion"></a>구성 정리
끝으로, 아래 표에는 ‘Key Vault의 서비스 주체’에 대한 Metrics Advisor의 자격 증명 엔터티 구성과 구성 방법이 나와 있습니다.

| 구성 | 구성 방법 |
|-------------| ---------------------|
| Key Vault 엔드포인트 | **3단계:** 키 자격 증명 모음의 자격 증명 모음 URI입니다. |
| 테넌트 ID | **1단계:** 첫 번째 서비스 주체의 디렉터리(테넌트) ID입니다. |
| Key Vault 클라이언트 ID | **5단계:** 두 번째 서비스 주체의 애플리케이션(클라이언트) ID입니다. |
| Key Vault 클라이언트 비밀 | **5단계:** 두 번째 서비스 주체의 클라이언트 비밀 값입니다. |
| 서비스 주체 클라이언트 ID 이름 | **4단계:** 클라이언트 ID에 대해 설정한 비밀 이름입니다. |
| 서비스 주체 클라이언트 비밀 이름 | **4단계:** 클라이언트 비밀 값에 대해 설정한 비밀 이름입니다. |


## <a name="next-steps"></a>다음 단계

- [데이터 온보딩](onboard-your-data.md)
- [여러 데이터 원본 연결](../data-feeds-from-different-sources.md)
