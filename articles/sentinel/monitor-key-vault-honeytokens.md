---
title: Azure honeytokens를 사용 하 여 Azure Key Vault 배포 및 모니터링 | Microsoft Docs
description: 허니 토큰는 키와 비밀을 Azure Key Vault 하 고 Azure 센티널로 모니터링 합니다.
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
ms.openlocfilehash: 160abcf739cbece61d34cd3db4118fd9f2f0ed9f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483117"
---
# <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-azure-sentinel-public-preview"></a>Azure 센티널을 사용 하 여 Azure Key Vault honeytokens 배포 및 모니터링 (공개 미리 보기)

> [!IMPORTANT]
> Azure 센티널 사기 (허니 Tokens) 솔루션은 현재 미리 보기로 제공 됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

이 문서에서는 **Azure 센티널 사기 (허니 token) 솔루션** 을 사용 하 여 *honeytokens* 라고 하는 위장 [Azure Key Vault](/azure/key-vault/) 키와 비밀을 기존 작업에 공장 화 하는 방법을 설명 합니다.

솔루션에서 제공 하는 [분석 규칙](detect-threats-built-in.md), [watchlists](watchlists.md)및 [통합 문서](monitor-your-data.md) 를 사용 하 여 배포 된 honeytokens에 대 한 액세스를 모니터링 합니다.

시스템에서 honeytokens를 사용 하는 경우 검색 원칙은 동일 하 게 유지 됩니다. 허니 토큰에 액세스할 수 있는 정당한 이유가 없기 때문에 모든 활동은 환경에 익숙하지 않은 사용자의 존재를 표시 하 고 잠재적으로 공격자가 될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

**Azure 센티널 사기 (허니 Tokens)** 솔루션 사용을 시작 하려면 다음이 있는지 확인 합니다.

- **필수 역할**: **Azure 센티널 사기 (허니 Tokens)** 솔루션을 설치 하려면 테 넌 트 관리자 여야 합니다. 솔루션이 설치 된 후에는 고유한 honeytokens를 배포할 수 있도록 통합 문서를 key vault 소유자와 공유할 수 있습니다.

- **필수 데이터 커넥터**: 작업 영역에 [Azure Key Vault](data-connectors-reference.md#azure-key-vault) 및 [Azure 활동](data-connectors-reference.md#azure-activity) 데이터 커넥터를 배포 하 고 연결 했는지 확인 합니다.

    데이터 라우팅이 성공 했 고 **Keyvault** 및 **Azureactivity** 데이터가 Azure 센티널로 이동 하는지 확인 합니다. 자세한 내용은 다음을 참조하세요.

    - [Azure Sentinel을 Azure, Windows, Microsoft 및 Amazon 서비스에 연결](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)
    - [Azure 센티널 데이터 커넥터 찾기](data-connectors-reference.md)

## <a name="install-the-solution"></a>솔루션 설치

[다른 솔루션과](monitor-key-vault-honeytokens.md)마찬가지로 **Azure 센티널 사기 (허니 Tokens)** 솔루션을 설치 합니다. **Azure 센티널 사기** 솔루션 페이지에서 **시작** 을 선택 하 여 시작 합니다.

:::image type="content" source="media/monitor-key-vault-honeytokens/honeytoken-create-solution.png" alt-text="솔루션 만들기 페이지의 스크린샷":::

**사기 솔루션을 설치 하려면**:

다음 단계는 **Azure 센티널 사기 (허니 Tokens)** 솔루션에 필요한 특정 작업을 설명 합니다.

1. **기본 사항** 탭에서 Azure 센티널 작업 영역이 있는 동일한 리소스 그룹을 선택 합니다.

1. **필수 구성 요소** 탭의 **함수 앱 이름** 필드에 키 자격 증명 모음에 honeytokens를 만들 Azure 함수 앱에 대 한 의미 있는 이름을 입력 합니다.

    함수 앱 이름은 고유 해야 하며, 길이가 2-22 자에서 자 사이 여야 합니다.

    사용자가 정의한 이름으로 명령이 표시 됩니다. 예를 들어:

    :::image type="content" source="media/monitor-key-vault-honeytokens/prerequisites.png" alt-text="업데이트 된 말아 명령이 표시 되는 필수 구성 요소 탭의 스크린샷":::

1. **클라우드 셸을 열려면 여기를 클릭** 하세요 .를 선택 하 여 Cloud Shell 탭을 엽니다. 메시지가 표시 되 면 로그인 한 다음 표시 되는 명령을 실행 합니다. 

    실행 하는 스크립트는 honeytokens를 배포 하는 Azure AD (AAD) 함수 앱을 만듭니다.    예를 들어:

    ```bash
    Requesting a Cloud Shell.Succeeded
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    maria@Azure:~$curl -sL https://aka.ms/sentinelhoneytokensappcreate | bash -s HoneyTokenFunctionApp
    ```

     스크립트 출력은 AAD 앱 ID 및 암호를 포함 합니다. 예를 들어:

    ```bash
    WARNING: The output includes credentials that you must protect. Be sure that you do not include these credentials in your code or check the credentials into your source control. For more information, see https://aka.ms/azadsp-cli
    function app name: HoneyTokenFunctionApp
    AAD App Id: k4js48k3-97gg-3958=sl8d=48620nne59k4
    AAD App secret: v9kUtSoy3u~K8DKa8DlILsCM_K-s9FR3Kj
    maria@Azure:~$
    ```

1. Azure 센티널로 돌아가서 **필수 조건** 탭의 맨 아래에 AAD 앱 ID 및 암호를 관련 필드에 입력 합니다. 예를 들어:

    :::image type="content" source="media/monitor-key-vault-honeytokens/client-app-secret-values.png" alt-text="함수 앱의 클라이언트 앱 및 추가 된 비밀 값의 스크린샷":::

1. 4 단계에서 **함수 앱 설정에서 계속 하려면 여기를 클릭** 하세요 .를 선택 합니다. Azure AD 응용 프로그램 설정에 새 브라우저 탭이 열립니다.

    메시지가 표시 되 면 로그인 한 다음 **에 대 한 `<your directory name>` 관리자 동의 부여** 를 선택 하 여 계속 합니다. 예를 들어:

    :::image type="content" source="media/monitor-key-vault-honeytokens/grant-admin-access.png" alt-text="디렉터리에 대 한 관리자 동의 허용 단추 스크린샷":::

    자세한 내용은 [앱 등록에서 관리자 동의 부여](/azure/active-directory/manage-apps/grant-admin-consent)를 참조 하세요.

1. 다시 Azure 센티널로 돌아가서 **통합 문서**, **분석**, **Watchlists** 및 플레이 **북** 탭에서 생성 될 보안 콘텐츠를 확인 하 고 필요에 따라 이름을 수정 합니다.

    > [!NOTE]
    > 이 문서의 다른 지침은 **HoneyTokensIncidents** 및 **Sochtmanagement** 통합 문서를 참조 합니다. 이러한 통합 문서의 이름을 변경 하는 경우 고유한 참조에 대 한 새 통합 문서 이름을 확인 하 고 기본 이름 대신 필요에 따라 사용 해야 합니다.
    >

1. **Azure Functions** 탭에서 다음 값을 정의 합니다.

    **키 자격 증명 모음 구성**: 다음 필드는 AAD 앱의 암호를 저장할 주요 자격 증명 모음에 대 한 값을 정의 합니다. 이러한 필드는 honeytokens를 배포할 주요 자격 증명 모음을 정의 *하지* 않습니다.

    |필드  |Description  |
    |---------|---------|
    | **서비스 계획**     |   함수 앱에 대 한 **Premium** 또는 **소비** 계획을 사용할지 여부를 선택 합니다. 자세한 내용은 [Azure Functions 소비 계획 호스팅](/azure/azure-functions/consumption-plan) 및 [Azure Functions Premium 계획](/azure/azure-functions/functions-premium-plan)을 참조 하세요.      |
    | **새 KeyVault를 만들어야 합니다.**     |    **새로** 만들기를 선택 하 여 앱의 비밀에 대 한 새 키 자격 증명 모음을 만들거나 기존 키 자격 증명 모음을 사용 하도록 **기존** 키 자격 증명 모음을 만듭니다.   |
    | **KeyVault 이름**     | 새 key vault를 만들도록 선택한 경우에만 표시 됩니다. <br><br>앱 암호를 저장 하는 데 사용 하려는 key vault의 이름을 입력 합니다. 이 이름은 전역적으로 고유해야 합니다.     |
    | **KeyVault 리소스 그룹**     |새 key vault를 만들도록 선택한 경우에만 표시 됩니다. <br><br> 응용 프로그램 키에 대 한 키 자격 증명 모음을 저장 하려는 리소스 그룹의 이름을 선택 합니다.      |
    | **기존 키 자격 증명 모음** | 기존 key vault를 사용 하도록 선택한 경우에만 표시 됩니다. 사용할 키 자격 증명 모음을 선택 합니다. |
    | **KeyVault 비밀 이름**     |   클라이언트 암호를 저장 하는 데 사용 되는 암호의 이름을 입력 합니다.      |

    **허니 토큰 구성**: 다음 필드는 honeytokens에서 사용 되는 키와 비밀에 사용 되는 설정을 정의 합니다. 공격자가 차이점을 알릴 수 없도록 조직의 명명 요구 사항에 따라 혼합 되는 명명 규칙을 사용 합니다.

    |필드  |Description  |
    |---------|---------|
    |**Keys 키워드**     |  위장 허니 토큰 이름에 사용할 쉼표로 구분 된 값 목록을 입력 합니다.  예들 들어 `key,prod,dev`입니다.  값은 영숫자만 가능 해야 합니다.   |
    |**비밀**     |   위장 허니 토큰 비밀에 사용할 쉼표로 구분 된 값 목록을 입력 합니다.  예들 들어 `secret,secretProd,secretDev`입니다. 값은 영숫자만 가능 해야 합니다.    |
    |**추가 허니 토큰 확률**     |  과 같이와 사이의 값을 입력 `0` `1` `0.6` 합니다. 이 값은 둘 이상의 허니 토큰가 Key Vault에 추가 되는 확률을 정의 합니다.       |
    |     |         |

1. **다음: 검토 + 만들기** 를 선택 하 여 솔루션 설치를 완료 합니다.

    솔루션이 설치 된 후에는 다음 항목이 표시 됩니다.

    - **Sochtmanagement** 통합 문서에 대 한 링크입니다. 이 절차의 앞부분에 나오는 **통합 문서** 탭에서이 이름을 수정 했을 수 있습니다.

    - 사용자 지정 ARM 템플릿에 대 한 URL입니다. 이 ARM 템플릿을 사용 하 여 Azure Security Center 사용자 지정 권장 사항에 연결 된 Azure Policy 이니셔티브를 배포할 수 있습니다 .이 권장 사항은 **Sochtmanagement** 통합 문서를 조직의 keyvault 소유자에 게 배포 합니다.

1. **배포 후 단계** 탭에는 배포 출력에 표시 된 정보를 사용 하 여 조직의 모든 주요 자격 증명 모음 소유자에 게 Azure Security Center 사용자 지정 권장 사항을 배포할 수 있습니다 .이는 키 자격 증명 모음에 honeytokens를 배포 하는 것을 권장 합니다.

    설치 출력에 표시 된 사용자 지정 [ARM 템플릿 URL](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2faka.ms%2fsentinelhoneytokenspolicy) 을 사용 하 여 연결 된 템플릿의 **사용자 지정 배포** 페이지를 엽니다.

    자세한 내용은 [SOCHTManagement 통합 문서 배포](#distribute-the-sochtmanagement-workbook)를 참조 하세요.

## <a name="deploy-your-honeytokens"></a>Honeytokens 배포

**Azure 센티널 사기 (허니 token)** 솔루션을 설치한 후에는 **Sochtmanagement** 통합 문서의 단계를 사용 하 여 키 자격 증명 모음에 honeytokens 배포를 시작할 준비가 되었습니다.

주요 자격 증명 모음에 고유한 honeytokens를 만들 수 있도록 조직에서 주요 자격 증명 모음 소유자와 **Sochtmanagement** 통합 문서를 공유 하는 것이 좋습니다. [솔루션을 설치할](#install-the-solution)때이 통합 문서의 이름을 변경 했을 수 있습니다. 공유 하는 경우 읽기 권한만 부여 해야 합니다.

**키 자격 증명 모음에 honeytokens를 배포 합니다**.

1. Azure 센티널에서 **내 통합 문서 > 통합** 문서로 이동 하 고 **Sochtmanagement** 통합 문서를 엽니다. 솔루션을 배포할 때이 이름을 수정 했을 수 있습니다.

1. **저장 된 통합 문서 보기**  >  **신뢰할 수 있는 이름 추가를** 선택 합니다. 예를 들어:

    :::image type="content" source="media/monitor-key-vault-honeytokens/add-as-trusted.png" alt-text="SOCHTManagement 통합 문서 ' 신뢰할 수 있는 것으로 추가 ' 단추의 스크린샷":::

    인프라는 허니 토큰 배포를 허용 하기 위해 키 자격 증명 모음에 배포 됩니다.

1. 통합 문서의 **Key Vault** 탭에서 구독을 확장 하 여 honeytokens 및 honeytokens가 이미 배포 된 key vault를 배포 하기 위한 주요 자격 증명 모음을 볼 수 있습니다.

    SOC 열에서 **모니터링** 하는 경우 녹색 확인 표시는 :::image type="icon" source="media/monitor-key-vault-honeytokens/checkmark.png" border="false"::: 키 자격 증명 모음에 이미 honeytokens가 있음을 나타냅니다. 빨간색 x 표시는 :::image type="icon" source="media/monitor-key-vault-honeytokens/xmark.png" border="false"::: 키 자격 증명 모음에 아직 honeytokens가 없음을 나타냅니다. 예를 들어:

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-deployed.png" alt-text="배포 된 honeytokens를 보여 주는 SOCHTManagement 통합 문서의 스크린샷.":::


1. 통합 문서 페이지에서 아래로 스크롤하고 **작업 수행** 섹션의 지침과 링크를 사용 하 여 honeytokens를 모든 key vault에 배포 하거나 한 번에 하나씩 수동으로 배포 합니다.

    # <a name="deploy-at-scale"></a>[대규모 배포](#tab/deploy-at-scale)

    **Honeytokens을 대규모로 배포 하려면**:

    1. **사용자 사용** 링크를 선택 하 여 키 자격 증명 모음 액세스 정책을 배포 하는 ARM 템플릿을 배포 하 고 honeytokens를 만들 수 있는 권한으로 지정 된 사용자 ID를 부여 합니다.

        메시지가 표시 되 면 로그인 하 고 ARM 템플릿 배포에 대 한 **Project 세부 정보** 및 **인스턴스 세부 정보** 영역에 대 한 값을 입력 합니다. 사용자의 Azure Active Directory 홈 페이지에서 **테 넌 트 id** 및 **사용자 개체 id** 를 찾습니다.

        완료 되 면 **검토 + 만들기** 를 선택 하 여 ARM 템플릿을 배포 합니다. 예를 들어:

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-arm-template.png" alt-text="사용자 지정 배포 페이지의 스크린샷":::

        설정의 유효성을 검사 하 고 유효성 검사를 통과 하면 확인이 성공 합니다. **유효성 검사 통과**

        페이지 맨 아래에서 **만들기** 를 선택 하 여 ARM 템플릿을 배포 하 고 성공적인 배포 확인 페이지를 봅니다.

    1. Azure 센티널로 돌아가서 **sochtmanagement** 통합 문서에서 scale에서 **작업** 배포를 > 하 고  >   , 선택한 구독에서 액세스 권한이 있는 모든 key vault에 honeytokens를 추가 하려면 **클릭** 합니다. 링크를 선택 합니다.

        완료 되 면 허니 토큰 배포 결과가 새 탭의 테이블에 표시 됩니다.

    1. 사용자가 이전에 만든 액세스 정책을 제거 하려면 **사용자 사용 안 함** 링크를 선택 해야 합니다. 메시지가 표시 되 면 다시 로그인 하 고 사용자 지정 ARM 배포에 대 한 값을 입력 한 다음 ARM 템플릿을 배포 합니다. 이 단계에서는 키 및 암호를 만들 수 있는 사용자 권한을 제거 하는 키 자격 증명 모음 액세스 정책을 배포 합니다.

    # <a name="deploy-a-single-honeytoken"></a>[단일 허니 토큰 배포](#tab/deploy-a-single-honeytoken)

    **단일 허니 토큰을 수동으로 배포 하려면**:

    1. 페이지 맨 위에 있는 표에서 허니 토큰를 배포 하려는 key vault를 선택 합니다. **특정 키 자격 증명 모음에 배포:** 섹션은 페이지의 맨 아래에 나타납니다.

    1. 아래로 스크롤하고 **허니 토큰 유형** 드롭다운에서 키 또는 비밀을 만들지 여부를 선택 합니다. **새 허니 토큰 이름** 필드에 허니 토큰에 대 한 의미 있는 이름을 입력 합니다. 예를 들어:

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-manually.png" alt-text="특정 키 자격 증명 모음에 대 한 배포의 스크린샷":::

    1. **작업** 테이블에서 **허니 토큰 배포** 섹션을 확장 하 고 각 작업 이름을 선택 하 여 필요한 단계를 수행 합니다. 메시지가 표시 되 면 로그인 합니다.

        - **키 자격 증명 모음을 감사 하려면 클릭** 하십시오 .를 선택 합니다. Azure Key Vault에서 키 자격 증명 모음 진단 설정이 Log Analytics에 감사 이벤트를 보내도록 설정 되어 있는지 확인 합니다.
        - **키 자격 증명 모음 정책에 사용자가 없는 경우 사용** 을 선택 합니다. Azure Key Vault에서 사용자에 게 필요한 위치에 honeytokens를 배포할 수 있는 권한이 있는지 확인 합니다. **저장** 을 선택하여 변경 내용을 저장합니다.
        - **허니 토큰을 키 자격 증명 모음에 추가 하려면 클릭** 하십시오 .를 선택 하 여 Azure Key Vault을 엽니다. 새 비밀 같은 새 허니 토큰를 구성 된 key vault에 추가 합니다.
        - **SOC에서 모니터링을 추가 하려면 클릭** 합니다 .를 선택 합니다. 성공 하면 새 탭에 확인 메시지가 표시 `Honey-token was successfully added to monitored list` 됩니다.

        자세한 내용은 [Azure Key Vault 설명서](/azure/key-vault/secrets/about-secrets)를 참조하세요.

    > [!NOTE]
    > **필요한 경우 키 자격 증명 모음 정책에서 사용자를 사용 하지 않도록 설정** 링크를 선택 하 여 honeytokens를 만들 수 있는 액세스 권한 부여 권한을 제거 합니다.
    >

    # <a name="remove-a-honeytoken"></a>[허니 토큰 제거](#tab/remove-a-honeytoken)

    **특정 허니 토큰을 제거 하려면**:

    1. 페이지 맨 위에 있는 표에서 허니 토큰을 제거 하려는 키 자격 증명 모음을 선택 합니다. **특정 키 자격 증명 모음에 배포:** 섹션은 페이지의 맨 아래에 나타납니다.

    1. **작업** 테이블에서 **허니 토큰 제거** 섹션을 확장 하 고 각 작업 이름을 선택 하 여 필요한 단계를 수행 합니다. 메시지가 표시 되 면 로그인 합니다.

        - 허니 토큰를 제거할 수 있는 페이지에 Azure Key Vault를 열려면 **키 자격 증명 모음에서 허니 토큰을 삭제 하려면 클릭** 합니다 .를 선택 합니다. 
        - **SOC를 업데이트 하려면 전자 메일 보내기를** 선택 합니다. 기본 전자 메일 클라이언트에서 SOC로 메일을 열고 선택한 keyvault에 대 한 허니 토큰 모니터링을 제거 하는 것을 권장 합니다.

    > [!TIP]
    > 사용자가 삭제 하는 honeytokens에 대해 SOC와 명확 하 게 통신 하는 것이 좋습니다.
    >

    ---

데이터가 채워지고 사용 권한이 업데이트 되 면 몇 분 정도 기다려야 할 수 있습니다. Keyvault 배포에 업데이트를 표시 하려면 페이지를 새로 고칩니다.

## <a name="test-the-solution-functionality"></a>솔루션 기능 테스트

**허니 토큰에 대 한 액세스 시도에 대 한 경고를 받도록 테스트 하려면**:

1. Azure 센티널 **Watchlists** 페이지에서 **내 Watchlists** 탭을 선택 하 고 **HoneyTokens** 관심 목록를 선택 합니다.

    **Log Analytics에서 보기** 를 선택 하 여 찾은 현재 허니 토큰 값의 목록을 확인 합니다. **로그** 페이지에서 관심 목록의 항목이 쿼리에 대해 자동으로 추출 됩니다. 예를 들어:

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png" alt-text="Log Analytics honeytokens 관심 목록 값의 스크린샷" lightbox="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png":::


    자세한 내용은 [Azure Sentinel 관심 목록 사용](watchlists.md)을 참조하세요.

1. Log Analytics 목록에서 테스트할 허니 토큰 값을 선택 합니다.

    그런 다음 Azure Key Vault로 이동 하 여 공개 키를 다운로드 하거나 선택한 허니 토큰에 대 한 암호를 확인 합니다.

    예를 들어 허니 토큰을 선택 하 고 **공개 키 다운로드** 를 선택 합니다. 이 작업을 수행 `KeyGet` 하면 `SecretGet` Azure 센티널에서 경고를 트리거하는 또는 로그가 생성 됩니다.

    자세한 내용은 [Key Vault 설명서](/azure/key-vault/)를 참조하세요.

1. Azure 센티널로 돌아가서 **인시던트** 페이지로 이동 합니다. 5 분 정도 기다려야 할 수 있지만, 예를 들어 **HoneyTokens: KeyVault HoneyTokens key에 액세스** 한 새 인시던트가 표시 되어야 합니다.

    수행 된 키 작업, 허니 토큰 키에 액세스 한 사용자 및 손상 된 key vault의 이름과 같은 세부 정보를 보려면 인시던트를 선택 합니다.

    > [!TIP]
    > 허니 토큰 키와 암호를 사용 하는 모든 액세스 또는 작업은 Azure 센티널에서 조사할 수 있는 인시던트를 생성 합니다. 실제로 허니 토큰 키와 비밀을 사용 하는 이유는 없으므로 작업 영역에서 유사한 활동은 악성이 될 수 있으므로 조사 해야 합니다.
    >

1. **HoneyTokensIncident** 통합 문서에서 허니 토큰 활동을 봅니다. Azure 센티널 **통합 문서** 페이지에서 **HoneyTokensIncident** 통합 문서를 검색 하 여 엽니다.

    이 통합 문서에는 모든 허니 토큰 관련 인시던트, 관련 엔터티, 손상 된 키 자격 증명 모음, 수행 되는 키 작업 및 액세스 한 honeytokens이 표시 됩니다.

    관련 된 모든 작업을 자세히 조사 하려면 특정 인시던트 및 작업을 선택 합니다.

## <a name="distribute-the-sochtmanagement-workbook"></a>SOCHTManagement 통합 문서 배포

조직에서 최적의 검색 기능을 보장 하기 위해 최대한 많은 키 자격 증명 모음에 honeytokens를 배포 하는 것이 좋습니다.

그러나 많은 SOC 팀은 주요 자격 증명 모음에 액세스할 수 없습니다. 이러한 격차를 해결 하려면 SOC 팀에서 자신의 honeytokens를 배포할 수 있도록 **Sochtmanagement** 통합 문서를 테 넌 트의 모든 주요 자격 증명 모음 소유자에 게 배포 합니다. [솔루션을 설치할](#install-the-solution)때이 통합 문서의 이름을 수정 했을 수 있습니다.

항상 통합 문서에 대 한 직접 링크를 공유할 수 있습니다. 또는이 절차에서는 ARM 템플릿을 사용 하 여 사용자 지정 권장 사항 Azure Security Center에 연결 된 Azure Policy 이니셔티브를 배포 하는 방법에 대해 설명 합니다 .이 권장 사항은 **Sochtmanagement** 통합 문서를 조직의 keyvault 소유자에 게 배포 합니다.

> [!NOTE]
> 통합 문서를 배포할 때마다 읽기 권한만 부여 해야 합니다.
>

**Azure Policy 이니셔티브를 통해 SOCHTManagement 통합 문서를 배포 하려면**

1. 다음 표에서 ARM 템플릿을 배포 하려는 방법에 따라 **Azure에 배포** 단추를 선택 하 여 Arm 템플릿을 **사용자 지정 배포** 페이지로 엽니다.  GitHUb 링크를 사용 하 여 ARM 템플릿에 포함 된 항목의 세부 정보를 보거나 사용자 환경에 대 한 ARM 템플릿을 사용자 지정 합니다.

    **Azure에 배포** 단추는 [솔루션 설치](#install-the-solution)후 **출력** 탭에 표시 되는 것과 동일한 url을 사용 합니다.

    | 배포 옵션 | 설명 | Azure에 배포 | GitHub 링크 |
    |-------------------|-------------|-------------|-----------------|
    | 관리 그룹 | 엔터프라이즈 수준 배포에 권장| [![DTA 단추-MG]][DTA-MG]  |[GitHub 예제][GitHub-MG] |
    | 구독 | 단일 구독에서 테스트하는 데 권장 | [![DTA-Button-Sub]][DTA-Sub]  | [GitHub 예제][GitHub-Sub] |

    메시지가 표시되면 로그인합니다.

1. ARM 템플릿의 **Deception Solution Policy Deployment**  >  **Basics** 탭에서 관리 그룹 값 및 지역을 선택합니다. 그런 다음, **다음: 배포 대상 >** 선택하여 계속합니다.

1. 배포 **대상** 탭에서 관리 그룹을 다시 선택한 다음 **Nex: 관리 통합 문서 >** 를 선택합니다.

1. 관리 **통합 문서** 탭에서 **SOCHTManagement** 통합 문서에 대한 링크를 붙여넣습니다.

    Azure Sentinel **SOCHTManagement** 통합 문서에서 통합 문서 링크를 찾을 수 있으며 솔루션 배포의 **출력** 탭에도 포함되었습니다.

    예를 들어 통합 문서에서 링크를 찾으려면 통합 문서 내 **통합**  >  **문서**  >  **SOCHTManagement** 를 선택한 다음, 도구 모음에서 **링크 복사를** 선택합니다.

1. 통합 문서 링크를 입력한 후 **다음: 검토 + >만들기를** 선택하여 계속합니다. 유효성 검사가 통과되었다는 확인 메시지가 표시될 때까지 기다린 다음, **만들기를** 선택합니다.

1. 배포가 완료되면 배포에 새 **HoneyTokens** 이니셔티브와 **KeyVault HoneyTokens** 및 **KVReviewTag라는** 두 개의 새 정책이 포함됩니다. 예를 들어:

    :::image type="content" source="media/monitor-key-vault-honeytokens/policy-deployment.png" alt-text="성공적으로 배포된 ARM 템플릿 정책의 스크린샷." lightbox="media/monitor-key-vault-honeytokens/policy-deployment.png":::

1. Azure **Policy에서** 필요한 범위로 새 **KVReviewTag** 정책을 할당합니다. 이 할당은 **KVReview** 태그 및 **ReviewNeeded** 값을 선택한 범위의 모든 키 자격 증명 모음에 추가합니다.

    1. Azure Policy 왼쪽의 **제작에서** **정의를** 선택합니다. **KVReviewTag** 정책 행을 찾고 오른쪽의 옵션 메뉴를 선택합니다.

    1. 활동 **로그에 대한 진단 설정 Log Analytics 작업 영역에 배포** 페이지에서 환경에 대한 진단 설정을 배포하는 데 필요한 값을 입력합니다.

        **수정** 탭에서 **수정 작업 만들기** 옵션을 선택하여 기존 키 자격 증명 모음에 태그를 적용해야 합니다.

    자세한 내용은 [Azure 정책 설명서](/azure/governance/policy/assign-policy-portal)를 참조하세요.

1. Azure **Security Center** 선택한 범위의 모든 키 자격 증명 모음에 감사 권장 사항을 추가합니다.

    1. **규정 준수 > 준수 정책 관리를** 선택한 다음, 범위를 선택합니다.

    1. 선택한 범위에 대한 세부 정보 페이지에서 아래로 스크롤하고 **사용자 지정 initatives** 섹션에서 **사용자 지정 이니셔티브 추가를** 선택합니다.

    1. **HoneyTokens** 이니셔티브 행에서 **추가를** 선택합니다.

**SOCHTManagement** 통합 문서에 대한 링크가 있는 감사 권장 사항이 선택한 범위의 모든 키 자격 증명 모음에 추가됩니다. [솔루션을 설치할 때](#install-the-solution)이 통합 문서의 이름을 수정했을 수 있습니다.

자세한 내용은 Azure Security Center [설명서를 참조하세요.](/azure/security-center/security-center-recommendations)


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel 솔루션 정보](sentinel-solutions.md)
- [Azure Sentinel 솔루션 검색 및 배포](monitor-key-vault-honeytokens.md)
- [Azure Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)
- [처음부터 위협 탐지](detect-threats-built-in.md)
- [일반적으로 사용되는 Azure Sentinel 통합 문서](top-workbooks.md)


<!-- The following section is used to store references to external images and links to reduce maintenance overhead and enable tooltips -->

[//]: # (*******************************)
[//]: # (아래의 외부 이미지 참조)
[//]: # (*******************************)

[DTA-Button-MG]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "관리 그룹 범위에 ASC 방지를 배포합니다."
[DTA-Button-Sub]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "구독 범위에 ASC 방지를 배포합니다."

[//]: # (**************************)
[//]: # (아래의 외부 링크 레이블)
[//]: # (**************************)

[GitHub-MG]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicy.json
[GitHub-Sub]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicySub.json

[DTA-MG]: https://portal.azure.com/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicy.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicyUI.json
[DTA-Sub]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicySub.json
