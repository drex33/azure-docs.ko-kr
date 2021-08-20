---
title: Azure Virtual Desktop 원격 앱 스트리밍이란 무엇인가요? - Azure
description: Azure Virtual Desktop 원격 앱 스트리밍의 개요
author: Heidilohr
ms.topic: overview
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 88bc4c94612a69be8a9d7aad7bbad983b687235a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799181"
---
# <a name="what-is-azure-virtual-desktop-remote-app-streaming"></a>Azure Virtual Desktop 원격 앱 스트리밍이란 무엇인가요?

Azure Virtual Desktop은 클라우드에서 실행되는 데스크톱 및 앱 가상화 서비스로, 언제 어디서나 원격 데스크톱에 액세스할 수 있도록 지원합니다. 그러나 Azure Virtual Desktop을 PaaS(Platform as a Service)로 사용하여 고객에게 조직의 앱을 SaaS(Software as a Service)로 제공할 수 있다는 사실을 알고 계셨나요? Azure Virtual Desktop 원격 앱 스트리밍을 사용하면 이제 Azure Virtual Desktop을 사용하여 보안 네트워크를 통해 가상 머신으로 고객에게 앱을 제공할 수 있습니다.

Azure Virtual Desktop에 익숙하지 않는 경우(일반적으로 앱 가상화를 처음 사용하는 경우)에 배포를 실행하는 데 도움이 될 수 있는 몇 가지 리소스를 참조할 수 있습니다.

## <a name="requirements"></a>요구 사항

시작하기 전에 Azure Virtual Desktop을 실행하기 위한 시스템 요구 사항에 대한 자세한 내용을 보려면 [Azure Virtual Desktop 개요](../overview.md)를 확인하는 것이 좋습니다. 대부분의 문서가 Azure Virtual Desktop에 대한 원격 앱 스트리밍에도 적용되므로 IT 부서 관점에서 서비스를 좀 더 살펴보기 위해 Azure Virtual Desktop 설명서의 나머지 부분도 참조할 수 있습니다. 기본 사항을 이해하면 원격 앱 스트리밍 설명서를 보다 효과적으로 사용할 수 있습니다.

조직 외부 고객이 사용할 수 있는 사용자 지정 앱에 대한 Azure Virtual Desktop 배포를 설정하기 위해서는 다음 항목이 필요합니다.

- 사용자 지정 앱. Azure Virtual Desktop이 지원하는 앱 유형 및 고객에게 이러한 앱을 제공하는 방법에 대한 자세한 내용을 보려면 [Azure Virtual Desktop을 사용하여 사용자 지정 앱을 호스트하는 방법](custom-apps.md)을 참조하세요.

- 도메인 조인 자격 증명 Azure Virtual Desktop과 호환되는 ID 관리 시스템이 아직 없는 경우 호스트 풀에 대한 ID 관리를 설정해야 합니다.

- Azure 구독 아직 구독이 없는 경우 [계정을 만들어야](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 합니다.

## <a name="get-started"></a>시작

이제 준비가 완료되었으므로 Azure Virtual Desktop 배포를 설정하는 방법을 살펴보겠습니다.

다음 자습서에 따라 배포를 수동으로 설정할 수 있습니다.

1. [Azure Portal로 호스트 풀 만들기](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

2. [앱 액세스 관리](../manage-app-groups.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

3. [서비스 업데이트의 유효성을 검사하기 위한 호스트 풀 만들기](../create-validation-host-pool.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

4. [서비스 경고 설정](../set-up-service-alerts.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)

## <a name="customize-and-manage-azure-virtual-desktop"></a>Azure Virtual Desktop 사용자 지정 및 관리

Azure Virtual Desktop을 설정하고 나면 조직 또는 고객의 요구에 맞게 배포를 사용자 지정할 수 있는 다양한 옵션이 제공됩니다. 다음 문서는 시작하는 데 도움이 됩니다.

- [Azure Virtual Desktop을 사용하여 사용자 지정 앱을 호스트하는 방법](custom-apps.md)
- [사용자별 액세스 가격으로 구독 등록](per-user-access-pricing.md)
- [Azure Active Directory를 사용하는 방법](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [Intune에서 Windows 10 가상 머신 사용](/mem/intune/fundamentals/windows-10-virtual-machines)
- [MSIX 앱 연결을 사용하여 앱을 배포하는 방법](msix-app-attach.md)
- [Azure Monitor for Azure Virtual Desktop을 사용하여 배포 모니터링](../azure-monitor.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [비즈니스 연속성 및 재해 복구 계획 설정](../disaster-recovery.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Automation을 사용하여 세션 호스트 크기 조정](../set-up-scaling-script.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [유니버설 인쇄 설정](/universal-print/fundamentals/universal-print-getting-started)

## <a name="get-to-know-your-azure-virtual-desktop-deployment"></a>Azure Virtual Desktop 배포 알아보기

Azure Virtual Desktop 배포를 만들고 관리하는 데 필요한 개념을 이해하려면 다음 문서를 참조하세요.

- [라이선스 및 사용자별 액세스 가격 책정 이해](licensing.md)
- [조직 간 앱에 대한 보안 지침](security.md)
- [Windows Virtual Desktop 보안 모범 사례](../security-guide.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [Azure Virtual Desktop용 Azure Monitor 용어집](../azure-monitor-glossary.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
- [기업용 Azure Virtual Desktop](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
- [총 배포 비용 예상](total-costs.md)
- [아키텍처 권장 사항](architecture-recs.md)

## <a name="next-steps"></a>다음 단계

배포를 수동으로 설정할 준비가 되면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Azure Portal로 호스트 풀 만들기](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)
