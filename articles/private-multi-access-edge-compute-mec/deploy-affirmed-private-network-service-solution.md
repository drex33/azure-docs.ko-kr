---
title: Azure의 확정된 개인 네트워크 서비스 배포
description: Azure에 Affirmed Private Network Service 솔루션을 배포하는 방법 알아보기
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: d3c027a7d4006a947f520dee8406e40a30f1b304
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458455"
---
# <a name="deploy-affirmed-private-network-service-on-azure"></a>Azure의 확정된 개인 네트워크 서비스 배포

이 문서에서는 Microsoft Azure Marketplace를 통해 Azure Stack Edge 디바이스에 APNS(Affirmed Private Network Service) 솔루션을 배포하는 프로세스에 대한 개략적인 개요를 제공합니다.

다음 다이어그램은 배포에 필요한 리소스를 포함하여 Affirmed Private Network Service의 시스템 아키텍처를 보여 줍니다.

![Affirmed Private Network Service 배포](media/deploy-affirmed-private-network-service/deploy-affirmed-private-network-service.png)

## <a name="collect-required-information"></a>필요한 정보 수집

APNS를 배포하려면 다음 리소스가 있어야 합니다.

- 구성된 Azure Network Function Manager - Azure Stack Edge 디바이스의 디지털 쌍 역할을 하는 디바이스 개체입니다. 

- NetFoundry VM을 통해 완전히 배포된 Azure Stack Edge 

- Affirmed Management Systems VM 제품 및 APNS 관리형 애플리케이션에 대한 구독 승인 

- 활성 구독 및 다음에 대한 액세스 권한이 있는 Azure 계정:  

    - 리소스 그룹에 대한 기본 제공 **소유자** 역할 

    - 구독에 대한 기본 제공 **관리형 애플리케이션 기여자** 역할 

    - 조인할 가상 네트워크 및 서브넷(포트 tcp/443 및 tcp/8443 열기) 

    - 가상 서브넷의 IP 주소 5개 

    - Affirmed Release Engineering에서 제공하는 유효한 SAS 토큰  

    - 배포하는 동안 프로그래밍할 관리 사용자 이름/암호 
    
## <a name="deploy-apns"></a>APNS 배포

필요한 모든 리소스 및 관련 정보를 사용하여 APNS 관리형 애플리케이션을 자동으로 배포하려면 Microsoft Azure Marketplace에서 APNS 관리형 애플리케이션을 선택합니다. APNS를 배포하면 필요한 모든 리소스가 자동으로 만들어지고 관리형 리소스 그룹에 포함됩니다.

APNS를 배포하려면 다음 절차를 완료합니다.
1.  Azure Portal에 로그인하고 **리소스 만들기** 를 선택합니다.
2.  검색 표시줄에 *APNS* 를 입력하고 Enter 키를 누릅니다.
3.  **프라이빗 제품 보기** 를 선택합니다. 
    > [!NOTE]
    > APNS 관리형 애플리케이션은 **프라이빗 제품 보기** 를 선택할 때까지 표시되지 않습니다.
4.  **프라이빗 제품** 의 드롭다운 메뉴에서 **만들기** 를 선택한 다음, 배포 옵션을 선택합니다.
5.  애플리케이션 설정, 네트워크 설정을 완료하고 검토한 후 만듭니다.
6.  **배포** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- Azure Stack Edge 디바이스에서 APNS를 배포하고 NetFoundry 설정을 구성하기 위한 단계별 지침은 [Affirmed Private Network Service 배포 가이드](https://go.microsoft.com/fwlink/?linkid=2165732)를 참조하세요.
- 운영자가 프라이빗 모바일 코어 네트워크를 배포, 모니터링 및 관리하는 데 사용한 프로그래밍 방식의 GUI 기반 포털에 대한 자세한 내용은 [Affirmed Private Network Service Manager 사용자 가이드](https://go.microsoft.com/fwlink/?linkid=2165932)를 참조하세요.
- [Azure의 Affirmed Private Network Service](affirmed-private-network-service-overview.md)에 대해 자세히 알아봅니다.
