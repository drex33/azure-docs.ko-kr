---
title: Azure Data Box 주문 자습서 | Microsoft 설명서
description: 이 자습서에서는 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 솔루션인 Azure Data Box와 Azure Data Box를 정렬하는 방법에 대해 알아봅니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/22/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dc06c5646f85fda4b4100805be4a9566d0917e2a
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652995"
---
# <a name="tutorial-order-azure-data-box"></a>자습서: Azure Data Box 주문

Azure Data Box는 빠르고 쉽게 신뢰할 수 있는 방식으로 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 솔루션입니다. 데이터를 Microsoft에서 제공하는 80TB(사용 가능한 용량) 스토리지 디바이스에 전송한 다음, 디바이스를 다시 배송합니다. 그런 다음, 이 데이터는 Azure에 업로드됩니다.

이 자습서에서는 Azure Data Box를 주문하는 방법에 대해 설명합니다. 이 자습서에서는 다음에 대해 알아봅니다.  

> [!div class="checklist"]
>
> * Data Box를 배포하기 위한 필수 구성 요소
> * Data Box 주문
> * 주문 추적
> * 주문 취소

> [!NOTE]
> Data Box 주문 및 배송에 대한 질문과 대답은 [Data Box FAQ](data-box-faq.yml)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

# <a name="portal"></a>[포털](#tab/portal)

디바이스를 배포하기 전에 Data Box 서비스 및 디바이스에 대해 다음 필수 구성 요소를 완료합니다.

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

Azure에 로그인하고 다음 두 방법 중 하나로 Azure CLI 명령을 실행할 수 있습니다.

* CLI를 설치하고 로컬로 CLI를 실행합니다.
* Azure Portal 내에서 Azure Cloud Shell을 사용하여 CLI 명령을 실행합니다.

이 자습서에서는 Windows PowerShell을 통해 Azure CLI를 사용하지만, 어떤 옵션을 선택해도 됩니다.

### <a name="for-azure-cli"></a>Azure CLI의 경우

시작하기 전에 다음 사항을 확인합니다.

#### <a name="install-the-cli-locally"></a>로컬에서 CLI 설치

* [Azure CLI](/cli/azure/install-azure-cli) 버전 2.0.67 이상을 설치합니다. 또는 [MSI를 사용하여 설치](https://aka.ms/installazurecliwindows)해도 됩니다.

**Azure에 로그인**

Windows PowerShell 명령 창을 열고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure에 로그인합니다.

```azurecli
PS C:\Windows> az login
```

다음은 성공적인 로그인의 출력입니다.

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Azure Data Box CLI 확장 설치**

Azure Data Box CLI 명령을 사용하려면 먼저 확장을 설치해야 합니다. Azure CLI 확장은 아직 핵심 CLI의 일부로 제공되지 않는 실험적 명령과 시험판 명령에 대한 액세스를 제공합니다. 확장에 대한 자세한 내용은 [Azure CLI에서 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조하세요.

Azure Data Box 확장을 설치하려면 `az extension add --name databox` 명령을 실행합니다.

```azurecli

    PS C:\Windows> az extension add --name databox
```

확장이 성공적으로 설치되면 다음 출력이 표시됩니다.

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell 사용

브라우저를 통해 Azure 호스팅 대화형 셸 환경인 [Azure Cloud Shell](https://shell.azure.com/)을 사용하여 CLI 명령을 실행할 수 있습니다. Azure Cloud Shell은 Azure 서비스에서 Bash 또는 Windows PowerShell을 지원합니다. Azure CLI가 사전 설치되어 계정에서 사용하도록 구성됩니다. Azure Portal의 오른쪽 위 섹션에 있는 메뉴에서 Cloud Shell 단추를 선택합니다.

![Cloud Shell 메뉴 선택](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

이 단추는 이 방법 문서에 설명된 단계를 실행하는 데 사용할 수 있는 대화형 셸을 시작합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Azure PowerShell의 경우

시작하기 전에 다음을 수행합니다.

* Windows PowerShell 6.2.4 이상을 설치합니다.
* Azure PowerShell(AZ) 모듈을 설치합니다.
* Azure Data Box(Az.DataBox) 모듈을 설치합니다.
* Azure에 로그인합니다.

#### <a name="install-azure-powershell-and-modules-locally"></a>Azure PowerShell 및 모듈을 로컬로 설치

**Windows PowerShell 설치 또는 업그레이드**

Windows PowerShell version 6.2.4 이상이 설치되어 있어야 합니다. 설치한 PowerShell 버전을 확인하려면 `$PSVersionTable`을 실행합니다.

다음 출력이 표시됩니다.

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

버전이 6.2.4보다 낮은 경우 Windows PowerShell 버전을 업그레이드해야 합니다. PowerShell 최신 버전을 설치하려면 [Azure PowerShell 설치](/powershell/scripting/install/installing-powershell)를 참조하세요.

**Azure PowerShell 및 Data Box 모듈 설치**

Azure PowerShell를 사용하여 Azure Data Box를 주문하려면 Azure PowerShell 모듈을 설치해야 합니다. Azure PowerShell 모듈을 설치하는 방법은 다음과 같습니다.

1. [Azure PowerShell Az 모듈](/powershell/azure/new-azureps-module-az)을 설치합니다.
2. 그런 다음, `Install-Module -Name Az.DataBox` 명령을 사용하여 Az.DataBox를 설치합니다.

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Azure에 로그인

Windows PowerShell 명령 창을 열고 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) 명령을 사용하여 Azure에 로그인합니다.

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

다음은 성공적인 로그인의 출력입니다.

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Windows PowerShell을 사용하여 Azure에 로그인하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 로그인](/powershell/azure/authenticate-azureps)을 참조하세요.

---

## <a name="order-data-box"></a>Data Box 주문

# <a name="portal"></a>[포털](#tab/portal)

디바이스를 주문하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 다음 URL에서 로그인합니다. [https://portal.azure.com](https://portal.azure.com)
2. **+ 리소스 만들기** 를 선택하고 *Azure Data Box* 를 검색합니다. **Azure Data Box** 를 선택합니다.

   ![검색 필드에 Azure Data Box가 있는 새 섹션의 스크린샷](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. **만들기** 를 선택합니다.

   ![만들기 옵션이 호출된 Azure Data Box 섹션의 스크린샷](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. 해당 지역에서 Data Box 서비스를 사용할 수 있는지 확인합니다. 다음 정보를 입력하거나 선택하고 **적용** 을 클릭합니다.

    |설정  |값  |
    |---------|---------|
    |전송 형식     | **Azure로 가져오기** 를 선택합니다.        |
    |Subscription     | Data Box 서비스에 대한 EA, CSP 또는 Azure 스폰서쉽 구독을 선택합니다. <br> 구독은 대금 청구 계정에 연결됩니다.       |
    |리소스 그룹 | 기존 리소스 그룹을 선택합니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. |
    |원본 국가/지역    |    현재 데이터가 있는 국가/지역을 선택합니다.         |
    |대상 Azure 지역     |     데이터를 전송하려는 Azure 지역을 선택합니다. <br> 자세한 내용은 [지역 가용성](data-box-overview.md#region-availability)을 참조하세요.            |

    [ ![Azure Data Box 가져오기 주문 시작](media/data-box-deploy-ordered/select-data-box-import-04-b.png) ](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. **Data Box** 를 선택합니다. 단일 주문의 최대 사용 가능한 용량은 80TB입니다. 더 큰 데이터 크기에 대해 여러 개의 주문을 만들 수 있습니다.

    ![사용 가능한 데이터 크기: Data Box Disk, 40테라바이트, Data Box, 100테라바이트, Data Box Heavy, 1000테라바이트, 자신의 디스크 보내기, 1테라바이트](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. **주문** 에서 **기본 사항** 탭으로 이동합니다. 다음 정보를 입력하거나 선택하고 **다음: 데이터 대상>** 을 선택합니다.

    |설정  |값  |
    |---------|---------|
    |Subscription      | 이전에 선택한 내용에 따라 구독이 자동으로 채워집니다.|
    |Resource group    | 이전에 선택한 리소스 그룹입니다. |
    |주문 이름 가져오기 | 주문을 추적하는 데 친숙한 이름을 입력합니다. <br> 이 이름은 2~24자 사이의 문자, 숫자 및 하이픈일 수 있습니다. <br> 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.    |

    ![Data Box 가져오기 주문 마법사, 기본 사항 화면, 올바른 정보 입력](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. **데이터 대상** 화면에서 **데이터 대상**(스토리지 계정 또는 관리 디스크)을 선택합니다.

    스토리지 대상으로 **스토리지 계정** 을 사용하는 경우 다음 화면이 표시됩니다.

    ![Data Box 가져오기 주문 마법사, 데이터 대상 화면(스토리지 계정이 선택됨)](media/data-box-deploy-ordered/select-data-box-import-07.png)

    지정된 Azure 지역에 따라 필터링된 기존 스토리지 계정 목록에서 하나 이상의 스토리지 계정을 선택합니다. Data Box는 최대 10개의 스토리지 계정과 연결할 수 있습니다. 새 **범용 v1**, **범용 v2** 또는 **Blob Storage 계정** 도 만들 수 있습니다.

   > [!NOTE]
   > - Azure Premium FileStorage 계정을 선택하면 스토리지 계정 공유의 프로비저닝된 할당량이 파일 공유에 복사되는 데이터의 크기에 따라 증가합니다. 예를 들어 어떤 이유로 Data Box에서 데이터를 복사할 수 없는 경우 할당량이 증가된 후에는 다시 조정되지 않습니다.
   > - 이 할당량은 요금 청구에 사용됩니다. 데이터가 데이터 센터에 업로드된 후에는 요구 사항에 맞게 할당량을 조정해야 합니다. 자세한 내용은 [요금 청구 이해](../../articles/storage/files/understanding-billing.md)를 참조하세요.

    가상 네트워크를 사용하는 스토리지 계정은 지원됩니다. Data Box 서비스에서 보안 스토리지 계정을 사용하려면 스토리지 계정 네트워크 방화벽 설정 내에서 신뢰할 수 있는 서비스를 사용하도록 설정합니다. 자세한 내용은 [Azure Data Box를 신뢰할 수 있는 서비스로 추가](../storage/common/storage-network-security.md#exceptions)하는 방법을 참조하세요.

    Data Box를 사용하여 온-프레미스 VHD(가상 하드 디스크)에서 **관리 디스크** 를 만드는 경우에도 다음 정보를 제공해야 합니다.

    |설정  |값  |
    |---------|---------|
    |리소스 그룹     | 온-프레미스 VHD에서 관리형 디스크를 만들려는 경우 새 리소스 그룹을 만듭니다. 이전에 Data Box 서비스를 통해 관리 디스크에 대한 Data Box 주문을 만들 때 리소스 그룹을 만든 경우에만 기존 리소스 그룹을 사용할 수 있습니다. <br> 세미콜론으로 구분해서 여러 리소스 그룹을 지정합니다. 최대 10개의 리소스 그룹이 지원됩니다.|

    ![Data Box 가져오기 주문 마법사, 데이터 대상 화면(Managed Disks가 선택됨)](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    관리형 디스크에 대해 지정한 스토리지 계정은 스테이징 스토리지 계정으로 사용됩니다. Data Box 서비스는 VHD를 관리형 디스크로 변환한 후 리소스 그룹으로 이동하기 전에 페이지 Blob으로 스테이징 스토리지 계정에 업로드합니다. 자세한 내용은 [Azure에 대한 데이터 업로드 확인](data-box-deploy-picked-up.md#verify-data-upload-to-azure)을 참조하세요.

   > [!NOTE]
   > 페이지 Blob이 관리 디스크로 성공적으로 변환되지 않은 경우 스토리지 계정에 유지되고 스토리지에 대한 요금이 청구됩니다.

8. 계속하려면 **다음: 보안** 을 선택합니다.

    **보안** 화면을 사용하면 사용자 고유의 암호화 키 및 디바이스를 사용하고, 암호를 공유하며, 이중 암호화를 사용하도록 선택할 수 있습니다.

    **보안** 화면의 모든 설정은 선택 사항입니다. 설정을 변경하지 않으면 기본 설정이 적용됩니다.

    ![Data Box 가져오기 주문 마법사의 보안 화면](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. 사용자 고유의 고객 관리형 키를 사용하여 새 리소스에 대한 잠금 해제 암호를 보호하려면 **암호화 유형** 을 펼칩니다.

    Azure Data Box에 대한 고객 관리형 키 구성은 선택 사항입니다. 기본적으로 Data Box는 Microsoft 관리형 키를 사용하여 잠금 해제 암호를 보호합니다.

    고객 관리형 키는 디바이스의 데이터가 암호화되는 방식에 영향을 주지 않습니다. 키는 디바이스 잠금 해제 암호를 암호화하는 데만 사용됩니다.

    고객 관리형 키를 사용하지 않으려면 15단계로 건너뜁니다.

   ![암호화 유형 설정을 보여 주는 보안 화면](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. 키 유형으로 **고객 관리형 키** 를 선택합니다. 그런 다음, **Key Vault 및 키 선택** 을 선택합니다.
   
    ![보안 화면, 고객 관리형 키 설정](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. **Azure Key Vault에서 키 선택** 블레이드에서 구독이 자동으로 채워집니다.

    - **키 자격 증명 모음** 의 드롭다운 목록에서 기존 키 자격 증명 모음을 선택할 수 있습니다.

      ![Azure Key Vault에서 키 선택 화면](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - **새로 만들기** 를 선택하여 새로운 키 자격 증명 모음을 만들 수도 있습니다. **키 자격 증명 모음 만들기** 화면에서 리소스 그룹 및 키 자격 증명 모음 이름을 입력합니다. **일시 삭제** 및 **보호 제거** 를 사용하도록 설정되어 있는지 확인합니다. 다른 모든 기본값을 적용하고, **검토 + 만들기** 를 선택합니다.

      ![새 Azure Key Vault 설정 만들기](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      키 자격 증명 모음에 대한 정보를 검토하고, **만들기** 를 선택합니다. 키 자격 증명 모음 만들기가 완료될 때까지 몇 분 정도 기다립니다.

      ![새 Azure Key Vault 검토 화면](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. **Azure Key Vault에서 키 선택** 에서 키 자격 증명 모음의 기존 키를 선택할 수 있습니다.

    ![Azure Key Vault에서 기존 키 선택](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    새 키를 만들려면 **새로 만들기** 를 선택합니다. RSA 키를 사용해야 합니다. 크기는 2048 이상일 수 있습니다. 새 키에 대한 이름을 입력하고, 다른 기본값을 적용한 다음, **만들기** 를 선택합니다.

      ![새 키 만들기 옵션](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      키가 키 자격 증명 모음에 만들어지면 알림이 표시됩니다.

13. 사용할 키의 **버전** 을 선택한 다음, **선택** 을 선택합니다.

      ![키 자격 증명 모음에 새로 생성된 키](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    새 키 버전을 만들려면 **새로 만들기** 를 선택합니다.

    ![새 키 버전을 만들기 위한 대화 상자 열기](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    새 키 버전에 대한 설정을 선택하고,**만들기** 를 선택합니다.

    ![새로운 키 버전 만들기](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    **보안** 화면의 **암호화 유형** 설정에 키 자격 증명 모음 및 키가 표시됩니다.

    ![고객 관리형 키에 대한 키 및 키 자격 증명 모음](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. 이 리소스에 대한 액세스를 관리하는 데 사용할 사용자 ID를 선택합니다. **사용자 ID 선택** 을 선택합니다. 오른쪽 패널에서 사용할 구독 및 관리 ID를 선택합니다. 그런 다음, **선택** 을 선택합니다.

    사용자가 할당한 관리 ID는 여러 리소스를 관리하는 데 사용할 수 있는 독립 실행형 Azure 리소스입니다. 자세한 내용은 [관리 ID 유형](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.  

    새 관리 ID를 만들어야 하는 경우 [Azure Portal을 사용하여 사용자가 할당한 관리 ID에 역할 만들기, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)의 지침을 따릅니다.
    
    ![사용자 ID 선택](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    사용자 ID는 **암호화 유형** 설정에 표시됩니다.

    ![암호화 유형 설정에 표시된 선택한 사용자 ID](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Azure Data Box에서 기본적으로 사용하는 시스템 생성 암호를 사용하지 않으려면 **보안** 화면에서 **사용자 고유 암호 가져오기** 를 펼칩니다.

    시스템 생성 암호는 안전하며, 조직에서 달리 요구하는 경우가 아니면 사용하는 것이 좋습니다.

    ![펼쳐진 Data Box 가져오기 주문에 대한 사용자 고유 암호 가져오기 옵션](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - 사용자 고유 암호를 새 디바이스에 사용하려면 **디바이스 암호에 대한 기본 설정 지정** 에서 **사용자 고유 암호 사용** 을 선택하고 보안 요구 사항을 충족하는 암호를 입력합니다.
     
     암호는 영숫자여야 하며 최소한 대문자 1개, 소문자 1개, 특수 문자 1개, 숫자 1개로 구성된 12~15자여야 합니다. 

     - 허용되는 특수 문자: @ # - $ % ^ ! + = ; : _ ( )
     - 허용되지 않는 문자: I i L o O 0
   
     ![Data Box 가져오기 주문에 대한 보안 화면에서 사용자 고유 디바이스 암호를 사용하기 위한 옵션](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - 사용자 고유 암호를 공유에 사용하려면 다음을 수행합니다.

   1. **공유 암호에 대한 기본 설정 지정** 에서 **사용자 고유 암호 사용** 및 **공유 암호 선택** 을 선택합니다.
     
       ![Data Box 가져오기 주문에 대한 보안 화면에서 사용자 고유 공유 암호를 사용하기 위한 옵션](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    1. 각 스토리지 계정에 대한 암호를 순서대로 입력합니다. 암호가 스토리지 계정에 대한 모든 공유에서 사용됩니다.
    
       암호는 영숫자여야 하며 최소한 대문자 1개, 소문자 1개, 특수 문자 1개, 숫자 1개로 구성된 12~64자여야 합니다.

       - 허용되는 특수 문자: @ # - $ % ^ ! + = ; : _ ( )
       - 허용되지 않는 문자: I i L o O 0
     
    1. 동일한 암호를 모든 스토리지 계정에 사용하려면 **모두에 복사** 를 선택합니다. 

    1. 완료되면 **저장** 을 선택합니다.
     
       ![Data Box 가져오기 주문에 대한 공유 암호를 입력하는 화면](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

    **보안** 화면에서 **암호 보기 또는 변경** 을 사용하여 암호를 변경할 수 있습니다.

16. 소프트웨어 기반 이중 암호화를 사용하도록 설정하려면 **보안** 에서 **이중 암호화(매우 안전한 환경용)** 를 펼치고 **주문에 이중 암호화 사용** 을 선택합니다.

    ![Data Box 가져오기에 대한 보안 화면, Data Box 주문에 소프트웨어 기반 암호화 사용](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    소프트웨어 기반 암호화는 Data Box에 있는 데이터의 AES-256비트 암호화와 함께 수행됩니다.

    > [!NOTE]
    > 이 옵션을 사용하도록 설정하면 주문 처리 및 데이터 복사 시간이 더 길어질 수 있습니다. 주문을 만든 후에는 이 옵션을 변경할 수 없습니다.

    완료되면 **다음: 연락처 정보** 를 선택하여 계속 진행합니다.

17. **연락처 정보** 에서 **+ 배송 주소 추가** 를 선택합니다.

    ![연락처 세부 정보 화면에서 Azure Data Box 가져오기 주문에 배송 주소를 추가합니다.](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. **배송 주소** 에 사용자의 성과 이름, 회사의 이름과 우편 주소 및 유효한 전화 번호를 입력합니다. **주소 확인** 을 선택합니다. 서비스에서 서비스 가용성을 위해 배송 주소의 유효성을 검사합니다. 지정한 배송 주소에 대해 서비스를 사용할 수 있으면 해당 알림을 받게 됩니다.

    ![배송 사용 옵션 및 배송 주소 추가 옵션이 호출된 배송 주소 추가 대화 상자의 스크린샷](media/data-box-deploy-ordered/select-data-box-import-10.png)

    주문이 성공적으로 발주된 후 자체 관리형 배송을 선택하면 이메일 알림을 받게 됩니다. 자체 관리형 배송에 대한 자세한 내용은 [자체 관리형 배송 사용](data-box-portal-customer-managed-shipping.md)을 참조하세요.

19. 배송 정보가 성공적으로 확인되면 **배송 주소 추가** 을 선택합니다. **연락처 정보** 탭으로 이동됩니다.

20. **연락처 정보** 탭으로 돌아가면 하나 이상의 이메일 주소를 추가합니다. 서비스에서는 주문 상태에 대한 모든 업데이트와 관련된 이메일 알림을 지정한 이메일 주소로 보냅니다.

    그룹의 관리자가 떠나는 경우에도 계속 알림을 받으려면 그룹 이메일을 사용하는 것이 좋습니다.

    ![주문 마법사의 연락처 세부 정보에 대한 이메일 섹션](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. **검토 + 주문** 에서 주문, 연락처, 알림 및 개인 정보 사용 약관과 관련된 정보를 검토합니다. 개인 정보 처리 방침에 대한 계약에 해당하는 확인란을 선택합니다.

22. **주문** 을 선택합니다. 주문을 만드는 데 몇 분 정도 걸립니다.

    ![주문 마법사의 검토 및 주문 화면](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

디바이스를 주문하려면 Azure CLI를 사용하여 다음 단계를 수행합니다.

1. Data Box 주문에 대한 설정을 적습니다. 이러한 설정에는 개인/회사 정보, 구독 이름, 디바이스 정보 및 배송 정보가 포함됩니다. CLI 명령을 실행하여 Data Box 주문을 작성할 때 이러한 설정을 매개 변수로 사용해야 합니다. 다음 표에서는 `az databox job create`에 사용되는 매개 변수 설정을 보여줍니다.

   | 설정(매개 변수) | Description |  샘플 값 |
   |---|---|---|
   |resource-group| 기존 그룹을 사용하거나 새 그룹을 만듭니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |name| 작성하는 주문의 이름입니다. | "mydataboxorder"|
   |contact-name| 배송 주소와 연결된 이름입니다. | "Gus Poland"|
   |phone| 주문한 제품을 받을 사람 또는 회사의 전화 번호입니다.| "14255551234"
   |위치| 디바이스를 배송할 가장 가까운 Azure 지역입니다.| "미국 서부"|
   |sku| 주문하는 특정 Data Box 디바이스입니다. 유효한 값은 다음과 같습니다. "DataBox", "DataBoxDisk" 및 "DataBoxHeavy"| "DataBox" |
   |email-list| 주문과 연결된 이메일 주소입니다.| "gusp@contoso.com" |
   |street-address1| 주문한 제품이 배송되는 주소입니다. | "15700 NE 39th St" |
   |street-address2| 아파트 번호 또는 건물 번호와 같은 보조 주소 정보입니다. | "빌딩 123" |
   |city| 디바이스가 배송될 도시입니다. | "Redmond" |
   |state-or-province| 디바이스가 배송될 시/도입니다.| "WA" |
   |country| 디바이스가 배송될 국가입니다. | "미국" |
   |postal-code| 배송 주소와 연결된 우편 번호입니다.| "98052"|
   |company-name| 근무하는 회사의 이름입니다.| "Contoso, LTD" |
   |스토리지 계정 만들기| 데이터를 가져올 Azure Storage 계정입니다.| "mystorageaccount"|
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다.  | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

2. 선택한 명령 프롬프트 또는 터미널에서 [az databox job create](/cli/azure/databox/job#az_databox_job_create)를 실행하여 Azure Data Box 주문을 만듭니다.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   다음은 명령 사용의 예입니다.

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. 모든 Azure CLI 명령은 사용자가 변경하지 않는 한 기본적으로 json을 출력 형식으로 사용합니다. 글로벌 매개 변수 `--output <output-format>`을 사용하여 출력 형식을 변경할 수 있습니다. 형식을 "table"로 변경하면 출력 가독성이 향상됩니다.

   다음은 방금 실행한 명령을 약간 조정하여 형식을 변경하는 명령입니다.

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   다음은 명령 실행의 출력입니다.

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

디바이스를 주문하려면 Azure PowerShell을 사용하여 다음 단계를 수행합니다.

1. 가져오기 주문을 작성하려면 스토리지 계정을 가져오고 스토리지 계정 개체를 변수에 저장해야 합니다.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Data Box 주문에 대한 설정을 적습니다. 이러한 설정에는 개인/회사 정보, 구독 이름, 디바이스 정보 및 배송 정보가 포함됩니다. PowerShell 명령을 실행하여 Data Box 주문을 작성할 때 이러한 설정을 매개 변수로 사용해야 합니다. 다음 표에서는 [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob)에 사용되는 매개 변수 설정을 보여줍니다.

    | 설정(매개 변수) | Description |  샘플 값 |
    |---|---|---|
    |ResourceGroupName [필수]| 기존 리소스 그룹을 사용합니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
    |Name [필수]| 작성하는 주문의 이름입니다. | "mydataboxorder"|
    |ContactName [필수]| 배송 주소와 연결된 이름입니다. | "Gus Poland"|
    |PhoneNumber [필수]| 주문한 제품을 받을 사람 또는 회사의 전화 번호입니다.| "14255551234"
    |Location [필수]| 디바이스를 배송할 가장 가까운 Azure 지역입니다.| "WestUS"|
    |DataBoxType [필수]| 주문하는 특정 Data Box 디바이스입니다. 유효한 값은 다음과 같습니다. "DataBox", "DataBoxDisk" 및 "DataBoxHeavy"| "DataBox" |
    |EmailId [필수]| 주문과 연결된 이메일 주소입니다.| "gusp@contoso.com" |
    |StreetAddress1 [필수]| 주문한 제품이 배송되는 주소입니다. | "15700 NE 39th St" |
    |StreetAddress2| 아파트 번호 또는 건물 번호와 같은 보조 주소 정보입니다. | "빌딩 123" |
    |StreetAddress3| 3차 주소 정보입니다. | |
    |City [필수]| 디바이스가 배송될 도시입니다. | "Redmond" |
    |StateOrProvinceCode [필수]| 디바이스가 배송될 시/도입니다.| "WA" |
    |CountryCode [필수]| 디바이스가 배송될 국가입니다. | "미국" |
    |PostalCode [필수]| 배송 주소와 연결된 우편 번호입니다.| "98052"|
    |CompanyName| 근무하는 회사의 이름입니다.| "Contoso, LTD" |
    |StorageAccountResourceId [필수]| 데이터를 가져올 Azure Storage 계정 ID입니다.| <AzStorageAccount>.id |

3. 원하는 명령 프롬프트 또는 터미널에서 [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob)을 사용하여 Azure Data Box 주문을 작성합니다.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   다음은 명령 실행의 출력입니다.

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>주문 추적

# <a name="portal"></a>[포털](#tab/portal)

주문이 완료되면 Azure Portal에서 주문 상태를 추적할 수 있습니다. Data Box 주문, **개요** 로 차례로 이동하여 상태를 확인합니다. 포털에서는 해당 주문을 **주문됨** 상태로 표시합니다.

디바이스를 사용할 수 없는 경우 알림을 받습니다. 디바이스를 사용할 수 있으면 Microsoft에서는 배송할 디바이스를 확인하고 배송을 준비합니다. 디바이스를 준비하는 동안 수행되는 작업은 다음과 같습니다.

* 디바이스와 연결된 각 스토리지 계정에 대해 SMB 공유가 생성됩니다.
* 각 공유에 대한 사용자 이름 및 암호와 같은 액세스 자격 증명이 생성됩니다.
* 디바이스의 잠금을 해제하는 데 도움이 되는 디바이스 암호도 생성됩니다.
* Data Box는 언제든지 디바이스에 대한 권한이 없는 액세스를 방지하기 위해 잠겨 있습니다.

디바이스 준비가 완료되면 포털에서는 해당 주문을 **처리됨** 상태로 표시합니다.

![처리된 Data Box 주문](media/data-box-overview/data-box-order-status-processed.png)

그런 다음, Microsoft에서는 디바이스를 준비하고 지역 배송업체를 통해 발송합니다. 디바이스가 배송되면 추적 번호를 받게 됩니다. 포털에서 해당 작업을 **발송됨** 상태로 표시합니다.

![발송된 Data Box 주문](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>단일 주문 추적

단일 기존 Azure Data Box 주문에 대한 추적 정보를 가져오려면 [`az databox job show`](/cli/azure/databox/job#az_databox_job_show)를 실행합니다. 이 명령은 이름, 리소스 그룹, 추적 정보, 구독 ID, 연락처 정보, 배송 유형, 디바이스 sku 등의 주문 정보를 표시합니다.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   다음 표에서는 `az databox job show`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |resource-group [필수]| 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |name [필수]| 표시할 주문의 이름입니다. | "mydataboxorder"|
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다. | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

   다음은 출력 형식이 "table"로 설정된 명령의 예입니다.

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   다음은 명령 실행의 출력입니다.

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> 주문 나열은 구독 수준에서 지원할 수 있으며, 따라서 리소스 그룹은 필수 매개 변수가 아닌 선택적 매개 변수입니다.

### <a name="list-all-orders"></a>모든 주문 나열

여러 디바이스를 주문한 경우 [`az databox job list`](/cli/azure/databox/job#az_databox_job_list)을 실행하여 모든 Azure Data Box 주문을 볼 수 있습니다. 이 명령은 특정 리소스 그룹에 속한 모든 주문을 나열합니다. 또한 주문 이름, 배송 상태, Azure 지역, 배달 유형, 주문 상태가 출력에 표시됩니다. 취소된 주문도 목록에 포함됩니다.
뿐만 아니라 이 명령은 각 주문의 타임스탬프를 표시합니다.

```azurecli
az databox job list --resource-group <resource-group>
```

다음 표에서는 `az databox job list`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |resource-group [필수]| 주문을 포함하는 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다. | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

   다음은 출력 형식이 "table"로 설정된 명령의 예입니다.

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>단일 주문 추적

단일 기존 Azure Data Box 주문에 대한 추적 정보를 가져오려면 [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob)을 실행합니다. 이 명령은 이름, 리소스 그룹, 추적 정보, 구독 ID, 연락처 정보, 배송 유형, 디바이스 sku 등의 주문 정보를 표시합니다.

> [!NOTE]
> `Get-AzDataBoxJob`은 단일 주문과 여러 주문을 표시하는 데 모두 사용됩니다. 차이점은 단일 주문의 주문 이름을 지정한다는 것입니다.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   다음 표에서는 `Get-AzDataBoxJob`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |ResourceGroup [필수]| 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |Name [필수]| 정보를 가져올 주문의 이름입니다. | "mydataboxorder"|
   |ResourceId| 주문과 연결된 리소스 ID입니다. |  |

   다음은 출력이 있는 명령의 예입니다.

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>모든 주문 나열

여러 디바이스를 주문한 경우 [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob)을 실행하여 모든 Azure Data Box 주문을 볼 수 있습니다. 이 명령은 특정 리소스 그룹에 속한 모든 주문을 나열합니다. 또한 주문 이름, 배송 상태, Azure 지역, 배달 유형, 주문 상태가 출력에 표시됩니다. 취소된 주문도 목록에 포함됩니다.
뿐만 아니라 이 명령은 각 주문의 타임스탬프를 표시합니다.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

다음은 명령의 예제입니다.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

다음은 명령 실행의 출력입니다.

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>주문 취소

# <a name="portal"></a>[포털](#tab/portal)

이 주문을 취소하려면 Azure Portal에서 **개요** 로 이동하고, 명령 모음에서 **취소** 를 선택합니다.

주문이 완료되면 주문 상태가 처리됨으로 표시되기 전까지는 취소할 수 있습니다.

취소된 주문을 삭제하려면 **개요** 로 이동하고, 명령 모음에서 **삭제** 를 선택합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>주문 취소

Azure Data Box 주문을 취소하려면 [`az databox job cancel`](/cli/azure/databox/job#az_databox_job_cancel)를 실행합니다. 주문을 취소하는 이유를 지정해야 합니다.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   다음 표에서는 `az databox job cancel`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |resource-group [필수]| 삭제할 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |name [필수]| 삭제할 주문의 이름입니다. | "mydataboxorder"|
   |reason [필수]| 주문을 취소하는 이유입니다. | "잘못된 정보를 입력하여 주문을 취소해야 합니다." |
   |예| 확인을 묻는 메시지를 표시하지 마세요. | --yes (-y)| 
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다. | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

   다음은 출력이 있는 명령의 예입니다.

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>주문 삭제

Azure Data Box 주문을 취소한 경우 [`az databox job delete`](/cli/azure/databox/job#az_databox_job_delete)을 실행하여 주문을 삭제할 수 있습니다.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   다음 표에서는 `az databox job delete`에 대한 매개 변수 정보를 보여줍니다.

   | 매개 변수 | Description |  샘플 값 |
   |---|---|---|
   |resource-group [필수]| 삭제할 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
   |name [필수]| 삭제할 주문의 이름입니다. | "mydataboxorder"|
   |subscription| Azure 구독의 이름 또는 ID(GUID)입니다. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |예| 확인을 묻는 메시지를 표시하지 마세요. | --yes (-y)|
   |debug| 자세한 정보 로깅을 위한 디버깅 정보를 포함합니다. | --debug |
   |help| 이 명령에 대한 도움말 정보를 표시합니다. | --help -h |
   |only-show-errors| 오류만 표시하고, 경고를 표시하지 않습니다. | --only-show-errors |
   |output -o| 출력 형식을 설정합니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc. 기본값은 json입니다. | --output "json" |
   |Query| JMESPath 쿼리 문자열입니다. 자세한 내용은 [JMESPath](http://jmespath.org/)를 참조하세요. | --query <string>|
   |verbose| 자세한 정보 로깅을 포함합니다. | --verbose |

다음은 출력이 있는 명령의 예입니다.

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   다음은 명령 실행의 출력입니다.

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>주문 취소

Azure Data Box 주문을 취소하려면 [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob)을 실행합니다. 주문을 취소하는 이유를 지정해야 합니다.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

다음 표에서는 `Stop-AzDataBoxJob`에 대한 매개 변수 정보를 보여줍니다.

| 매개 변수 | Description |  샘플 값 |
|---|---|---|
|ResourceGroup [필수]| 취소할 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
|Name [필수]| 삭제할 주문의 이름입니다. | "mydataboxorder"|
|Reason [필수]| 주문을 취소하는 이유입니다. | "잘못된 정보를 입력하여 주문을 취소해야 합니다." |
|Force | 사용자 확인 없이 cmdlet을 강제로 실행합니다. | -Force |

다음은 출력이 있는 명령의 예입니다.

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

다음은 명령 실행의 출력입니다.

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>주문 삭제

Azure Data Box 주문을 취소한 경우 [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob)을 실행하여 주문을 삭제할 수 있습니다.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

다음 표에서는 `Remove-AzDataBoxJob`에 대한 매개 변수 정보를 보여줍니다.

| 매개 변수 | Description |  샘플 값 |
|---|---|---|
|ResourceGroup [필수]| 삭제할 주문과 연결된 리소스 그룹의 이름입니다. 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. | "myresourcegroup"|
|Name [필수]| 삭제할 주문의 이름입니다. | "mydataboxorder"|
|Force | 사용자 확인 없이 cmdlet을 강제로 실행합니다. | -Force |

다음은 출력이 있는 명령의 예입니다.

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

다음은 명령 실행의 출력입니다.

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 문서의 다음 내용을 알아보았습니다.

> [!div class="checklist"]
>
> * Data Box를 배포하기 위한 필수 구성 요소
> * Data Box 주문
> * 주문 추적
> * 주문 취소

Data Box를 설정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box 설정](./data-box-deploy-set-up.md)
