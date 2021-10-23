---
title: Azure에서 아웃바운드 SMTP 연결 문제 해결 | Microsoft Docs
description: 이메일을 보내는 권장 방법 및 Azure의 아웃바운드 SMTP 연결 문제를 해결하는 방법에 대해 알아봅니다.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: genli
ms.openlocfilehash: 5c4c129514ccf2fe3b55fc173d3fd4b87fd50715
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248966"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Azure에서 아웃바운드 SMTP 연결 문제 해결

VM(가상 머신)에서 TCP 포트 25의 외부 도메인(예: outlook.com 및 gmail.com)으로 직접 전송되는 아웃바운드 이메일 메시지는 특정 구독 유형으로 VM을 배포하는 경우에만 가능합니다.

> [!IMPORTANT]
> 아래 모든 예제에서 프로세스는 Virtual Machines & VM Scale Sets 리소스 ()에 *만* 적용 됩니다 `Microsoft.Compute/virtualMachines`  &  `Microsoft.Compute/virtualMachineScaleSets` . 포트 25에서 전자 메일을 보내는 것은 [Azure App Service](https://azure.microsoft.com/services/app-service) 및 [Azure Functions](https://azure.microsoft.com/services/functions)를 비롯 한 모든 Azure PaaS (Platform as a Service) 리소스에서 지원 되지 않습니다.

## <a name="recommended-method-of-sending-email"></a>이메일을 전송하는 권장 방법

인증된 SMTP 릴레이 서비스를 사용하여 Azure VM 또는 Azure App Service에서 이메일을 보내는 것이 좋습니다. 이러한 릴레이 서비스는 일반적으로 TCP 포트 587을 통해 연결되지만 다른 포트도 지원합니다. 이러한 서비스는 외부 도메인에서 메시지를 거부하거나 스팸 폴더에 넣을 가능성을 최소화하기 위해 IP 및 도메인 평판을 유지하는 데 사용됩니다. [SendGrid](https://sendgrid.com/partners/azure/)는 이러한 SMTP 릴레이 서비스 중 하나이지만 다른 서비스도 있습니다. 온-프레미스 서버에서 인증된 SMTP 릴레이 서비스를 사용할 수도 있습니다.

이러한 이메일 배달 서비스를 사용하는 작업은 구독 형식에 상관없이 Azure에서 제한되지 않습니다.

## <a name="enterprise-agreement"></a>기업 계약

기업계약 구독에 배포된 VM의 경우 TCP 포트 25의 아웃바운드 SMTP 연결이 차단되지 않습니다. 그러나 외부 도메인은 VM에서 들어오는 메일을 수락한다는 보장이 없습니다. 외부 도메인에서 이메일을 거부하거나 필터링하는 경우 외부 도메인의 이메일 서비스 공급자에게 문의하여 문제를 해결해야 합니다. 이러한 문제는 Azure 지원에서 다루지 않습니다.

## <a name="pay-as-you-go"></a>종량제

Azure 플랫폼은 종량제 구독에 배포된 VM에 대해 TCP 포트 25의 아웃바운드 SMTP 연결을 차단합니다. Azure 구독이 적절한 시기에 있고 충분한 지불 기록이 있는 경우 이 차단을 제거할 수 있습니다. [Azure Portal](https://portal.azure.com)에서 Azure Virtual Network 리소스에 대한 **진단 및 해결** 블레이드의 **이메일을 보낼 수 없음(SMTP-포트 25)** 섹션으로 이동하여 제한을 제거하도록 요청할 수 있습니다. 

이 차단에서 종량제 구독이 제외되고 Azure Portal에서 VM이 중지되었다가 다시 시작되면 해당 구독의 모든 VM은 전달에서 제외됩니다. 예외는 요청된 구독에만 적용되고 인터넷으로 직접 라우팅되는 VM 트래픽에만 적용됩니다.

> [!NOTE]
> Microsoft에서 서비스 약관이 위반되었음을 판단하게 되면 이러한 예외를 취소할 수 있는 권한을 가집니다.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, 학생용 Azure, Visual Studio 및 무료 평가판

Azure 플랫폼은 다음 구독 유형으로 배포된 VM에 대해 TCP 포트 25의 아웃바운드 SMTP 연결을 차단합니다.

- MSDN
- Azure Pass
- Azure in Open
- Education
- Azure for Students
- 무료 평가판
- 모든 Visual Studio Subscription  

제한 사항은 남용을 방지하기 위해 적용됩니다. 이러한 제한을 제거하기 위한 요청은 허용되지 않습니다.

이러한 구독 형식을 사용하는 경우 이 문서의 앞부분에 설명된 대로 인증된 SMTP 릴레이 서비스를 사용하거나 구독 유형을 변경하는 것이 좋습니다.

## <a name="cloud-solution-provider"></a>클라우드 솔루션 공급자

Azure 플랫폼은 클라우드 솔루션 공급자 구독에 배포된 VM에 대해 TCP 포트 25의 아웃바운드 SMTP 연결을 차단합니다. 이 차단을 제거하는 것이 가능합니다. 차단을 제거하도록 요청하려면 Azure Portal의 Azure Virtual Network 리소스에서 **진단 및 해결** 블레이드의 **이메일을 보낼 수 없음(SMTP-포트 25)** 섹션으로 이동하여 지원 요청을 엽니다.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft 파트너 네트워크, BizSpark Plus 및 Azure 스폰서쉽

Azure 플랫폼은 다음 구독에서 배포된 VM에 대한 TCP 포트 25의 아웃바운드 SMTP 배달 시도를 차단합니다.

- MPN(Microsoft 파트너 네트워크)
- BizSpark Plus
- Azure 스폰서쉽

이 차단을 제거하는 것이 가능합니다. 차단을 제거하도록 요청하려면 Azure Portal의 Azure Virtual Network 리소스에서 **진단 및 해결** 블레이드의 **이메일을 보낼 수 없음(SMTP-포트 25)** 섹션으로 이동하여 지원 요청을 엽니다.

이 차단에서 구독을 제외하고 VM이 중지되었다가 다시 시작되면 해당 구독의 모든 VM은 제외됩니다. 예외는 요청된 구독에만 적용되고 인터넷으로 직접 라우팅되는 VM 트래픽에만 적용됩니다.

> [!NOTE]
> Microsoft에서 서비스 약관이 위반되었음을 판단하게 되면 이러한 예외를 취소할 수 있는 권한을 가집니다.

## <a name="changing-subscription-type"></a>구독 유형 변경

구독 유형을 기업계약에서 다른 구독 유형으로 변경하면 배포를 변경하여 아웃바운드 SMTP가 차단될 수 있습니다. 구독 유형을 기업계약에서 다른 구독 유형으로 변경하고 TCP 포트 25에서 아웃바운드 SMTP를 요구하려는 경우 구독 유형을 변경하기 전에 구독을 차단 해제하기 위해 지원팀과 협력해야 합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요. 이 문제 유형 사용: **기술** > **가상 네트워크** > **이메일을 보낼 수 없음(SMTP-포트 25)** .
