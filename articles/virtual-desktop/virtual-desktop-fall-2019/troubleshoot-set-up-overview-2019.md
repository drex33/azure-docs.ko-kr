---
title: Azure Virtual Desktop(클래식) 문제 해결 개요 - Azure
description: Azure Virtual Desktop(클래식) 테넌트 환경을 설정하는 동안의 문제 해결에 대한 개요입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5c55dac88810531af849cbacb129197eaddf1a08
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774598"
---
#  <a name="azure-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Azure Virtual Desktop(클래식) 문제 해결 개요, 피드백 및 지원

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../troubleshoot-set-up-overview.md)를 참조하세요.

이 문서에서는 Azure Virtual Desktop 테넌트 환경을 설정할 때 발생할 수 있는 문제에 대한 개요를 제공하고 문제 해결 방법을 제공합니다.

## <a name="provide-feedback"></a>피드백 제공

제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Azure Virtual Desktop 서비스에 대해 토론하려면 [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하세요.

## <a name="escalation-tracks"></a>에스컬레이션 트랙

다음 표를 사용하여 원격 데스크톱 클라이언트 사용을 통해 테넌트 환경을 설정할 때 발생할 수 있는 문제를 식별 및 해결할 수 있습니다. 테넌트를 설정한 후에는 새로운 [진단 서비스](diagnostics-role-service-2019.md)를 사용하여 일반적인 시나리오에 대한 문제를 식별할 수 있습니다.

>[!NOTE]
> 제품 팀 및 활성 커뮤니티 구성원과 이슈를 논의하기 위해 방문할 수 있는 기술 커뮤니티 포럼을 제공하고 있습니다. [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 토론을 시작해보세요.

| **문제점**                                                            | **추천 솔루션**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Azure Virtual Desktop 테넌트 만들기                                                    | Azure 중단이 발생하면 [Azure 지원 요청을 엽니다.](https://azure.microsoft.com/support/create-ticket/) 그렇지 않으면 [Azure 지원 요청을 열고,](https://azure.microsoft.com/support/create-ticket/) 서비스로 **Azure Virtual Desktop** 을 선택하고, 문제 유형으로 **배포** 를 선택한 다음, 문제 하위 유형으로 **Azure Virtual Desktop 테넌트 만들기 문제** 를 선택합니다.|
| Azure Portal에서 Marketplace 템플릿에 액세스       | Azure 가동 중단이 발생하면 [Azure 지원 요청을 엽니다.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Azure Marketplace Azure Virtual Desktop 템플릿은 무료로 사용할 수 있습니다.|
| GitHub에서 Azure Resource Manager 템플릿에 액세스                                  | [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues-2019.md) 섹션의 [Azure Virtual Desktop 세션 호스트 VM 만들기](troubleshoot-set-up-issues-2019.md#creating-azure-virtual-desktop-session-host-vms) 섹션을 참조하세요. 그래도 문제가 해결되지 않으면 [GitHub 지원 팀](https://github.com/contact)에 문의하세요. <br> <br> GitHub에서 템플릿에 액세스한 후 오류가 발생하면 [Azure 지원](https://azure.microsoft.com/support/create-ticket/)에 문의하세요.|
| 세션 호스트 풀 Azure Virtual Network(VNET) 및 Express 경로 설정               | [Azure 지원 요청](https://azure.microsoft.com/support/create-ticket/)을 열고, (네트워킹 범주에서) 적절한 서비스를 선택합니다. |
| Azure Virtual Desktop에 제공된 Azure Resource Manager 템플릿을 사용하지 않는 경우 세션 호스트 풀 VM(가상 머신) 만들기 | [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/) 서비스로 **Windows를 실행하는 Virtual Machine** 을 선택합니다. <br> <br> Azure Virtual Desktop과 함께 제공되는 Azure Resource Manager 템플릿에 대한 이슈는 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues-2019.md)의 Azure Virtual Desktop 테넌트 만들기 섹션을 참조하세요. |
| Azure Portal에서 Azure Virtual Desktop 세션 호스트 환경 관리    | [Azure 지원 요청을 엽니다](https://azure.microsoft.com/support/create-ticket/). <br> <br> 원격 데스크톱 서비스/Azure Virtual Desktop PowerShell을 사용할 때의 관리 문제는 [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)을 참조하거나 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop**, 문제 유형에서 **구성 및 관리** 를 선택한 다음, 문제 하위 유형에서 **PowerShell을 사용한 테넌트 구성 문제** 를 선택합니다. |
| 호스트 풀 및 애플리케이션 그룹에 연결된 Azure Virtual Desktop 구성 관리(앱 그룹)      | [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)을 참조하거나 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop** 을 선택한 다음, 적절한 문제 유형을 선택합니다.|
| FSLogix 프로필 컨테이너 배포 및 관리 | [FSLogix 제품 문제 해결 가이드](/fslogix/fslogix-trouble-shooting-ht/)를 참조하고 문제가 해결되지 않는 경우, [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop**, 문제 유형에서 **FSLogix** 를 선택한 다음, 적절한 문제 하위 유형을 선택합니다. |
| 시작 시 원격 데스크톱 클라이언트 오작동                                                 | [원격 데스크톱 클라이언트 문제 해결](../troubleshoot-client.md)을 참조하고 문제가 해결되지 않는 경우, [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop** 를 선택한 다음, 문제 유형에서 **원격 데스크톱 클라이언트** 를 선택합니다.  <br> <br> 네트워크 문제인 경우 사용자는 네트워크 관리자에게 문의해야 합니다. |
| 연결되었지만 피드 없음                                                                 | [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection-2019.md)의 [사용자가 연결했지만 아무 것도 표시되지 않음(피드 없음)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) 섹션을 사용하여 문제를 해결합니다. <br> <br> 사용자가 앱 그룹에 할당된 경우 [Azure 지원 요청을 열고](https://azure.microsoft.com/support/create-ticket/), 서비스에서 **Azure Virtual Desktop** 을 선택한 다음, 문제 유형에서 **원격 데스크톱 클라이언트** 를 선택합니다. |
| 네트워크로 인한 피드 검색 문제                                            | 사용자가 네트워크 관리자에게 문의해야 합니다. |
| 클라이언트 연결                                                                    | [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection-2019.md)을 참조하고 문제가 해결되지 않는 경우 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration-2019.md)을 참조합니다. |
| 원격 애플리케이션 또는 데스크톱의 응답성                                      | 문제가 특정 애플리케이션 또는 제품과 관련된 경우 해당 제품을 담당하는 팀에 문의하세요. |
| 라이선스 메시지 또는 오류                                                          | 문제가 특정 애플리케이션 또는 제품과 관련된 경우 해당 제품을 담당하는 팀에 문의하세요. |
| GitHub에서 Azure Virtual Desktop 도구를 사용할 때의 문제(Azure Resource Manager 템플릿, 진단 도구, 관리 도구) | 문제를 보고하려면 [원격 데스크톱 서비스용 Azure Resource Manager 템플릿](https://github.com/Azure/RDS-Templates/blob/master/README.md)을 참조하세요. |

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop 환경에서 테넌트와 호스트 풀을 만드는 동안 발생하는 문제를 해결하려면 [테넌트 및 호스트 풀 만들기](troubleshoot-set-up-issues-2019.md)를 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration-2019.md)을 참조하세요.
- Azure Virtual Desktop 클라이언트 연결 문제를 해결하려면 [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection-2019.md)을 참조하세요.
- 원격 데스크톱 클라이언트와 관련된 문제를 해결하려면 [원격 데스크톱 클라이언트 문제 해결](../troubleshoot-client.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)을 참조하세요.
- 서비스에 대한 자세한 내용은 [Azure Virtual Desktop 환경](environment-setup-2019.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../../azure-monitor/essentials/activity-log.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
