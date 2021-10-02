---
title: 워크플로에서 B2B 메시지를 보호 하는 인증서 추가
description: Azure Logic Apps 및 엔터프라이즈 통합 팩를 사용 하 여 워크플로에서 B2B 메시지를 보호 하기 위해 통합 계정에 인증서를 추가 합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/23/2021
ms.openlocfilehash: b68bc9f1f77c6ad5b60cd5b2ea9c4b644acb4c5b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361048"
---
# <a name="add-certificates-to-integration-accounts-for-securing-messages-in-workflows-with-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 워크플로에서 메시지 보안 설정에 대 한 통합 계정에 인증서 추가

논리 앱 B2B (기업 간) 워크플로에서 기밀 메시지를 교환 해야 하는 경우 인증서를 사용 하 여이 통신의 보안을 강화할 수 있습니다. 인증서는 다음과 같은 방법으로 통신을 보호 하는 데 도움이 되는 디지털 문서입니다.

* 전자 통신에서 참가자의 id를 확인 합니다.

* 메시지 내용을 암호화 합니다.

* 메시지에 디지털 서명 합니다.

워크플로에서 다음 인증서 유형을 사용할 수 있습니다.

* 공용 [인증서](https://en.wikipedia.org/wiki/Public_key_certificate)-공용 인터넷 [인증 기관 (CA)](https://en.wikipedia.org/wiki/Certificate_authority)에서 구입 해야 합니다. 이러한 인증서에는 키가 필요 하지 않습니다.

* 사용자가 만들고 발급 하는 개인 인증서 또는 [*자체 서명 된 인증서*](https://en.wikipedia.org/wiki/Self-signed_certificate). 그러나 이러한 인증서에는 개인 키가 필요 합니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 검토하세요. b2b 엔터프라이즈 통합에 대 한 자세한 내용은 [Azure Logic Apps 및 엔터프라이즈 통합 팩를 사용 하 여 b2b 엔터프라이즈 통합 워크플로](logic-apps-enterprise-integration-overview.md)를 검토 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용 하기 위해 거래 업체, 규약, 인증서 등의 아티팩트를 정의 하 고 저장 하는 [통합 계정 리소스](logic-apps-enterprise-integration-create-integration-account.md) 입니다. 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 는 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 있습니다.

  * [ **논리 앱 (소비)** 리소스 유형을](logic-apps-overview.md#resource-type-and-host-environment-differences)사용 하는 경우 워크플로에 아티팩트를 사용 하려면 먼저 [통합 계정을 논리 앱 리소스에 연결](logic-apps-enterprise-integration-create-integration-account.md#link-account) 해야 합니다.

    **논리 앱 (소비)** 워크플로에서 사용할 인증서를 만들고 추가 하려면 아직 논리 앱 리소스가 필요 하지 않습니다. 그러나 워크플로에서 이러한 인증서를 사용할 준비가 되 면 논리 앱 리소스에는 해당 인증서를 저장 하는 연결 된 통합 계정이 필요 합니다.

  * [ **논리 앱 (표준)** 리소스 종류](logic-apps-overview.md#resource-type-and-host-environment-differences)를 사용 하는 경우 통합 계정에 논리 앱 리소스에 대 한 링크가 필요 하지는 않지만, [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md)및 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업을 사용 하는 것과 함께 파트너, 규약 및 인증서와 같은 다른 아티팩트를 저장 하는 데에도 필요 합니다. 통합 계정은 동일한 Azure 구독을 사용 하 고 논리 앱 리소스와 동일한 위치에 있는 다른 요구 사항을 충족 해야 합니다.

    > [!NOTE]
    > 현재 **논리 앱 (소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원 합니다. **논리 앱 (표준)** 리소스 유형은 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 포함 하지 않습니다.

* 개인 인증서의 경우 다음 필수 구성 요소를 충족 해야 합니다.

  * [Azure Key Vault](../key-vault/general/overview.md) 에 개인 키를 추가 하 고 **키 이름을** 입력 합니다. 자세한 내용은 [Azure Key Vault에 개인 키 추가](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)를 참조 하세요.

  * 키 자격 증명 모음에서 작업을 수행 하도록 Azure Logic Apps 서비스에 권한을 부여 합니다. Azure Logic Apps 서비스 주체에 대 한 액세스 권한을 부여 하려면 PowerShell 명령 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)를 사용 합니다. 예를 들면 다음과 같습니다.

    `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

    [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

  * 키 자격 증명 모음에 [해당 공용 인증서를 추가](#add-public-certificate) 합니다. 이 인증서 [는 메시지 서명 및 암호화에 대 한 규약의 **송신** 및 **수신** 설정](logic-apps-enterprise-integration-agreements.md)에 표시 됩니다. 예를 들어 [Azure Logic Apps에서 AS2 메시지 설정에 대 한 참조](logic-apps-enterprise-integration-as2-message-settings.md)를 검토 합니다.

* 두 개 이상의 [거래 업체](logic-apps-enterprise-integration-partners.md) 와 통합 계정에서 [해당 파트너 간의 규약](logic-apps-enterprise-integration-agreements.md) . 규약에는 호스트 파트너와 게스트 파트너가 필요 합니다. 또한 규약은 두 파트너가 모두 AS2, X12, EDIFACT 또는 RosettaNet 규약에 적절 한 동일한 또는 호환 *비즈니스 id* 한정자를 사용 하도록 요구 합니다.

* 필요에 따라 인증서를 사용 하려는 논리 앱 리소스 및 워크플로입니다. 워크플로에는 논리 앱의 워크플로를 시작 하는 트리거가 필요 합니다. 이전에 논리 앱 워크플로를 만들지 않은 경우 빠른 시작 [: 첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)를 검토 합니다.

<a name="add-public-certificate"></a>

## <a name="add-a-public-certificate"></a>공용 인증서 추가

워크플로에서 *공용 인증서* 를 사용 하려면 먼저 통합 계정에 인증서를 추가 해야 합니다.

1. [Azure Portal](https://portal.azure.com) 검색 상자에를 입력 하 `integration accounts` 고 **통합 계정** 을 선택 합니다.

1. **통합 계정** 에서 인증서를 추가 하려는 통합 계정을 선택 합니다.

1. 통합 계정 메뉴의 **설정** 에서 **인증서** 를 선택 합니다.

1. **인증서** 창에서 **추가** 를 선택 합니다.

1. **인증서 추가** 창에서 인증서에 대 한 다음 정보를 제공 합니다.

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | <*certificate-name*> | 인증서의 이름 ( `publicCert` 이 예제에서는) |
   | **인증서 종류** | 예 | **공용** | 인증서의 종류입니다. |
   | **MSSQLSERVER에 대한 프로토콜 속성** | 예 | <*certificate-file-name*> | 추가 하려는 인증서 파일을 찾아보려면 **인증서** 상자 옆에 있는 폴더 아이콘을 선택 합니다. |
   |||||

   !["추가"가 선택 된 Azure Portal 및 통합 계정과 공용 인증서 세부 정보를 포함 하는 "인증서 추가" 창이 표시 된 스크린샷](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

1. 완료되면 **확인** 을 선택합니다.

   Azure에서는 사용자의 선택 사항이 유효한지 확인한 후 인증서를 업로드합니다.

   !["인증서" 목록의 공용 인증서를 사용 하 여 Azure Portal 및 통합 계정을 보여 주는 스크린샷](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png)

<a name="add-public-certificate"></a>

## <a name="add-a-private-certificate"></a>개인 인증서 추가

워크플로에서 *개인 인증서* 를 사용 하려면 먼저 통합 계정에 인증서를 추가 해야 합니다. 또한 [필수 구성 요소 개인 인증서](#prerequisites)를 충족 하는지 확인 합니다.

1. [Azure Portal](https://portal.azure.com) 검색 상자에를 입력 하 `integration accounts` 고 **통합 계정** 을 선택 합니다.

1. **통합 계정** 에서 인증서를 추가 하려는 통합 계정을 선택 합니다.

1. 통합 계정 메뉴의 **설정** 에서 **인증서** 를 선택 합니다.

1. **인증서** 창에서 **추가** 를 선택 합니다.

1. **인증서 추가** 창에서 인증서에 대 한 다음 정보를 제공 합니다.

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | <*certificate-name*> | 인증서의 이름 ( `privateCert` 이 예제에서는) |
   | **인증서 종류** | 예 | **개인** | 인증서의 종류입니다. |
   | **MSSQLSERVER에 대한 프로토콜 속성** | 예 | <*certificate-file-name*> | 추가 하려는 인증서 파일을 찾아보려면 **인증서** 상자 옆에 있는 폴더 아이콘을 선택 합니다. 개인 키를 포함 하는 주요 자격 증명 모음에서 추가 하는 파일은 공용 인증서입니다. |
   | **리소스 그룹** | 예 | <*integration-account-resource-group*> | 통합 계정의 리소스 그룹 ( `Integration-Account-RG` 이 예제에서는) |
   | **Key Vault** | 예 | <*key-vault-name*> | 키 자격 증명 모음 이름 |
   | **키 이름** | 예 | <*key-name*> | 키 이름 |
   |||||

   !["추가"가 선택되고 프라이빗 인증서 세부 정보가 있는 "인증서 추가" 창이 있는 Azure Portal 및 통합 계정을 보여주는 스크린샷](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

1. 완료되면 **확인** 을 선택합니다.

   Azure에서는 사용자의 선택 사항이 유효한지 확인한 후 인증서를 업로드합니다.

   !["인증서" 목록에서 프라이빗 인증서가 있는 Azure Portal 및 통합 계정을 보여주는 스크린샷.](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png)

## <a name="next-steps"></a>다음 단계

* [AS2 메시지 교환](logic-apps-enterprise-integration-as2.md)
* [EDIFACT 메시지 교환](logic-apps-enterprise-integration-edifact.md)
* [X12 메시지 교환](logic-apps-enterprise-integration-x12.md)
* [Exchange RosettaNet 메시지](logic-apps-enterprise-integration-rosettanet.md)
