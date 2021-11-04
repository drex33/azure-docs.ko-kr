---
title: 미사용 데이터의 사용자 지정 명령 서비스 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 사용자 지정 명령 암호화
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: c1b057b8bfaacc0f2f26dcf8688d7439e6506c8b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502790"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>미사용 데이터의 사용자 지정 명령 암호화

사용자 지정 명령은 클라우드에 데이터를 저장할 때 자동으로 암호화합니다. 사용자 지정 명령 서비스 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정에 부합하는 데 도움이 됩니다.

> [!NOTE]
> 사용자 지정 명령 서비스는 애플리케이션과 연결된 LUIS 리소스에 대해 암호화를 자동으로 사용하지 않습니다. 필요한 경우 [여기](../luis/encrypt-data-at-rest.md)에서 LUIS 리소스에 대한 암호화를 사용하도록 설정해야 합니다.

## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보
데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2)를 준수하는 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화 및 암호 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 자동으로 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

사용자 지정 명령을 사용하는 경우 Speech Service는 클라우드에 다음 데이터를 저장합니다.
* 사용자 지정 명령 애플리케이션 뒤의 구성 JSON
* LUIS 작성 및 예측 키

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 그러나 사용자 고유의 암호화 키를 사용하여 구독을 관리할 수도 있습니다. CMK(고객 관리 키)(BYOK(Bring Your Own Key)라고도 함)를 사용하여 훨씬 더 유연하게 액세스 제어를 만들고, 회전하고, 해제하고, 취소할 수 있습니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.


> [!IMPORTANT]
> 고객 관리형 키는 2020년 6월 27일 이후에 생성된 리소스만 사용할 수 있습니다. Speech Service에 CMK를 사용하려면 새 음성 리소스를 만들어야 합니다. 리소스가 생성되면 Azure Key Vault를 사용하여 관리 ID를 설정할 수 있습니다.

고객 관리형 키를 사용하는 기능을 요청하려면 고객 관리형 키 요청 양식을 작성하여 제출합니다. 요청 상태에 대한 답변을 받는 데는 영업일 기준 약 3~5일이 소요됩니다. 요청에 따라 공간이 확보되면 큐에 배치되고 승인될 수 있습니다. Speech Service에 CMK를 사용하도록 승인되면 Azure Portal에서 새 음성 리소스를 만들어야 합니다.
   > [!NOTE]
   > **CMK(고객 관리형 키)는 사용자 지정 명령에만 지원됩니다.**
   >
   >  **Custom Speech 및 Custom Voice는 여전히 BYOS(Bring Your Own Storage)를 지원합니다.**  [자세히 알아보기](speech-encryption-of-data-at-rest.md)
   >
   > 제공된 음성 리소스를 사용하여 이러한 서비스에 액세스하는 경우 BYOS를 명시적으로 구성하여 규정 준수 요구 사항을 충족해야 합니다.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용하는 고객 관리형 키

고객 관리형 키를 저장하려면 Azure Key Vault를 사용해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. 음성 리소스 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure AD(Azure Active Directory) 테넌트에 있어야 하지만 서로 다른 구독에 있을 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../../key-vault/general/overview.md)을 참조하세요.

새 음성 리소스가 만들어지고 사용자 지정 명령을 프로비전하는 데 사용되는 경우, Microsoft 관리형 키를 사용하여 데이터가 항상 암호화됩니다. 리소스를 만들 때는 고객 관리형 키를 사용하도록 설정할 수 없습니다. 고객 관리형 키는 Azure Key Vault에 저장되며, 키 자격 증명 모음은 Cognitive Services 리소스와 연결된 관리 ID에 키 권한을 부여하는 액세스 정책을 사용하여 프로비전되어야 합니다. 관리 ID는 CMK에 필요한 가격 책정 계층을 사용하여 리소스를 만든 후에만 사용할 수 있습니다.

고객 관리형 키를 사용하도록 설정하면 Azure AD의 기능인 시스템 할당 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)도 사용할 수 있습니다. 시스템 할당 관리 ID를 사용하도록 설정하면 이 리소스가 Azure Active Directory에 등록됩니다. 등록 후 관리 ID에는 고객 관리형 키를 설정하는 동안 선택된 키 자격 증명 모음에 대한 액세스 권한이 부여됩니다. 

> [!IMPORTANT]
> 시스템 할당 관리 ID를 사용하지 않도록 설정하면 키 자격 증명 모음에 대한 액세스 권한이 제거되고 고객 키로 암호화된 데이터에 더 이상 액세스할 수 없게 됩니다. 이 데이터에 의존하는 기능은 작동하지 않습니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 자동 할당됩니다. 이후에 구독, 리소스 그룹 또는 리소스를 Azure AD 디렉터리 간에 이동하는 경우, 리소스와 연결된 관리 ID가 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 FAQ에서 **Azure AD 디렉터리 간 구독 전송** 및 [Azure 리소스에 대한 관리 ID의 알려진 문제](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)를 참조하세요.  

## <a name="configure-azure-key-vault"></a>Azure Key Vault 구성

고객 관리형 키를 사용하려면 Key Vault에서 **일시 삭제** 및 **삭제 안 함** 속성을 설정해야 합니다. 두 속성은 기본적으로 설정되어 있지 않지만 새로운 또는 기존 키 자격 증명 모음에서 PowerShell 또는 Azure CLI를 사용하여 설정할 수 있습니다.

> [!IMPORTANT]
> **일시 삭제** 및 **삭제 안 함** 속성이 설정되어 있지 않은 상태에서 키를 삭제하면 Cognitive Service 리소스의 데이터를 복구할 수 없습니다.

기존 키 자격 증명 모음에서 이러한 속성을 사용하려면 다음 문서 중 하나에서 **일시 삭제를 사용하도록 설정** 및 **제거 보호 활성화** 섹션을 참조하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)
- [CLI에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)

Azure Storage 암호화에는 2048 크기의 RSA 키만 지원됩니다. 키에 대한 자세한 내용은 [Azure Key Vault 키, 비밀 및 인증서 정보](../../key-vault/general/about-keys-secrets-certificates.md)의 **Key Vault 키** 를 참조하세요.

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>음성 리소스에 고객 관리형 키 사용

Azure Portal에서 고객 관리형 키를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 음성 리소스로 이동합니다.
1. 음성 리소스의 **설정** 블레이드에서 **암호화** 를 클릭합니다. 다음 그림에 표시된 것처럼 **고객 관리형 키** 옵션을 선택합니다.

 ![고객 관리형 키를 선택하는 방법을 보여 주는 스크린샷](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>키 지정

고객 관리형 키를 사용하도록 설정하면 Cognitive Services 리소스와 연결할 키를 지정할 수 있습니다.

### <a name="specify-a-key-as-a-uri"></a>키를 URI로 지정

키를 URI로 지정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 키 URI를 찾으려면 키 자격 증명 모음으로 이동하여 **키** 설정을 선택합니다. 원하는 키를 선택한 다음, 키를 클릭하여 버전을 확인합니다. 키 버전을 선택하여 해당 버전의 설정을 봅니다.
1. URI를 제공하는 **키 식별자** 필드의 값을 복사합니다.

    ![키 자격 증명 모음 키 URI를 보여 주는 스크린샷](../media/cognitive-services-encryption/key-uri-portal.png)

1. Speech Service에 대한 **암호화** 설정에서 **키 URI 입력** 옵션을 선택합니다.
1. 복사한 URI를 **키 URI** 필드에 붙여넣습니다.

1. 키 자격 증명 모음을 포함하는 구독을 지정합니다.
1. 변경 내용을 저장합니다.

### <a name="specify-a-key-from-a-key-vault"></a>Key Vault에서 키 지정

키 자격 증명 모음의 키를 지정하려면 먼저 키가 포함된 키 자격 증명 모음이 있는지 확인합니다. 키 자격 증명 모음의 키를 지정하려면 다음 단계를 수행합니다.

1. **Key Vault에서 선택** 옵션을 선택합니다.
1. 사용하려는 키가 포함된 키 자격 증명 모음을 선택합니다.
1. 키 자격 증명 모음의 키를 선택합니다.

   ![고객 관리형 키 옵션을 보여 주는 스크린샷](media/custom-commands/configure-cmk-fromKV.png)

1. 변경 내용을 저장합니다.

## <a name="update-the-key-version"></a>키 버전 업데이트

새 버전 키를 만들 때 새 버전을 사용하도록 음성 리소스를 업데이트해야 합니다. 다음 단계를 수행합니다.

1. 음성 리소스로 이동하여 **암호화** 설정을 표시합니다.
1. 새 키 버전의 URI를 입력합니다. 또는 키 자격 증명 모음을 선택하고 키를 다시 선택하여 버전을 업데이트할 수 있습니다.
1. 변경 내용을 저장합니다.

## <a name="use-a-different-key"></a>다른 키 사용

암호화에 사용되는 키를 변경하려면 다음 단계를 수행합니다.

1. 음성 리소스로 이동하여 **암호화** 설정을 표시합니다.
1. 새 키의 URI를 입력합니다. 또는 키 자격 증명 모음을 선택하고 새 키를 선택할 수 있습니다.
1. 변경 내용을 저장합니다.

## <a name="rotate-customer-managed-keys"></a>고객 관리형 키 순환

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리형 키를 순환할 수 있습니다. 키를 순환하는 경우 새 키 URI를 사용하도록 음성 리소스를 업데이트해야 합니다. Azure Portal에서 새 버전의 키를 사용하도록 리소스를 업데이트하는 방법을 알아보려면 [키 버전 업데이트](#update-the-key-version)를 참조하세요.

키를 순환해도 리소스에서 데이터의 재암호화가 트리거되지 않습니다. 사용자의 추가 작업은 필요하지 않습니다.

## <a name="revoke-access-to-customer-managed-keys"></a>고객 관리형 키에 대한 액세스 권한 철회

고객 관리형 키에 대한 액세스 권한을 철회하려면 PowerShell 또는 Azure CLI를 사용합니다. 자세한 내용은 [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) 또는 [Azure Key Vault CLI](/cli/azure/keyvault)를 참조하세요. 액세스 권한을 철회하면 Cognitive Services에서 암호화 키에 액세스할 수 없으므로 Cognitive Services 계정의 모든 데이터에 대한 액세스가 효과적으로 차단됩니다.

## <a name="disable-customer-managed-keys"></a>고객 관리형 키 사용 안 함

고객 관리형 키를 사용하지 않도록 설정하면 음성 리소스가 Microsoft 관리형 키를 사용하여 암호화됩니다. 고객 관리형 키를 사용하지 않도록 설정하려면 다음 단계를 수행합니다.

1. 음성 리소스로 이동하여 **암호화** 설정을 표시합니다.
1. **사용자 자신의 키 사용** 설정 옆에 있는 확인란의 선택을 취소합니다.

## <a name="next-steps"></a>다음 단계

* [음성 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)
* [관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)
