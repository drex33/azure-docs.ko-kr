---
title: Azure Sentinel | Azure Key Vault Honeytoken 배포 및 모니터링 Microsoft Docs
description: 허니 토큰 키와 비밀을 Azure Key Vault Azure Sentinel 모니터링합니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2021
ms.author: bagol
ms.openlocfilehash: 23b7ef6b159438bb171ab4326652c81796bcaf91
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990099"
---
# <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-azure-sentinel-public-preview"></a>Azure Sentinel Azure Key Vault honeytoken 배포 및 모니터링(공개 미리 보기)

> [!IMPORTANT]
> Azure Sentinel Deception(허니 토큰) 솔루션은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

이 문서에서는 **Azure Sentinel Deception(허니 토큰) 솔루션을** 사용하여 *honeytoken이라고* 하는 디코이 [Azure Key Vault](/azure/key-vault/) 키와 비밀을 기존 워크로드에 심는 방법을 설명합니다.

솔루션에서 제공하는 [분석 규칙,](detect-threats-built-in.md) [감시 목록](watchlists.md)및 [통합 문서를](monitor-your-data.md) 사용하여 배포된 Honeytoken에 대한 액세스를 모니터링합니다.

시스템에서 Honeytoken을 사용하는 경우 검색 원칙은 동일하게 유지됩니다. Honeytoken에 액세스할 합법적인 이유가 없기 때문에 모든 활동은 환경에 익숙하지 않고 공격자가 될 수 있는 사용자의 존재를 나타냅니다.

## <a name="before-you-begin"></a>시작하기 전에

**Azure Sentinel Deception(허니 토큰)** 솔루션 사용을 시작하려면 다음이 있는지 확인합니다.

- **필수 역할:** **Azure Sentinel Deception(허니 토큰)** 솔루션을 설치하려면 테넌트 관리자여야 합니다. 솔루션이 설치되면 키 자격 증명 모음 소유자와 통합 문서를 공유하여 자체 Honeytoken을 배포할 수 있습니다.

- **필수 데이터 커넥터:** 작업 영역에 [Azure Key Vault](data-connectors-reference.md#azure-key-vault) 및 [Azure 활동](data-connectors-reference.md#azure-activity) 데이터 커넥터를 배포했고 연결되었는지 확인합니다.

    데이터 라우팅이 성공했는지, **KeyVault** 및 **AzureActivity** 데이터가 Azure Sentinel 들어오는지 확인합니다. 자세한 내용은 다음을 참조하세요.

    - [Azure Sentinel을 Azure, Windows, Microsoft 및 Amazon 서비스에 연결](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)
    - [Azure Sentinel 데이터 커넥터 찾기](data-connectors-reference.md)

## <a name="install-the-solution"></a>솔루션 설치

다른 솔루션과 마찬가지로 **Azure Sentinel Deception(허니 토큰)** [솔루션을 설치합니다.](sentinel-solutions-deploy.md) Azure Sentinel **Deception** 솔루션 페이지에서 **시작을** 선택하여 시작합니다.

:::image type="content" source="media/monitor-key-vault-honeytokens/honeytoken-create-solution.png" alt-text="솔루션 만들기 페이지의 스크린샷.":::

**Deception 솔루션을 설치하려면:**

다음 단계에서는 **Azure Sentinel Deception(허니 토큰)** 솔루션에 필요한 특정 작업을 설명합니다.

1. 기본 **탭에서** Azure Sentinel 작업 영역이 있는 동일한 리소스 그룹을 선택합니다.

1. 필수 **조건** 탭의 **함수 앱 이름** 필드에 키 자격 증명 모음에 Honeytoken을 만들 Azure 함수 앱에 대한 의미 있는 이름을 입력합니다.

    함수 앱 이름은 길이가 2-22자 사이이고 영숫자 문자만 고유해야 합니다.

    아래에는 정의한 이름으로 명령이 표시됩니다. 다음은 그 예입니다.

    :::image type="content" source="media/monitor-key-vault-honeytokens/prerequisites.png" alt-text="업데이트된 curl 명령을 보여주는 필수 구성 조건 탭의 스크린샷.":::

1. **Cloud Shell을 열려면 여기를 클릭을** 선택하여 Cloud Shell 탭을 엽니다. 메시지가 표시되면 로그인한 다음, 표시된 명령을 실행합니다. 

    실행하는 스크립트는 허니 토큰을 배포하는 Azure AD(AAD) 함수 앱을 만듭니다.    다음은 그 예입니다.

    ```bash
    Requesting a Cloud Shell.Succeeded
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    maria@Azure:~$curl -sL https://aka.ms/sentinelhoneytokensappcreate | bash -s HoneyTokenFunctionApp
    ```

     스크립트 출력에는 AAD 앱 ID 및 비밀이 포함됩니다. 다음은 그 예입니다.

    ```bash
    WARNING: The output includes credentials that you must protect. Be sure that you do not include these credentials in your code or check the credentials into your source control. For more information, see https://aka.ms/azadsp-cli
    function app name: HoneyTokenFunctionApp
    AAD App Id: k4js48k3-97gg-3958=sl8d=48620nne59k4
    AAD App secret: v9kUtSoy3u~K8DKa8DlILsCM_K-s9FR3Kj
    maria@Azure:~$
    ```

1. Azure Sentinel **돌아가서 필수 구성 조건** 탭 아래쪽에서 관련 필드에 AAD 앱 ID 및 비밀을 입력합니다. 다음은 그 예입니다.

    :::image type="content" source="media/monitor-key-vault-honeytokens/client-app-secret-values.png" alt-text="함수 앱의 클라이언트 앱 및 추가된 비밀 값의 스크린샷.":::

1. 4단계에서 **함수 앱 설정을 계속하려면 여기를 클릭하세요를** 선택합니다. Azure AD 애플리케이션 설정에서 새 브라우저 탭이 열립니다.

    메시지가 표시되면 로그인한 다음, **관리자 `<your directory name>` 동의 허용을** 선택하여 계속합니다. 다음은 그 예입니다.

    :::image type="content" source="media/monitor-key-vault-honeytokens/grant-admin-access.png" alt-text="디렉터리에 대한 관리자 동의 부여 단추의 스크린샷.":::

    자세한 내용은 [앱 등록 관리자 동의 부여를](/azure/active-directory/manage-apps/grant-admin-consent)참조하세요.

1. 다시 Azure Sentinel **통합 문서,** **분석,** **감시 목록** 및 **플레이북** 탭에서 생성될 보안 콘텐츠를 메모하고 필요에 따라 이름을 수정합니다.

    > [!NOTE]
    > 이 문서의 다른 지침은 **HoneyTokensIncidents** 및 **SOCHTManagement** 통합 문서를 참조합니다. 이러한 통합 문서의 이름을 변경하는 경우 사용자 고유의 참조에 대한 새 통합 문서 이름을 확인하고 기본 이름 대신 필요에 따라 사용해야 합니다.
    >

1. **Azure Functions** 탭에서 다음 값을 정의합니다.

    **키 자격 증명** 모음 구성: 다음 필드는 AAD 앱의 비밀을 저장할 키 자격 증명 모음에 대한 값을 정의합니다. 이러한 필드는 Honeytoken을 배포할 키 자격 증명 모음을 *정의하지* 않습니다.

    |필드  |설명  |
    |---------|---------|
    | **서비스 계획**     |   함수 앱에 **Premium** 또는 **소비** 계획을 사용할지 여부를 선택합니다. 자세한 내용은 [Azure Functions 소비 계획 호스팅](/azure/azure-functions/consumption-plan) 및 Azure Functions Premium [계획을](/azure/azure-functions/functions-premium-plan)참조하세요.      |
    | **새 KeyVault를 만들어야 합니다.**     |    **새로** 만들기를 선택하여 앱의 비밀에 대한 새 키 자격 증명 모음을 **만들거나** 기존 키 자격 증명 모음을 사용하기 위해 기존 키 자격 증명 모음을 만듭니다.   |
    | **KeyVault 이름**     | 새 키 자격 증명 모음을 만들도록 선택한 경우에만 표시됩니다. <br><br>앱의 비밀을 저장하는 데 사용할 키 자격 증명 모음의 이름을 입력합니다. 이 이름은 전역적으로 고유해야 합니다.     |
    | **KeyVault 리소스 그룹**     |새 키 자격 증명 모음을 만들도록 선택한 경우에만 표시됩니다. <br><br> 애플리케이션 키에 대한 키 자격 증명 모음을 저장할 리소스 그룹의 이름을 선택합니다.      |
    | **기존 키 자격 증명 모음** | 기존 키 자격 증명 모음을 사용하도록 선택한 경우에만 표시됩니다. 사용하려는 키 자격 증명 모음을 선택합니다. |
    | **KeyVault 비밀 이름**     |   클라이언트 비밀을 저장하는 데 사용되는 비밀의 이름을 입력합니다.      |

    **Honeytoken 구성:** 다음 필드는 Honeytoken에 사용되는 키 및 비밀에 사용되는 설정을 정의합니다. 공격자가 차이를 알 수 없도록 조직의 명명 요구 사항과 혼합되는 명명 규칙을 사용합니다.

    |필드  |설명  |
    |---------|---------|
    |**키 키워드**     |  디코이 허니 토큰 이름과 함께 사용하려는 값의 쉼표로 구분된 목록을 입력합니다.  예: `key,prod,dev`.  값은 영숫자여야 합니다.   |
    |**비밀**     |   디코이 허니 토큰 비밀에 사용할 값의 쉼표로 구분된 목록을 입력합니다.  예: `secret,secretProd,secretDev`. 값은 영숫자여야 합니다.    |
    |**추가 HoneyToken 확률**     |  과 사이에 값을 `0` `1` 입력합니다(예: `0.6` ). 이 값은 Key Vault 두 개 이상의 Honeytoken이 추가될 확률을 정의합니다.       |
    |     |         |

1. **다음: 검토 + 만들기를** 선택하여 솔루션 설치를 완료합니다.

    솔루션이 설치되면 다음 항목이 표시됩니다.

    - **SOCHTManagement** 통합 문서에 대한 링크입니다. 이 절차의 앞부분에 있는 **통합 문서** 탭에서 이 이름을 수정했을 수 있습니다.

    - 사용자 지정 ARM 템플릿의 URL입니다. 이 ARM 템플릿을 사용하여 조직의 KeyVault 소유자에게 **SOCHTManagement** 통합 문서를 배포하는 Azure Security Center 사용자 지정 권장 사항에 연결된 Azure Policy 이니셔티브를 배포할 수 있습니다.

1. **배포 후 단계** 탭에서는 배포 출력에 표시된 정보를 사용하여 조직의 모든 키 자격 증명 모음 소유자에게 Azure Security Center 사용자 지정 권장 사항을 배포할 수 있으며, 키 자격 증명 모음에 honeytoken을 배포하도록 권장합니다.

    설치 출력에 표시된 사용자 지정 [ARM 템플릿 URL을](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2faka.ms%2fsentinelhoneytokenspolicy) 사용하여 연결된 템플릿의 **사용자 지정 배포** 페이지를 엽니다.

    자세한 내용은 [SOCHTManagement 통합 문서 배포를 참조하세요.](#distribute-the-sochtmanagement-workbook)

## <a name="deploy-your-honeytokens"></a>Honeytoken 배포

**Azure Sentinel Deception(Honey Token)** 솔루션을 설치한 후에는 **SOCHTManagement** 통합 문서의 단계를 사용하여 키 자격 증명 모음에 Honeytoken 배포를 시작할 준비가 된 것입니다.

조직의 키 자격 증명 모음 소유자와 **SOCHTManagement** 통합 문서를 공유하여 키 자격 증명 모음에 자체 Honeytoken을 만들 수 있도록 하는 것이 좋습니다. [솔루션을 설치할](#install-the-solution)때 이 통합 문서의 이름을 변경했을 수 있습니다. 공유할 때 읽기 권한만 부여해야 합니다.

키 자격 **증명 모음에 honeytoken을 배포합니다.**

1. Azure Sentinel 내 통합 문서 **> 통합 문서로** 이동하여 **SOCHTManagement** 통합 문서를 엽니다. 솔루션을 배포할 때 이 이름을 수정했을 수 있습니다.

1. **저장된 통합 문서 보기**  >  **트러스트된 로 추가를** 선택합니다. 다음은 그 예입니다.

    :::image type="content" source="media/monitor-key-vault-honeytokens/add-as-trusted.png" alt-text="SOCHTManagement 통합 문서 '신뢰할 수 있는 것으로 추가' 단추의 스크린샷.":::

    Honeytoken 배포를 허용하도록 인프라가 키 자격 증명 모음에 배포됩니다.

1. 통합 문서의 **Key Vault** 탭에서 구독을 확장하여 Honeytoken을 배포할 준비가 된 키 자격 증명 모음과 이미 배포된 honeytoken이 있는 키 자격 증명 모음을 확인합니다.

    **SOC에서 모니터링되는** 항목 열에서 녹색 확인 표시는 키 자격 증명 :::image type="icon" source="media/monitor-key-vault-honeytokens/checkmark.png" border="false"::: 모음에 이미 Honeytoken이 있음을 나타냅니다. 빨간색 X 표시는 키 자격 증명 :::image type="icon" source="media/monitor-key-vault-honeytokens/xmark.png" border="false"::: 모음에 아직 Honeytoken이 없는 것을 나타냅니다. 다음은 그 예입니다.

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-deployed.png" alt-text="배포된 Honeytoken을 보여 있는 SOCHTManagement 통합 문서의 스크린샷.":::


1. 통합 문서 페이지에서 Scroll down **작업 수행** 섹션의 지침과 링크를 사용하여 대규모로 모든 키 자격 증명 모음에 honeytoken을 배포하거나 한 번에 하나씩 수동으로 배포합니다.

    # <a name="deploy-at-scale"></a>[대규모 배포](#tab/deploy-at-scale)

    **대규모로 Honeytoken을 배포하려면:**

    1. 사용자 **사용** 링크를 선택하여 키 자격 증명 모음 액세스 정책을 배포하는 ARM 템플릿을 배포하고 Honeytoken을 만들 수 있는 권한으로 지정된 사용자 ID를 부여합니다.

        메시지가 표시되면 로그인하고 ARM 템플릿 배포에 대한 **Project 세부 정보** 및 인스턴스 세부 **정보** 영역에 대한 값을 입력합니다. 사용자의 Azure Active Directory 홈페이지에서 **테넌트 ID** 및 **사용자 개체 ID를** 찾습니다.

        완료되면 **검토 + 만들기를** 선택하여 ARM 템플릿을 배포합니다. 다음은 그 예입니다.

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-arm-template.png" alt-text="사용자 지정 배포 페이지의 스크린샷":::

        설정의 유효성이 검사되고 유효성 검사가 통과하면 유효성 검사 **통과 확인이** 표시됩니다.

        페이지 아래쪽에서 **만들기를** 선택하여 ARM 템플릿을 배포하고 성공적인 배포 확인 페이지를 확인합니다.

    1. 다시 Azure Sentinel **SOCHTManagement** 통합 문서 >   >  **대규모로 작업 배포에서** **배포** 링크를 선택하여 선택한 구독에서 액세스할 수 있는 모든 키 자격 증명 모음에 honeytoken을 추가합니다.

        완료되면 허니 토큰 배포 결과가 새 탭의 테이블에 표시됩니다.

    1. 사용자 사용 안 함 링크를 선택하여 이전에 만든 액세스 정책을 제거해야 **합니다.** 메시지가 표시되면 다시 로그인하고 사용자 지정 ARM 배포에 대한 값을 입력한 다음 ARM 템플릿을 배포합니다. 이 단계에서는 키 및 비밀을 만드는 사용자 권한을 제거하는 키 자격 증명 모음 액세스 정책을 배포합니다.

    # <a name="deploy-a-single-honeytoken"></a>[단일 Honeytoken 배포](#tab/deploy-a-single-honeytoken)

    **단일 허니 토큰을 수동으로 배포하려면:**

    1. 페이지 맨 위에 있는 테이블에서 Honeytoken을 배포할 키 자격 증명 모음을 선택합니다. 특정 키 자격 증명 **모음에 배포:** 섹션이 페이지 아래쪽에 나타납니다.

    1. Scroll down **Honeytoken 형식** 드롭다운에서 키 또는 비밀을 만들 것인지 선택합니다. 새 **허니 토큰 이름 필드에 Honeytoken에** 대한 의미 있는 이름을 입력합니다. 다음은 그 예입니다.

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-manually.png" alt-text="특정 키 자격 증명 모음 영역에 배포하는 스크린샷.":::

    1. **작업** 테이블에서 **Honeytoken 배포 섹션을** 확장하고 각 작업 이름을 선택하여 필요한 단계를 수행합니다. 메시지가 표시되면 로그인합니다.

        - **키 자격 증명 모음이 감사되고 있는지 확인하려면 클릭을** 선택합니다. Azure Key Vault Key Vault 진단 설정이 Log Analytics에 감사 이벤트를 보내도록 설정되어 있는지 확인합니다.
        - 누락된 경우 키 자격 증명 **모음의 정책에서 사용자 사용을** 선택합니다. Azure Key Vault 사용자가 필요한 위치에 Honeytoken을 배포할 수 있는 액세스 권한이 있는지 확인합니다. **저장** 을 선택하여 변경 내용을 저장합니다.
        - 키 자격 증명 **모음에 허니 토큰을 추가하려면 클릭을** 선택하여 Azure Key Vault 엽니다. 구성된 키 자격 증명 모음에 새 비밀과 같은 새 Honeytoken을 추가합니다.
        - **SOC에서 모니터링을 추가하려면 클릭을** 선택합니다. 성공하면 새 탭에 확인 메시지가 `Honey-token was successfully added to monitored list` 표시됩니다.

        자세한 내용은 [Azure Key Vault 설명서](/azure/key-vault/secrets/about-secrets)를 참조하세요.

    > [!NOTE]
    > 필요한 경우 키 자격 증명 **모음의 정책에서 사용자 다시 사용 안 함** 링크를 선택하여 Honeytoken을 만들기 위해 만든 액세스 정책 권한 부여 권한을 제거해야 합니다.
    >

    # <a name="remove-a-honeytoken"></a>[Honeytoken 제거](#tab/remove-a-honeytoken)

    **특정 Honeytoken을 제거하려면:**

    1. 페이지 맨 위에 있는 테이블에서 Honeytoken을 제거할 키 자격 증명 모음을 선택합니다. 특정 키 자격 증명 **모음에 배포:** 섹션이 페이지 아래쪽에 나타납니다.

    1. **작업** 테이블에서 **Honeytoken 제거 섹션을** 확장하고 각 작업 이름을 선택하여 필요한 단계를 수행합니다. 메시지가 표시되면 로그인합니다.

        - 키 자격 증명 **모음에서 허니 토큰을 삭제하려면 클릭을** 선택하여 honeytoken을 제거할 수 있는 페이지로 Azure Key Vault 엽니다. 
        - 전자 메일 보내기를 선택하여 **SOC 를 업데이트합니다.** SOC에 대한 기본 메일 클라이언트에서 전자 메일이 열리고 선택한 keyvault에 대한 허니 토큰 모니터링을 제거하도록 권장합니다.

    > [!TIP]
    > 삭제한 Honeytoken에 대해 SOC와 명확하게 통신하는 것이 좋습니다.
    >

    ---

데이터가 채워지고 권한이 업데이트될 때 몇 분 정도 기다려야 할 수 있습니다. 페이지를 새로 고쳐 키 볼트 배포의 모든 업데이트를 표시합니다.

## <a name="test-the-solution-functionality"></a>솔루션 기능 테스트

**허니 토큰에 대한 액세스 시도에 대한 경고를 받는지 테스트하려면:**

1. Azure Sentinel **감시 목록** 페이지에서 내 주의 **목록 탭을** 선택한 다음 **HoneyTokens** 감시 목록을 선택합니다.

    **Log Analytics에서 보기를** 선택하여 찾은 현재 Honeytoken 값의 목록을 봅니다. **로그** 페이지에서는 쿼리에 대해 주의 목록의 항목이 자동으로 추출됩니다. 다음은 그 예입니다.

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png" alt-text="Log Analytics의 Honeytokens 감시 목록 값 스크린샷." lightbox="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png":::


    자세한 내용은 [Azure Sentinel 관심 목록 사용](watchlists.md)을 참조하세요.

1. Log Analytics의 목록에서 테스트할 Honeytoken 값을 선택합니다.

    그런 다음, Azure Key Vault 이동하여 공개 키를 다운로드하거나 선택한 honeytoken에 대한 비밀을 확인합니다.

    예를 들어 Honeytoken을 선택한 **다음, 공개 키 다운로드를** 선택합니다. 이 작업은 `KeyGet` Azure Sentinel 경고를 트리거하는 또는 `SecretGet` 로그를 만듭니다.

    자세한 내용은 [Key Vault 설명서](/azure/key-vault/)를 참조하세요.

1. Azure Sentinel 다시 **인시던트** 페이지로 이동합니다. 5분 정도 기다려야 할 수도 있지만 **HoneyTokens: KeyVault HoneyTokens 키 액세스와** 같이 라는 새 인시던트가 표시되어야 합니다.

    수행된 키 작업, Honeytoken 키에 액세스한 사용자 및 손상된 키 자격 증명 모음의 이름과 같은 세부 정보를 보려면 인시던트 를 선택합니다.

    > [!TIP]
    > 허니 토큰 키와 비밀을 통해 액세스하거나 작업을 수행하면 Azure Sentinel 조사할 수 있는 인시던트도 생성됩니다. 실제로 Honeytoken 키와 비밀을 사용할 이유가 없으므로 작업 영역의 유사한 활동은 악의적일 수 있으며 조사해야 합니다.
    >

1. **HoneyTokensIncident** 통합 문서에서 Honeytoken 작업을 봅니다. Azure Sentinel **통합 문서** 페이지에서 **HoneyTokensIncident** 통합 문서를 검색하여 엽니다.

    이 통합 문서에는 모든 Honeytoken 관련 인시던트, 관련 엔터티, 손상된 키 자격 증명 모음, 수행된 키 작업 및 액세스된 Honeytoken이 표시됩니다.

    특정 인시던트 및 작업을 선택하여 모든 관련 활동을 추가로 조사합니다.

## <a name="distribute-the-sochtmanagement-workbook"></a>SOCHTManagement 통합 문서 배포

조직에서 최적의 검색 기능을 보장하기 위해 가능한 한 많은 키 자격 증명 모음에 Honeytoken을 배포하는 것이 좋습니다.

그러나 많은 SOC 팀은 키 자격 증명 모음에 액세스할 수 없습니다. 이러한 차이를 해결하려면 SOC 팀이 자체 Honeytoken을 배포할 수 있도록 테넌트의 모든 키 자격 증명 모음 소유자에게 **SOCHTManagement** 통합 문서를 배포합니다. [솔루션을 설치할](#install-the-solution)때 이 통합 문서의 이름을 수정했을 수 있습니다.

언제든지 통합 문서에 대한 직접 링크를 공유할 수 있습니다. 또는 이 절차에서는 ARM 템플릿을 사용하여 조직의 KeyVault 소유자에게 **SOCHTManagement** 통합 문서를 배포하는 Azure Security Center 사용자 지정 권장 사항에 연결된 Azure Policy 이니셔티브를 배포하는 방법을 설명합니다.

> [!NOTE]
> 통합 문서를 배포할 때마다 읽기 권한만 부여해야 합니다.
>

**Azure Policy 이니셔티브를 통해 SOCHTManagement 통합 문서를 배포하려면**

1. 다음 표에서 **Azure에 배포** 단추를 선택하여 ARM 템플릿을 **배포하려는** 방법에 따라 ARM 템플릿을 사용자 지정 배포 페이지로 엽니다.  GitHUb 링크를 사용하여 ARM 템플릿에 포함된 항목의 세부 정보를 보거나 환경에 맞게 ARM 템플릿을 사용자 지정할 수 있습니다.

    **Azure에 배포** 단추는 [솔루션 설치](#install-the-solution)후 **출력** 탭에 표시된 것과 동일한 URL을 사용합니다.

    | 배포 옵션 | 설명 | Azure에 배포 | GitHub 링크 |
    |-------------------|-------------|-------------|-----------------|
    | 관리 그룹 | 엔터프라이즈 수준의 배포에 권장| [![DTA-Button-MG]][DTA-MG]  |[GitHub의 예제][GitHub-MG] |
    | 구독 | 단일 구독에서의 테스트에 권장 | [![DTA-단추-Sub]][DTA-Sub]  | [GitHub의 예제][GitHub-Sub] |

    메시지가 표시 되 면 로그인 합니다.

1. ARM 템플릿의 **사기 Solution 정책 배포**  >  **기본 사항** 탭에서 관리 그룹 값 및 지역을 선택 합니다. 그런 후에 **다음: 배포 대상 >** 를 선택 하 여 계속 합니다.

1. **배포 대상** 탭에서 관리 그룹을 다시 선택한 다음 **Nex: management Workbook >** 를 선택 합니다.

1. **관리 통합 문서** 탭에서 **sochtmanagement** 통합 문서에 대 한 링크를 붙여 넣습니다.

    Azure 센티널의 **Sochtmanagement** 통합 문서에서 통합 문서 링크를 찾을 수 있으며 솔루션 배포의 **출력** 탭에도 포함 되었습니다.

    예를 들어 통합 문서에서 링크를 **찾으려면 통합 문서**  >  **내 통합 문서**  >  **sochtmanagement** 를 선택한 다음 도구 모음에서 **링크 복사** 를 선택 합니다.

1. 통합 문서 링크를 입력 한 후 **다음: 검토 + >만들기** 를 선택 하 여 계속 합니다. 유효성 검사에 통과 한 확인 메시지가 있을 때까지 기다린 후 **만들기** 를 선택 합니다.

1. 배포가 완료 되 면 배포에 새 **HoneyTokens** 이니셔티브와 **keyvault HoneyTokens** 및 **KVReviewTag** 라는 두 개의 새 정책이 포함 되어 있음을 알 수 있습니다. 다음은 그 예입니다.

    :::image type="content" source="media/monitor-key-vault-honeytokens/policy-deployment.png" alt-text="성공적으로 배포 된 ARM 템플릿 정책의 스크린샷" lightbox="media/monitor-key-vault-honeytokens/policy-deployment.png":::

1. Azure **policy** 에서 필요한 범위를 사용 하 여 새 **KVReviewTag** 정책을 할당 합니다. 이 할당은 선택한 범위의 모든 키 자격 증명 모음에 **Kvreview** 태그와 **ReviewNeeded** 값을 추가 합니다.

    1. Azure Policy의 왼쪽에서 **제작** 에서 **정의** 를 선택 합니다. **KVReviewTag** policy 행을 찾아 오른쪽에 있는 옵션 메뉴를 선택 합니다.

    1. **활동 로그에 Log Analytics 작업 영역에 대 한 진단 설정 배포** 페이지에서 사용자 환경에 대 한 진단 설정을 배포 하는 데 필요한 값을 입력 합니다.

        **업데이트 관리** 탭에서 **수정 작업 만들기** 옵션을 선택 하 여 태그를 기존 키 자격 증명 모음에 적용 해야 합니다.

    자세한 내용은 [Azure 정책 설명서](/azure/governance/policy/assign-policy-portal)를 참조하세요.

1. Azure **Security Center** 에서 선택한 범위의 모든 주요 자격 증명 모음에 감사 권장 구성을 추가 합니다.

    1. **규정 준수 > 준수 정책 관리** 를 선택 하 고 범위를 선택 합니다.

    1. 선택한 범위에 대 한 세부 정보 페이지에서 아래로 스크롤하여 사용자 **지정 initatives** 섹션에서 **사용자 지정 이니셔티브 추가** 를 선택 합니다.

    1. **HoneyTokens** 이니셔티브 행에서 **추가** 를 선택 합니다.

**Sochtmanagement** 통합 문서에 대 한 링크가 있는 감사 권장 사항이 선택한 범위의 모든 key vault에 추가 됩니다. [솔루션을 설치할 때](#install-the-solution)이 통합 문서의 이름을 수정 했을 수 있습니다.

자세한 내용은 [Azure Security Center 설명서](/azure/security-center/security-center-recommendations)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel 솔루션 정보](sentinel-solutions.md)
- [Azure Sentinel 솔루션 검색 및 배포](sentinel-solutions-deploy.md)
- [Azure Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)
- [처음부터 위협 탐지](detect-threats-built-in.md)
- [일반적으로 사용되는 Azure Sentinel 통합 문서](top-workbooks.md)


<!-- The following section is used to store references to external images and links to reduce maintenance overhead and enable tooltips -->

[//]: # (*******************************)
[//]: # (아래의 외부 이미지 참조)
[//]: # (*******************************)

[DTA-MG 단추-]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "관리 그룹 범위에 ASC 정책을 배포 합니다."
[DTA-단추-]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "구독 범위에 ASC 정책을 배포 합니다."

[//]: # (**************************)
[//]: # (아래의 외부 링크 레이블)
[//]: # (**************************)

[GitHub-MG]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicy.json
[GitHub-Sub]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicySub.json

[DTA-MG]: https://portal.azure.com/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicy.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicyUI.json
[DTA-Sub]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicySub.json
