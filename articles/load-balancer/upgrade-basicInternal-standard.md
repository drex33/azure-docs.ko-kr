---
title: Basic Internal에서 Standard Internal로 업그레이드 - Azure Load Balancer
description: 이 문서에서는 Azure Internal Load Balancer를 Basic SKU에서 Standard SKU로 업그레이드하는 방법을 보여 줍니다.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 1b7bdbdb9e1d642f2ef4a715d4993e4f449ccd0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050700"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure Internal Load Balancer 업그레이드 - 아웃바운드 연결 필요 없음
[Azure 표준 Load Balancer](load-balancer-overview.md)는 영역 중복을 통해 다양한 기능과 고가용성을 제공합니다. Load Balancer SKU에 대한 자세한 내용은 [비교 표](./skus.md#skus)를 참조하세요.

이 문서에서는 기본 Load Balancer에서 표준 Load Balancer로 트래픽을 마이그레이션하는 방법과 함께 기본 Load Balancer와 동일한 구성을 사용하여 표준 Load Balancer를 만드는 PowerShell 스크립트를 소개합니다.

## <a name="upgrade-overview"></a>업그레이드 개요

다음을 수행하는 Azure PowerShell 스크립트를 사용할 수 있습니다.

* 지정하는 위치에 Standard Internal SKU Load Balancer를 만듭니다. Standard Internal Load Balancer에서는 [아웃바운드 연결](./load-balancer-outbound-connections.md)을 제공하지 않습니다.
* Basic SKU Load Balancer의 구성을 새로 만든 표준 Load Balancer에 원활하게 복사합니다.
* 기본 Load Balancer에서 새로 만든 표준 Load Balancer로 프라이빗 IP를 원활하게 이동합니다.
* 기본 Load Balancer의 백 엔드 풀에서 표준 Load Balancer의 백 엔드 풀로 VM을 원활하게 이동합니다.

### <a name="caveatslimitations"></a>주의 사항/제한 사항

* 스크립트는 아웃바운드 연결이 필요하지 않은 내부 Load Balancer 업그레이드만 지원합니다. 일부 VM에 [아웃바운드 연결](./load-balancer-outbound-connections.md)이 필요한 경우 지침은 이 [페이지](upgrade-InternalBasic-To-PublicStandard.md)를 참조하세요. 
* 기본 Load Balancer는 백 엔드 VM 및 NIC와 동일한 리소스 그룹에 있어야 합니다.
* 표준 Load Balancer를 다른 지역에서 만든 경우 이전 지역에 존재하는 VM을 새로 만든 표준 Load Balancer에 연결할 수 없습니다. 이 제한 사항을 해결하려면 새 지역에 새 VM을 만들어야 합니다.
* Load Balancer에 프런트 엔드 IP 구성 또는 백 엔드 풀이 없는 경우 스크립트를 실행하는 동안 오류가 발생할 수 있습니다. 비어 있지 않은지 확인합니다.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>프런트 엔드 IP 구성을 위해 IP 할당 방법을 정적으로 변경(이미 정적인 경우 이 단계 무시)

1. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 기본 Load Balancer를 선택합니다.

2. **설정** 에서 **프런트 엔드 IP 구성** 을 선택하고 첫 번째 프런트 엔드 IP 구성을 선택합니다. 

3. **할당** 에서 **정적** 을 선택합니다.

4. 기본 Load Balancer의 모든 프런트 엔드 IP 구성에 대해 3단계를 반복합니다.


## <a name="download-the-script"></a>스크립트 다운로드

[PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0)에서 마이그레이션 스크립트를 다운로드합니다.
## <a name="use-the-script"></a>스크립트를 사용합니다.

로컬 PowerShell 환경 설정 및 기본 설정에 따라 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

* Azure Az 모듈이 설치되어 있지 않거나 Azure Az 모듈을 제거해도 상관없는 경우 `Install-Script` 옵션을 사용하여 스크립트를 실행하는 것이 가장 좋습니다.
* Azure Az 모듈을 유지해야 하는 경우에는 스크립트를 다운로드하여 직접 실행하는 것이 가장 좋습니다.

Azure Az 모듈이 설치되어 있는지 확인하려면 `Get-InstalledModule -Name az`을(를) 실행합니다. 설치된 Az modules가 표시되지 않으면 `Install-Script` 메서드를 사용할 수 있습니다.

### <a name="install-using-the-install-script-method"></a>Install-Script 메서드를 사용하여 설치

이 옵션을 사용하려면 컴퓨터에 Azure Az 모듈이 설치되어 있지 않아야 합니다. 설치되어 있는 경우 다음 명령이 오류를 표시합니다. Azure Az 모듈을 제거하거나 다른 옵션을 사용하여 스크립트를 수동으로 다운로드하여 실행할 수 있습니다.
  
다음 명령을 사용하여 스크립트를 실행합니다.

`Install-Script -Name AzureILBUpgrade`

또한 이 명령은 필요한 Az 모듈을 설치합니다.  

### <a name="install-using-the-script-directly"></a>스크립트를 직접 사용한 설치

일부 Azure Az 모듈이 설치되어 있고 제거할 수 없는 경우(또는 제거하지 않으려는 경우) 스크립트 다운로드 링크의 **수동 다운로드** 탭을 사용하여 스크립트를 수동으로 다운로드할 수 있습니다. 이 스크립트는 원시 nupkg 파일로 다운로드됩니다. 이 nupkg 파일에서 스크립트를 설치하려면 [수동 패키지 다운로드](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)를 참조하세요.

스크립트를 실행하려면

1. `Connect-AzAccount`을(를) 사용하여 Azure에 연결합니다.

1. `Import-Module Az`을(를) 사용하여 Az 모듈을 가져옵니다.

1. 필수 매개 변수를 검토합니다.

   * **rgName: [문자열]: 필수** – 기존의 기본 Load Balancer 및 새 표준 Load Balancer의 리소스 그룹입니다. 이 문자열 값을 찾으려면 Azure Portal로 이동하고, 기본 Load Balancer 원본을 선택한 다음, Load Balancer에 대 한 **개요** 를 클릭합니다. 리소스 그룹은 해당 페이지에 있습니다.
   * **oldLBName: [문자열]: 필수** – 업그레이드하려는 기존 Basic Balancer의 이름입니다. 
   * **newlocation: [문자열]: 필수** - 표준 Load Balancer가 만들어질 위치입니다. 다른 기존 리소스와의 연결을 개선하기 위해 선택한 기본 Load Balancer의 동일한 위치를 표준 Load Balancer에 상속하는 것이 좋습니다.
   * **newLBName: [문자열]: 필수** – 만들려는 표준 Load Balancer의 이름입니다.
1. 적절한 매개 변수를 사용하여 스크립트를 실행합니다. 완료하는 데 5~7분 정도 걸릴 수 있습니다.

    **예제**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg&quot; -oldLBName &quot;LBForInternal&quot; -newlocation &quot;centralus&quot; -newLbName &quot;LBForUpgrade"
   ```

## <a name="common-questions"></a>일반적인 질문

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>v1에서 v2로 구성을 마이그레이션하기 위한 Azure PowerShell 스크립트에 제한 사항이 있나요?

예. [주의 사항/제한 사항](#caveatslimitations)을 참조하세요.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>또한 Azure PowerShell 스크립트는 기본 Load Balancer에서 새로 만든 표준 Load Balancer로 트래픽을 전환하나요?

예, 트래픽을 마이그레이션합니다. 트래픽을 개인적으로 마이그레이션하려는 경우 VM을 이동하지 않는 [이 스크립트](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0)를 사용합니다.

## <a name="next-steps"></a>다음 단계

[Standard Load Balancer에 대한 자세한 정보](load-balancer-overview.md)
