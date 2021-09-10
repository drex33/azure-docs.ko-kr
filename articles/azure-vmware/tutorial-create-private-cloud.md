---
title: 자습서 - Azure VMware Solution 프라이빗 클라우드 배포
description: Azure VMware Solution 프라이빗 클라우드를 만들고 배포하는 방법 알아보기
ms.topic: tutorial
ms.date: 06/11/2021
ms.openlocfilehash: d91e9fe9261aa4a04f5e5dffd3505742d9886623
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730354"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>자습서: Azure VMware Solution 프라이빗 클라우드 배포

Azure VMware Solution 프라이빗은 vSphere 클러스터를 Azure에 배포할 수 있는 기능을 제공합니다. 기본적으로 생성된 프라이빗 클라우드마다 vSAN 클러스터가 하나 있습니다. 클러스터를 추가, 삭제 및 스케일링할 수 있습니다.  클러스터당 최소 호스트 수는 3개입니다. 클러스터당 최대 16개까지 호스트를 한 번에 하나씩 추가할 수 있습니다. 프라이빗 클라우드당 최대 클러스터 수는 4개입니다.  Azure VMware Solution의 초기 배포에는 호스트가 3개 있습니다. 

평가판 클러스터는 평가용으로 제공되며 호스트 3개로 제한됩니다. 프라이빗 클라우드당 평가판 클러스터가 하나 있습니다. 평가 기간 중 단일 호스트를 기준으로 평가판 클러스터를 스케일링할 수 있습니다.

vSphere와 NSX-T Manager를 사용하여 클러스터 구성 또는 작업의 다른 대부분의 측면을 관리합니다. 클러스터의 각 호스트에 있는 모든 로컬 스토리지는 vSAN이 제어합니다.

>[!TIP]
>초기 배포 수를 초과해야 하는 경우 언제든지 나중에 클러스터를 확장하고 클러스터를 추가할 수 있습니다.

Azure VMware Solution에서는 시작 시 온-프레미스 vCenter를 사용하여 프라이빗 클라우드를 관리할 수 없으므로 추가 단계를 수행하여 구성해야 합니다.  이 자습서에서는 이러한 단계 및 관련 필수 조건에 대해 설명합니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure VMware Solution 프라이빗 클라우드 만들기
> * 배포된 프라이빗 클라우드 확인

## <a name="prerequisites"></a>사전 요구 사항

- 프라이빗 클라우드를 만들 수 있는 적절한 관리 권한 및 사용 권한 구독에서 최소 기여자 수준이어야 합니다.
- [계획](plan-private-cloud-deployment.md) 자습서에서 수집한 정보에 따라 Azure VMware Solution을 배포합니다.
- [네트워크 계획 검사 목록](tutorial-network-checklist.md)의 설명대로 적절한 네트워킹이 구성되어 있는지 확인합니다.
- 호스트가 프로비저닝되고 Microsoft.AVS [리소스 공급자가 등록되었습니다](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider).

## <a name="create-a-private-cloud"></a>프라이빗 클라우드 만들기

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure VMware Solution 프라이빗 클라우드 만들기
> * 배포된 프라이빗 클라우드 확인
> * Azure VMware Solution 프라이빗 클라우드 삭제

점프 상자를 만드는 방법을 알아보려면 다음 자습서를 계속 진행합니다. 로컬에서 프라이빗 클라우드를 관리할 수 있도록 점프 상자를 사용하여 환경에 연결합니다.


> [!div class="nextstepaction"]
> [Azure VMware Solution 프라이빗 클라우드에 액세스](tutorial-access-private-cloud.md)
