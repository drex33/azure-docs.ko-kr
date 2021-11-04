---
title: Azure에서 Windows 클라이언트 이미지 사용
description: Visual Studio 구독 혜택을 사용하여 개발/테스트 시나리오용으로 Azure에서 Windows 7, Windows 8 또는 Windows 10을 배포하는 방법
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: fdb0459b796704666fedfd013571f952b8382c8f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466735"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>개발/테스트 시나리오용으로 Azure에서 Windows 클라이언트 사용

**적용 대상:** :heavy_check_mark: Windows VM 

적절한 Visual Studio(이전의 MSDN) 구독이 있으면 Azure에서 개발/테스트 시나리오에 Windows 7, Windows 8 또는 Windows 10 Enterprise(x64)를 사용할 수 있습니다. 

프로덕션 환경에서 Windows 10을 실행하려면 [Azure에서 다중 테넌트 호스팅 권한으로 Windows 10을 배포하는 방법](windows-desktop-multitenant-hosting-deployment.md)을 참조하세요.


## <a name="subscription-eligibility"></a>구독 적격성
활성 Visual Studio 구독자, 즉 Visual Studio 구독 라이선스를 받은 사용자는 개발 및 테스트용으로 Windows 클라이언트 이미지를 사용할 수 있습니다. Windows 클라이언트 이미지는 사용자 고유의 하드웨어 또는 Azure 가상 머신에서 사용할 수 있습니다.

특정 Windows 클라이언트 이미지는 Azure Marketplace에서 사용할 수 있습니다. Visual Studio 구독자(사용 중인 제품 유형은 관계없음)는 64비트 Windows 7, Windows 8 또는 Windows 10 이미지를 [준비하고 작성](prepare-for-upload-vhd-image.md)한 다음, [Azure에 업로드](upload-generalized-managed.md)할 수도 있습니다.

## <a name="eligible-offers-and-client-images"></a>적합한 제품 및 클라이언트 이미지
아래 테이블에는 Azure Marketplace를 통해 Windows 클라이언트 이미지를 배포할 수 있는 제품 ID가 자세히 나와 있습니다. Windows 클라이언트 이미지는 다음 제품에 대해서만 표시됩니다. 

> [!NOTE]
> 이미지 제품은 Azure Marketplace의 **Windows 클라이언트** 아래에 있습니다. Visual Studio 구독자가 사용할 수 있는 클라이언트 이미지를 검색할 때 **Windows 클라이언트** 를 사용합니다. Visual Stuido 구독을 구입 해야 하는 경우 구입 시 다양 한 옵션을 참조 하세요 [Visual Studio](https://visualstudio.microsoft.com/vs/pricing/?tab=business)

| 제품 이름 | 제품 번호 | 사용 가능한 클라이언트 이미지 | 
|:--- |:---:|:---:|
| [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N(x64) <br> Windows 8.1 Enterprise N(x64) <br> Windows 7 Enterprise N with SP1(x64) |
| [Visual Studio Enterprise(MPN) 구독자](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N(x64) <br> Windows 8.1 Enterprise N(x64) <br> Windows 7 Enterprise N with SP1(x64) |
| [Visual Studio Professional 구독자](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N(x64) <br> Windows 8.1 Enterprise N(x64) <br> Windows 7 Enterprise N with SP1(x64) |
| [Visual Studio Test Professional 구독자](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N(x64) <br> Windows 8.1 Enterprise N(x64) <br> Windows 7 Enterprise N with SP1(x64) |
| [Visual Studio Enterprise 구독자](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N(x64) <br> Windows 8.1 Enterprise N(x64) <br> Windows 7 Enterprise N with SP1(x64) |
| [Visual Studio Enterprise(BizSpark) 구독자](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N(x64) <br> Windows 8.1 Enterprise N(x64) <br> Windows 7 Enterprise N with SP1(x64) |
| [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N(x64) <br> Windows 8.1 Enterprise N(x64) <br> Windows 7 Enterprise N with SP1(x64) |

자세한 내용은 [Microsoft 제품 유형 이해](../../cost-management-billing/costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers) 를 참조 하세요.

## <a name="check-your-azure-subscription"></a>Azure 구독 확인
제품 ID를 모르는 경우 Azure Portal을 통해 확인할 수 있습니다.  
- *구독* 창: ![Azure Portal의 제품 ID 세부 정보](./media/client-images/offer-id-azure-portal.png) 
- 또는 **청구** 를 클릭하고 구독 ID를 클릭합니다. 제품 ID가 *청구* 창에 표시됩니다. 
- Azure 계정 포털의 ['구독' 탭](https://account.windowsazure.com/Subscriptions)에서 제품 ID를 볼 수도 있습니다. ![Azure 계정 포털의 제품 ID 세부 정보](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>다음 단계
이제 [PowerShell](quick-create-powershell.md), [Resource Manager 템플릿](ps-template.md) 또는 [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)를 사용하여 VM을 배포할 수 있습니다.
