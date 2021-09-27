---
title: Azure Virtual Desktop 문제 해결 개요 - Azure
description: Azure Virtual Desktop 환경을 설정하는 동안 발생하는 문제 해결에 대한 개요입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c847c87a24dedc994da39a37f37def3caeef4db1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818621"
---
# <a name="troubleshooting-overview-feedback-and-support-for-azure-virtual-desktop"></a>Azure Virtual Desktop용 문제 해결 개요, 피드백 및 지원

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)를 참조하세요.

이 문서에서는 Azure Virtual Desktop 환경을 설정할 때 발생할 수 있는 문제에 대한 개요와 문제 해결 방법을 제공합니다.

## <a name="report-issues"></a>문제 보고

Azure Resource Manager 통합으로 Azure Virtual Desktop에 대한 문제를 보고하거나 기능을 제안하려면 [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum)를 방문하세요. 기술 커뮤니티를 활용하여 모범 사례를 논의하거나 새로운 기능을 제안하고 투표할 수 있습니다.

도움을 요청하거나 새로운 기능을 제안하는 게시물을 작성하는 경우 최대한 많은 세부 정보로 설명해야 합니다. 정보가 자세하면 다른 사용자가 질문에 답변하거나 투표를 제안하는 기능을 이해하는 데 도움이 될 수 있습니다.

## <a name="escalation-tracks"></a>에스컬레이션 트랙

다른 무언가를 하기 전에 [Azure 상태 페이지](https://status.azure.com/status) 및 [Azure Service Health](https://azure.microsoft.com/features/service-health/)를 사용하여 Azure 서비스가 올바르게 실행 중인지 확인하세요.

다음 표를 사용하여 원격 데스크톱 클라이언트 사용을 통해 환경을 설정할 때 발생할 수 있는 문제를 식별 및 해결할 수 있습니다. 환경을 설정한 후에는 새로운 [진단 서비스](diagnostics-role-service.md)를 사용하여 일반적인 시나리오에 대한 문제를 식별할 수 있습니다.

| **문제점**                                                            | **추천 솔루션**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 세션 호스트 풀 Azure Virtual Network(VNET) 및 Express 경로 설정               | [Azure 지원 요청](https://azure.microsoft.com/support/create-ticket/)을 열고, (네트워킹 범주에서) 적절한 서비스를 선택합니다. |
| Azure Virtual Desktop에 제공된 Azure Resource Manager 템플릿을 사용하지 않는 경우 세션 호스트 풀 VM(가상 머신) 만들기 | [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop** 을 선택합니다. <br> <br> Azure Virtual Desktop에 제공되는 Azure Resource Manager 템플릿 관련 문제는 [호스트 풀 만들기](troubleshoot-set-up-issues.md)의 Azure Resource Manager 템플릿 오류 섹션을 참조하세요. |
| Azure Portal에서 Azure Virtual Desktop 세션 호스트 환경 관리    | [Azure 지원 요청을 엽니다](https://azure.microsoft.com/support/create-ticket/). <br> <br> 원격 데스크톱 서비스/Azure Virtual Desktop PowerShell을 사용할 때의 관리 문제는 [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요. 또는 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/) 서비스에서 **Azure Virtual Desktop**, 문제 유형에서 **구성 및 관리** 를 선택한 다음, 문제 하위 유형에서 **PowerShell을 사용한 환경 구성 문제** 를 선택하세요. |
| 호스트 풀 및 애플리케이션 그룹에 연결된 Azure Virtual Desktop 구성 관리(앱 그룹)      | [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하거나 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop** 을 선택한 다음, 적절한 문제 유형을 선택합니다.|
| FSLogix 프로필 컨테이너 배포 및 관리 | [FSLogix 제품 문제 해결 가이드](/fslogix/fslogix-trouble-shooting-ht/)를 참조하고 문제가 해결되지 않는 경우, [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop**, 문제 유형에서 **FSLogix** 를 선택한 다음, 적절한 문제 하위 유형을 선택합니다. |
| 시작 시 원격 데스크톱 클라이언트 오작동                                                 | [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md)을 참조하고 문제가 해결되지 않는 경우, [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop** 를 선택한 다음, 문제 유형에서 **원격 데스크톱 클라이언트** 를 선택합니다.  <br> <br> 네트워크 문제인 경우 사용자는 네트워크 관리자에게 문의해야 합니다. |
| 연결되었지만 피드 없음                                                                 | [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection.md)의 [사용자가 연결했지만 아무 것도 표시되지 않음(피드 없음)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) 섹션을 사용하여 문제를 해결합니다. <br> <br> 사용자가 앱 그룹에 할당된 경우 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop** 을 선택한 다음, 문제 유형에서 **원격 데스크톱 클라이언트** 를 선택합니다. |
| 네트워크로 인한 피드 검색 문제                                            | 사용자가 네트워크 관리자에게 문의해야 합니다. |
| 클라이언트 연결                                                                    | [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection.md)을 참조하고 문제가 해결되지 않는 경우 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조합니다. |
| 원격 애플리케이션 또는 데스크톱의 응답성                                      | 문제가 특정 애플리케이션 또는 제품과 관련된 경우 해당 제품을 담당하는 팀에 문의하세요. |
| 라이선스 메시지 또는 오류                                                          | 문제가 특정 애플리케이션 또는 제품과 관련된 경우 해당 제품을 담당하는 팀에 문의하세요. |
| 타사 인증 방법 또는 도구 관련 문제 | 타사 공급자가 Azure Virtual Desktop 시나리오를 지원하는지 확인하고 알려진 문제와 관련된 방법을 확인합니다. |
| Azure Virtual Desktop용 Log Analytics 사용 문제 | 진단 스키마 관련 문제의 경우 [Azure 지원 요청을 여세요](https://azure.microsoft.com/support/create-ticket/).<br><br>Log Analytics의 쿼리, 시각화 또는 기타 문제의 경우 Log Analytics에서 적절한 문제 유형을 선택합니다. |
| Microsoft 365 앱 사용 문제 | [Microsoft 365 관리 센터 도움말 옵션](/microsoft-365/admin/contact-support-for-business-products/) 중 하나를 사용하여 Microsoft 365 관리 센터에 문의합니다. |

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop 환경에서 호스트 풀을 만드는 동안 발생하는 문제를 해결하려면 [호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Azure Virtual Desktop 에이전트 또는 세션 연결과 관련된 문제를 해결하려면 [일반적인 Azure Virtual Desktop 에이전트 문제 해결](troubleshoot-agent.md)을 참조하세요.
- Azure Virtual Desktop 클라이언트 연결 문제를 해결하려면 [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection.md)을 참조하세요.
- 원격 데스크톱 클라이언트와 관련된 문제를 해결하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 서비스에 대한 자세한 내용은 [Azure Virtual Desktop 환경](environment-setup.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-monitor/essentials/activity-log.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
