---
title: 총 Azure Virtual Desktop 배포 비용 이해 - Azure
description: Azure Virtual Desktop 배포의 총 비용을 예측하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 93383fa4b681dd7f0768f5ea30c24e64e9dcb2b9
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123036594"
---
# <a name="understanding-total-azure-virtual-desktop-deployment-costs"></a>총 Azure Virtual Desktop 배포 비용 이해

Azure Virtual Desktop 비용은 기본 Azure 리소스 사용량과 라이선스의 두 가지 출처에서 발생합니다. Azure Virtual Desktop 비용은 배포 리소스에 액세스하는 최종 사용자가 아닌 Azure Virtual Desktop 배포를 소유하는 조직에 청구됩니다. 일부 라이선스 요금은 미리 납부되어야 합니다. 다른 라이선스 및 기본 리소스 사용량 요금은 사용량을 추적하는 미터를 기준으로 합니다.

이 문서에서는 사용량 및 라이선스 비용과 Azure 가격 계산기를 사용하여 Azure Virtual Desktop을 배포하기 전에 서비스 비용을 예측하는 방법을 설명합니다. 이 문서에는 Azure Virtual Desktop을 배포한 후 Azure Cost Management를 사용하여 비용을 확인하는 방법에 대한 지침도 포함됩니다.

>[!NOTE]
>Azure Virtual Desktop 배포에 대해 요금을 납부하는 고객은 해당 배포의 수명 리소스 관리와 비용을 처리해야 합니다. 소유자에게 Azure Virtual Desktop 배포에 연결된 리소스가 더 이상 필요하지 않은 경우 해당 리소스가 제대로 제거되었는지 확인해야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 리소스를 관리하는 방법](../../azure-resource-manager/management/manage-resources-portal.md)을 참조하세요.

## <a name="consumption-costs"></a>사용량 비용

사용량 비용은 Azure Virtual Desktop 호스트 풀에 액세스하는 사용자에 대한 모든 Azure 리소스 사용 요금의 합계입니다. 이 요금은 AD DS(Active Directory Domain Services)용 도메인 컨트롤러와 같이 서비스 가용성을 유지하기 위해 추가 인프라를 실행해야 하는 ID 관리 시스템 및 Azure에서 다른 제품이 공유하는 리소스를 포함한 호스트 풀 내부의 세션 호스트 VM(가상 머신)에서 발생합니다.

### <a name="session-host-vm-costs"></a>세션 호스트 VM 비용

Azure Virtual Desktop에서 세션 호스트 VM은 다음 세 가지 Azure 서비스를 사용합니다.

- 가상 머신(컴퓨팅)
- 관리 디스크의 스토리지(VM당 OS 스토리지 및 개인 데스크톱용 데이터 디스크 포함)
- 대역폭(네트워킹)

이 요금은 세션 호스트 VM을 포함한 호스트 풀 관련 리소스가 할당되는 Azure 리소스 그룹 수준에서 볼 수 있습니다. 또한 하나 이상의 호스트 풀이 유료 Log Analytics 서비스를 사용하여 VM 데이터를 선택적 Azure Virtual Desktop 인사이트 기능으로 보내도록 구성된 경우 청구서는 해당 Azure 리소스 그룹의 Log Analytics에 대한 요금을 부과합니다. 자세한 내용은 [Windows Virtual Desktop 비용 가격 책정 예측 모니터링](../azure-monitor-costs.md)을 참조하세요.

이 섹션의 시작 부분에 나열된 세 가지 기본 VM 세션 호스트 사용 비용 중에서 컴퓨팅 비용이 일반적으로 가장 높습니다. 가용성을 통해 컴퓨팅 비용을 완화하고 리소스 수요를 최적화하기 위해 많은 고객이 [세션 호스트를 자동으로 스케일링](../set-up-scaling-script.md)하도록 선택합니다.

### <a name="domain-controller-costs-for-active-directories"></a>Active Directory에 대한 도메인 컨트롤러 비용

도메인 컨트롤러 VM은 최소한 다음 네 가지 Azure 서비스를 사용합니다.

- 가상 머신(컴퓨팅)
- 관리 디스크의 스토리지(VM당 OS 스토리지 및 개인 데스크톱용 데이터 디스크 포함)
- 대역폭(네트워킹)
- 가상 네트워크

Azure Virtual Desktop 배포가 도메인 컨트롤러를 사용하여 Active Directory를 실행하는 경우 총 Azure Virtual Desktop 배포 비용에 이를 포함해야 합니다. 표준 Azure VM은 Active Directory의 ID를 계속 사용해야 하므로 Azure에서 호스트되는 도메인 컨트롤러는 [세션 호스트 VM 비용](#session-host-vm-costs)에 설명된 세션 호스트 VM에 대해 세 가지 Azure 서비스를 공유합니다.

그러나 도메인 컨트롤러는 세션 호스트 VM에 대해 다음과 같은 몇 가지 주요 차이점이 있습니다.

- 도메인 컨트롤러는 배포 외부의 다른 서비스와 통신해야 하기 때문에 추가 가상 네트워크 요금을 생성합니다.
- 배포에서 도메인 컨트롤러를 항상 사용할 수 있어야 하므로 도메인 컨트롤러 가용성을 스케일링하면 문제가 발생할 수 있습니다.

### <a name="shared-service-costs"></a>공유 서비스 비용

Azure Virtual Desktop 배포에서 사용하는 기능에 따라 다음 선택적 기능을 조합하여 Azure Storage에 대한 요금을 납부해야 할 수도 있습니다.

- [MSIX 앱 연결](../what-is-app-attach.md)
- [사용자 지정 OS 이미지](../set-up-customize-master-image.md)
- [FSLogix 프로필](../fslogix-containers-azure-files.md)

이 기능은 [Azure Files](../../storage/files/storage-files-introduction.md) 및 [Azure NetApp Files](../../azure-netapp-files/azure-netapp-files-introduction.md) 같은 Azure Storage 옵션을 사용하므로 해당 기능은 Azure Virtual Desktop 외에도 다른 Azure 서비스와 스토리지를 공유할 수 있습니다. Azure Virtual Desktop 기능을 위해 구매한 스토리지의 비용과 사용 중인 다른 Azure 서비스의 비용 간 차이를 알려 줄 수 있도록 구매한 스토리지에 대해 별도의 스토리지 계정을 만드는 것이 좋습니다.

### <a name="user-access-costs"></a>사용자 액세스 비용

Azure Virtual Desktop 배포에서 앱이나 데스크톱에 연결하는 각 사용자에 대해 매월 사용자 액세스 비용을 청구합니다. Azure Virtual Desktop의 사용자별 액세스 가격 책정의 작동 방식에 관한 자세한 내용은 [라이선스 및 사용자별 액세스 가격 책정 이해](licensing.md)를 참조하세요.

## <a name="predicting-costs-before-deploying-azure-virtual-desktop"></a>Azure Virtual Desktop을 배포하기 전에 비용 예측

이제 기본 사항을 이해했으므로 예측을 시작하겠습니다. 이 작업을 수행하려면 사용량과 사용자 액세스 비용을 둘 다 예측해야 합니다.

### <a name="predicting-consumption-costs"></a>사용량 비용 예측

[Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 배포를 만들기 전에 Azure Virtual Desktop 사용량 비용을 예측할 수 있습니다. 사용량 비용을 예측하는 방법은 다음과 같습니다.

1. 가격 계산기에서 **컴퓨팅** 탭을 선택하여 Azure 가격 계산기 컴퓨팅 옵션을 표시합니다.

2. **Azure Virtual Desktop** 을 선택합니다. Azure Virtual Desktop 계산기 모듈이 표시됩니다.

3. 필드에 배포에 대한 값을 입력하여 예측된 컴퓨팅, 스토리지 및 네트워킹 사용량을 기준으로 월별 Azure 청구서를 예측합니다.

>[!NOTE]
>현재 Azure 가격 계산기 Azure Virtual Desktop 모듈은 세션 호스트 VM에 대한 사용량 비용만 예측하며 배포하도록 선택한 스토리지가 필요한 선택적 Azure Virtual Desktop 기능의 추가 스토리지를 집계합니다. 그러나 동일한 Azure 가격 계산기 페이지 내 별도의 모듈에 다른 Azure Virtual Desktop 기능의 예측을 추가하여 보다 완전하거나 모듈화된 비용 예측을 얻을 수 있습니다.
>
>추가 Azure 가격 계산기 모듈을 추가하여 다음을 포함하지만 이에 제한되지 않는 배포의 다른 구성 요소와 관련된 비용 영향을 예측할 수 있습니다.
>
>- 도메인 컨트롤러
>- 사용자 지정 OS 이미지, MSIX 앱 연결, Azure Monitor 등의 다른 스토리지 종속 기능

### <a name="predicting-user-access-costs"></a>사용자 액세스 비용 예측

사용자 액세스 비용은 매월 배포에 연결하는 사용자 수에 따라 달라집니다. 예측할 수 있는 총 사용자 액세스 비용을 예측하는 방법을 알아보려면 [Azure Virtual Desktop에 대한 사용자별 앱 스트리밍 비용 예측](streaming-costs.md)을 참조하세요.

## <a name="viewing-costs-after-deploying-azure-virtual-desktop"></a>Azure Virtual Desktop을 배포한 후 비용 보기

Azure Virtual Desktop을 배포한 후 [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md)를 사용하여 청구서를 볼 수 있습니다. 이 섹션에서는 현재 서비스의 가격을 조회하는 방법을 설명합니다.

### <a name="viewing-bills-for-consumption-costs"></a>사용량 비용에 대한 청구서 보기

적절한 [Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md) 권한을 보유한 청구 관리자 같은 조직의 사용자는 [비용 분석 도구](../../cost-management-billing/costs/cost-analysis-common-uses.md)를 사용하고 [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md)를 통해 Azure 청구서를 찾아서 Azure 구독에 따라 월별 Azure Virtual Desktop 사용량 비용을 추적할 수 있습니다.

### <a name="viewing-bills-for-user-access-costs"></a>사용자 액세스 비용에 대한 청구서 보기

사용자 액세스 비용은 사용량 비용, 기타 Azure 요금과 함께 등록된 구독에 대한 Azure 청구서에 청구 주기별로 표시됩니다.

## <a name="next-steps"></a>다음 단계

배포의 특정 부분에 얼마나 많은 비용이 부과되는지 보다 명확하게 이해하려면 다음 문서를 참조하세요.

- [라이선스 및 사용자별 액세스 가격 책정 이해](licensing.md)
- [Azure Virtual Desktop의 사용자별 앱 스트리밍 비용 예상](streaming-costs.md)