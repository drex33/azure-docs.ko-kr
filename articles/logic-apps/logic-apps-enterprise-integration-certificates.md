---
title: 워크플로에서 B2B 메시지를 보호하는 인증서 추가
description: Enterprise 통합 팩을 사용하여 Azure Logic Apps 워크플로에 대한 B2B 메시지를 보호하는 인증서를 추가합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/23/2021
ms.openlocfilehash: e2d2bc58c5a523667eef62b21791e953701f7bc1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550085"
---
# <a name="secure-messages-using-certificates-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps 워크플로에 대한 인증서를 사용하여 메시지 보호

논리 앱 B2B(Business-to-Business) 워크플로에서 기밀 메시지를 교환해야 하는 경우 인증서를 사용하여 이 통신에 대한 보안을 강화할 수 있습니다. 인증서는 다음과 같은 방법으로 통신을 보호하는 데 도움이 되는 디지털 문서입니다.

* 전자 통신에서 참가자의 ID를 확인합니다.

* 메시지 콘텐츠를 암호화합니다.

* 메시지에 디지털 서명합니다.

워크플로에서 다음 인증서 유형을 사용할 수 있습니다.

* [공용 인증서 -](https://en.wikipedia.org/wiki/Public_key_certificate)공용 [CA(인터넷 인증 기관)에서](https://en.wikipedia.org/wiki/Certificate_authority)구입해야 합니다. 이러한 인증서에는 키가 필요하지 않습니다.

* 직접 만들고 발급하는 프라이빗 인증서 또는 [*자체 서명된 인증서.*](https://en.wikipedia.org/wiki/Self-signed_certificate) 그러나 이러한 인증서에는 프라이빗 키가 필요합니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 검토하세요. B2B 엔터프라이즈 통합에 대한 자세한 내용은 [Azure Logic Apps 및 Enterprise 통합 팩을 통해 B2B 엔터프라이즈 통합 워크플로를](logic-apps-enterprise-integration-overview.md)검토하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * [ **논리 앱(소비)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 워크플로에서 아티팩트 사용을 위해 통합 계정을 논리 앱 [리소스에 연결해야](logic-apps-enterprise-integration-create-integration-account.md#link-account) 합니다.

    **논리 앱(소비)** 워크플로에서 사용할 인증서를 만들고 추가하려면 논리 앱 리소스가 아직 필요하지 않습니다. 그러나 워크플로에서 해당 인증서를 사용할 준비가 되면 논리 앱 리소스에 해당 인증서를 저장하는 연결된 통합 계정이 필요합니다.

  * [ **논리 앱(표준)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 통합 계정에 논리 앱 리소스에 대한 링크가 필요하지 않지만 [AS2,](logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)및 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업과 함께 파트너, 계약 및 인증서와 같은 다른 아티팩트를 저장해야 합니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

    > [!NOTE]
    > 현재 **논리 앱(소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원합니다. **논리 앱(표준)** 리소스 종류에는 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업이 포함되지 않습니다.

* 프라이빗 인증서의 경우 다음 필수 구성을 충족해야 합니다.

  * [Azure Key Vault](../key-vault/general/overview.md) 프라이빗 키를 추가하고 키 이름을 **갖습니다.** 자세한 내용은 [Azure Key Vault 프라이빗 키 추가를 검토하세요.](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)

  * 키 자격 증명 모음에서 작업을 수행하도록 Azure Logic Apps 서비스에 권한을 부여합니다. Azure Logic Apps 서비스 주체에 대한 액세스 권한을 부여하려면 PowerShell 명령 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)를 사용합니다. 예를 들면 다음과 같습니다.

    `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

    [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

  * [해당 공용 인증서를](#add-public-certificate) 키 자격 증명 모음에 추가합니다. 이 인증서는 메시지 서명 [및 암호화를 위한 계약의 **송/수신** 설정에](logic-apps-enterprise-integration-agreements.md)표시됩니다. 예를 들어 [Azure Logic Apps AS2 메시지 설정에 대한 참조를](logic-apps-enterprise-integration-as2-message-settings.md)검토합니다.

* 통합 계정에서 두 개 이상의 [거래 업체와](logic-apps-enterprise-integration-partners.md) [해당 파트너 간의 계약입니다.](logic-apps-enterprise-integration-agreements.md) 계약에는 호스트 파트너 및 게스트 파트너가 필요합니다. 또한 계약에 따라 두 파트너 모두 AS2, X12, EDIFACT 또는 RosettaNet 계약에 적합한 동일하거나 호환되는 *비즈니스 ID* 한정자를 사용해야 합니다.

* 필요에 따라 인증서를 사용하려는 논리 앱 리소스 및 워크플로입니다. 워크플로에는 논리 앱의 워크플로를 시작하는 트리거가 필요합니다. 이전에 논리 앱 워크플로를 만들지 않은 경우 [빠른 시작: 첫 번째 논리 앱 만들기를 검토합니다.](quickstart-create-first-logic-app-workflow.md)

<a name="add-public-certificate"></a>

## <a name="add-a-public-certificate"></a>공용 인증서 추가

워크플로에서 *공용 인증서를* 사용하려면 먼저 통합 계정에 인증서를 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com) 검색 상자에 `integration accounts` 를 입력하고 통합 **계정 을 선택합니다.**

1. **통합 계정에서 인증서를** 추가할 통합 계정을 선택합니다.

1. 통합 계정 메뉴의 **설정** 아래에서 **인증서를 선택합니다.**

1. **인증서** 창에서 **추가를** 선택합니다.

1. 인증서 추가 창에서 **인증서에** 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | <*certificate-name*> | `publicCert`인증서 이름(이 예제에 포함) |
   | **인증서 종류** | Yes | **공용** | 인증서의 종류입니다. |
   | **MSSQLSERVER에 대한 프로토콜 속성** | Yes | <*certificate-file-name*> | 추가하려는 인증서 파일을 찾아보려면 인증서 **상자** 옆에 있는 폴더 아이콘을 선택합니다. |
   |||||

   !["추가"가 선택되고 공용 인증서 세부 정보가 있는 "인증서 추가" 창이 있는 Azure Portal 및 통합 계정을 보여주는 스크린샷](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

1. 완료되면 **확인** 을 선택합니다.

   Azure에서는 사용자의 선택 사항이 유효한지 확인한 후 인증서를 업로드합니다.

   !["인증서" 목록에서 공용 인증서가 있는 Azure Portal 및 통합 계정을 보여주는 스크린샷.](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png)

<a name="add-public-certificate"></a>

## <a name="add-a-private-certificate"></a>프라이빗 인증서 추가

워크플로에서 *프라이빗 인증서를* 사용하려면 먼저 통합 계정에 인증서를 추가해야 합니다. 필수 구성 조건 프라이빗 인증서도 충족하는지 [확인합니다.](#prerequisites)

1. [Azure Portal](https://portal.azure.com) 검색 상자에 `integration accounts` 를 입력하고 통합 **계정 을 선택합니다.**

1. **통합 계정에서 인증서를** 추가할 통합 계정을 선택합니다.

1. 통합 계정 메뉴의 **설정** 아래에서 **인증서를 선택합니다.**

1. **인증서** 창에서 **추가를** 선택합니다.

1. 인증서 추가 창에서 **인증서에** 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | <*certificate-name*> | `privateCert`인증서 이름(이 예제에 포함) |
   | **인증서 종류** | Yes | **개인** | 인증서의 종류입니다. |
   | **MSSQLSERVER에 대한 프로토콜 속성** | Yes | <*certificate-file-name*> | 추가하려는 인증서 파일을 찾아보려면 인증서 **상자** 옆에 있는 폴더 아이콘을 선택합니다. 프라이빗 키가 포함된 키 자격 증명 모음에 추가하는 파일에는 공용 인증서가 있습니다. |
   | **리소스 그룹** | 예 | <*integration-account-resource-group*> | 이 예제에 있는 통합 계정의 리소스 그룹 `Integration-Account-RG` |
   | **Key Vault** | 예 | <*key-vault-name*> | 키 자격 증명 모음 이름 |
   | **키 이름** | Yes | <*key-name*> | 사용자의 키 이름 |
   |||||

   !["추가"가 선택 된 Azure Portal 및 통합 계정과 개인 인증서 세부 정보를 포함 하는 "인증서 추가" 창이 표시 된 스크린샷](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

1. 완료되면 **확인** 을 선택합니다.

   Azure에서는 사용자의 선택 사항이 유효한지 확인한 후 인증서를 업로드합니다.

   !["인증서" 목록의 개인 인증서를 사용 하 여 Azure Portal 및 통합 계정을 보여 주는 스크린샷](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png)

## <a name="next-steps"></a>다음 단계

* [AS2 메시지 교환](logic-apps-enterprise-integration-as2.md)
* [EDIFACT 메시지 교환](logic-apps-enterprise-integration-edifact.md)
* [X12 메시지 교환](logic-apps-enterprise-integration-x12.md)
* [Exchange RosettaNet 메시지](logic-apps-enterprise-integration-rosettanet.md)