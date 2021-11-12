---
title: Azure 관리형 애플리케이션의 Fortanix 기밀 컴퓨팅 관리자
description: Azure Portal의 관리형 애플리케이션에 Fortanix CCM(기밀 컴퓨팅 관리자)을 배포하는 방법에 대해 알아봅니다.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: fa811a3f528fb0d1bf20e053910540a6204eb724
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315917"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Azure 관리형 애플리케이션의 Fortanix 기밀 컴퓨팅 관리자

이 문서에서는 Azure Portal에서 Fortanix 기밀 컴퓨팅 관리자가 관리하는 애플리케이션을 배포하는 방법을 보여 줍니다.

Fortanix는 Azure 인프라를 기반으로 구축된 제품 및 서비스를 제공하는 타사 소프트웨어 공급업체입니다. Azure에서 유사한 기밀 컴퓨팅 서비스를 제공하는 다른 타사 공급자도 있습니다.

> [!NOTE]
>Microsoft는 이 문서에서 언급되는 제품을 관리하지 않습니다. Microsoft는 사용자의 편의를 위해서만 해당 정보를 제공하며, Microsoft 이외의 제품을 참조하는 것은 Microsoft가 해당 제품을 보증한다는 것을 의미하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 변환된 애플리케이션 이미지를 푸시하는 프라이빗 Docker 레지스트리
- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Azure 관리형 애플리케이션을 통해 기밀 컴퓨팅 관리자 배포

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/Azure-portal.png" alt-text="Azure Portal.":::

2. 검색 창에서 “Fortanix 기밀 컴퓨팅 관리자”를 검색하면 Fortanix CCM에 대한 Marketplace 목록을 확인할 수 있습니다. **Azure에서 Fortanix 기밀 컴퓨팅 관리자** 를 선택합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Marketplace 목록.":::

3. CCM 관리형 애플리케이션을 만드는 페이지가 열립니다. **만들기** 를 선택합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="애플리케이션 만들기.":::

4. 모든 필수 필드를 입력합니다.
   1. 관리형 애플리케이션 세부 정보 섹션의 **관리되는 리소스 그룹** 필드에는 사용자가 필요한 경우 수정할 수 있는 기본값이 입력되어 있습니다.
   2. **지역** 필드에서 **오스트레일리아 동부**, **오스트레일리아 남동부**, **미국 동부**, **미국 서부 2**, **서유럽**, **북유럽**, **캐나다 중부**, **캐나다 동부** 또는 **미국 동부 2 EUAP** 중 하나를 선택합니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="필수 필드":::

   **검토 + 만들기** 를 선택하여 Fortanix CCM 관리형 애플리케이션을 만듭니다.

5. 세부 정보를 검토하고 유효성 검사를 통과하면 **위 사용 약관에 동의합니다** 확인란을 선택하고 **만들기** 를 선택하여 관리형 애플리케이션을 만듭니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="세부 정보 검토.":::

6. Fortanix CCM 배포가 시작되며 배포가 진행되고 있음을 알립니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="배포 진행률.":::

7. 배포가 완료되면 **리소스로 이동** 단추를 선택하여 배포된 CCM 관리형 애플리케이션의 “개요” 페이지로 이동하고 컴퓨팅 노드를 등록합니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Azure Portal에서 성공적으로 배포가 이루어졌음을 보여 주는 스크린샷.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Azure Portal의 기밀 컴퓨팅 리소스에 대한 개요를 보여 주는 스크린샷.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Fortanix CCM에서 컴퓨팅 노드 등록

1. 왼쪽 탐색 메뉴에서 **기밀 컴퓨팅 관리자** 를 선택합니다. **그림 9** 에 표시된 대로 Fortanix CCM에 로그인하고 계정을 만듭니다.

    가입, 로그인, CCM에서 계정을 만드는 방법에 대한 자세한 내용은 [CCM 시작](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in)을 참조하세요.
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Fortanix 기밀 컴퓨팅 관리자 로그인을 보여 주는 스크린샷.":::
    
2. CCM 관리 콘솔에서 연결 토큰을 가져오려면 먼저 **노드 등록** 단추를 선택합니다. 그런 다음 노드 등록 창에서 **복사** 단추를 선택하여 연결 토큰을 복사합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="연결 토큰을 가져오는 과정을 보여 주는 스크린샷.":::

3. 이제 노드 에이전트를 등록하려면 **기밀 컴퓨팅 노드 에이전트** 탭을 선택하고 **추가** 를 선택하여 CCM 노드 에이전트를 추가합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="노드 에이전트 추가를 보여 주는 스크린샷.":::

4.  CCM 노드 에이전트 양식에서 모든 필수 필드를 채웁니다. **연결 토큰** 에 2단계에서 복사한 연결 토큰을 붙여넣습니다. **검토 + 제출** 을 선택하여 확인합니다.

    CCM 컴퓨팅 노드를 등록하는 방법에 대한 자세한 내용은 [컴퓨팅 노드 등록](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes)을 참조하세요.
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="컴퓨팅 노드를 등록을 보여 주는 스크린샷.":::
    
5. 유효성 검사를 통과한 다음 **제출** 을 선택하여 노드 에이전트 만들기를 완료합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="노드 에이전트가 생성된 것을 보여 주는 스크린샷.":::

6. 배포 상태를 확인하려면 **개요** 탭으로 이동하고 **관리되는 리소스 그룹** 링크를 선택합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="노드가 등록되어 있음을 보여 주는 스크린샷.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="배포 상태 확인을 보여 주는 스크린샷.":::

7. 이제 배포 상태가 아직 진행 중이며 노드 에이전트가 성공적으로 등록되려면 몇 분 정도 걸릴 것이라는 사실을 알 수 있습니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="진행 중인 배포를 보여 주는 스크린샷.":::

8. 노드 에이전트가 성공적으로 등록되면 상태가 “성공”으로 변경됩니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="배포가 성공했음을 보여 주는 스크린샷.":::

9. 이제 CCM 관리형 애플리케이션에서 컴퓨팅 노드 페이지로 이동하면 노드가 **활성** 상태이며 성공적으로 등록된 것을 확인할 수 있습니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="성공적으로 등록된 노드를 보여 주는 스크린샷.":::

## <a name="clean-up-resources"></a>리소스 정리

사용자는 기밀 컴퓨팅 노드 에이전트 페이지에서 CCM 노드 에이전트를 삭제할 수도 있습니다. 노드 에이전트를 삭제하려면 노드 에이전트를 선택하고 위쪽 표시줄에서 **삭제** 단추를 선택합니다.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="노드 에이전트 삭제를 보여 주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

이 빠른 시작에 안내된 Azure 관리형 앱을 사용하여 Fortanix의 기밀 컴퓨팅 관리자에 노드를 등록했습니다. 노드를 등록했으므로 기밀 컴퓨팅 가상 머신을 기반으로 하여 애플리케이션 이미지 변환을 실행할 수 있습니다. Azure의 기밀 컴퓨팅 가상 머신에 대한 자세한 내용은 [Virtual Machines 솔루션](virtual-machine-solutions-sgx.md)을 참조하세요.

Azure의 기밀 컴퓨팅 제품에 대해 자세히 알아보려면 [azure 기밀 컴퓨팅](overview.md)을 참조하세요.

Azure에서 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 및 [scone](https://sconedocs.github.io)와 같은 기타 타사 제품을 사용하여 유사한 작업을 완료하는 방법을 알아보세요.
