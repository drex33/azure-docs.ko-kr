---
title: Azure Migrate 어플라이언스 아키텍처
description: 서버 검색, 평가 및 마이그레이션에 사용되는 Azure Migrate 어플라이언스에 대해 간략히 설명합니다.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 78b62c91148fc7a2c202cbf6792c1de442c142cc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567006"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate 어플라이언스 아키텍처

이 문서에서는 Azure Migrate 어플라이언스 아키텍처와 프로세스에 대해 설명합니다. Azure Migrate 어플라이언스는 온-프레미스에 배포되는 경량 어플라이언스로, Azure로 마이그레이션할 서버를 검색합니다.

## <a name="deployment-scenarios"></a>배포 시나리오

Azure Migrate 어플라이언스가 사용되는 시나리오는 다음과 같습니다.

**시나리오** | **도구** | **용도**
--- | --- | ---
**VMware 환경에서 실행되는 서버 검색 및 평가** | Azure Migrate: 검색 및 평가 | VMware 환경 내에서 실행되는 서버 검색<br/><br/> 설치된 소프트웨어 인벤토리 검색, ASP.NET 웹앱, SQL Server 인스턴스 및 데이터베이스, 에이전트 없는 종속성 분석을 수행합니다.<br/><br/> 평가를 위해 서버 구성 및 성능 메타데이터를 수집합니다.
**VMware 환경에서 실행되는 에이전트 없는 서버 마이그레이션** | Azure Migrate: 서버 마이그레이션 | VMware 환경 내에서 실행되는 서버를 검색합니다.<br/><br/> 서버에 에이전트를 설치하지 않고 서버를 복제합니다.
**Hyper-V 환경에서 실행되는 서버 검색 및 평가** | Azure Migrate: 검색 및 평가 | Hyper-V 환경 내에서 실행되는 서버를 검색합니다.<br/><br/> 평가를 위해 서버 구성 및 성능 메타데이터를 수집합니다.
**온-프레미스에서 물리적 또는 가상화된 서버 검색 및 평가** |  Azure Migrate: 검색 및 평가 |  온-프레미스에서 물리적 또는 가상화된 서버를 검색합니다.<br/><br/> 평가를 위해 서버 구성 및 성능 메타데이터를 수집합니다.

## <a name="deployment-methods"></a>배포 방법

어플라이언스는 다음과 같은 몇 가지 방법을 사용하여 배포할 수 있습니다.

- VMware 또는 Hyper-V 환경([VMware용 OVA 템플릿](how-to-set-up-appliance-vmware.md) 또는 [Hyper-V용 VHD](how-to-set-up-appliance-hyper-v.md))에서 실행되는 서버용 템플릿을 사용하여 이 어플라이언스를 배포할 수 있습니다.
- 템플릿을 사용하지 않으려면 [PowerShell 설치 프로그램 스크립트](deploy-appliance-script.md)를 사용하여 VMware 또는 Hyper-V용 어플라이언스 환경을 배포할 수 있습니다.
- Microsoft Azure Government에서는 PowerShell 설치 프로그램 스크립트를 사용하여 어플라이언스를 배포해야 합니다. [여기](deploy-appliance-script-government.md)에서 배포 단계를 참조하세요.
- 온-프레미스 또는 다른 클라우드의 물리적 또는 가상화된 서버에 대해서는 항상 PowerShell 설치 프로그램 스크립트를 사용하여 어플라이언스를 배포합니다. [여기](how-to-set-up-appliance-physical.md)에서 배포 단계를 참조하세요.
- 다운로드 링크는 아래 표에 나와 있습니다.

## <a name="appliance-services"></a>어플라이언스 서비스

어플라이언스에는 다음 서비스가 있습니다.

- **어플라이언스 구성 관리자**: 서버에 대한 검색 및 평가를 시작하기 위해 원본 세부 정보를 사용하여 구성할 수 있는 웹 애플리케이션입니다.
- **검색 에이전트**: 에이전트는 온-프레미스 평가로 만드는 데 사용할 수 있는 서버 구성 메타데이터를 수집합니다.
- **평가 에이전트**: 에이전트는 성능 기반 평가를 만드는 데 사용할 수 있는 서버 성능 메타데이터를 수집합니다.
- **자동 업데이트 서비스**: 이 서비스는 어플라이언스에서 실행되는 모든 에이전트를 최신 상태로 유지합니다. 자동으로 24시간마다 한 번씩 실행됩니다.
- **DRA 에이전트**: VM 복제를 오케스트레이션하고, 복제된 서버와 Azure 간의 통신을 조정합니다. 에이전트 없는 마이그레이션을 사용하여 서버를 Azure에 복제할 때만 사용됩니다.
- **게이트웨이**: 복제된 데이터를 Azure에 보냅니다. 에이전트 없는 마이그레이션을 사용하여 서버를 Azure에 복제할 때만 사용됩니다.
- **SQL 검색 및 평가 에이전트**: SQL Server 인스턴스 및 데이터베이스의 구성 및 성능 메타데이터를 Azure에 보냅니다.
- **웹앱 검색 및 평가 에이전트**: 웹앱 구성 데이터를 Azure에 보냅니다.

> [!Note]
> 마지막 네 가지 서비스는 VMware 환경에서 실행 중인 서버를 검색하고 평가하는 데 사용되는 어플라이언스에서만 사용할 수 있습니다.

## <a name="discovery-and-collection-process"></a>검색 및 수집 프로세스

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="어플라이언스 구조":::

어플라이언스는 다음 프로세스를 사용하여 검색 원본과 통신합니다.

**처리** | **VMware 어플라이언스** | **Hyper-V 어플라이언스** | **물리적 어플라이언스**
---|---|---|---
**검색 시작** | 어플라이언스는 기본적으로 TCP 포트 443에서 vCenter Server와 통신합니다. vCenter Server가 다른 포트에서 수신 대기하는 경우 어플라이언스 구성 관리자에서 구성할 수 있습니다. | 어플라이언스는 WinRM 포트 5985(HTTP)에서 Hyper-V 호스트와 통신합니다. | 어플라이언스는 WinRM 포트 5985(HTTP)를 통해 Windows 서버와 통신하고 포트 22(TCP)를 통해 Linux 서버와 통신합니다.
**구성 및 성능 메타데이터 수집** | 어플라이언스는 포트 443(기본 포트) 또는 vCenter Server에서 수신 대기하는 다른 포트에서 연결하여 vCenter Server에서 실행되는 서버의 메타데이터를 vSphere API를 사용하여 수집합니다. | 어플라이언스는 포트 5985의 호스트에서 CIM(Common Information Model) 세션을 사용하여 Hyper-V 호스트에서 실행되는 서버의 메타데이터를 수집합니다.| 어플라이언스는 포트 5985의 서버에서 CIM(Common Information Model) 세션을 사용하여 Windows 서버에서 메타데이터를 수집하고, 포트 22에서 SSH 연결을 사용하여 Linux 서버에서 메타데이터를 수집합니다.
**검색 데이터 보내기** | 어플라이언스는 수집된 데이터를 SSL를 포트 443을 통해 Azure Migrate: 검색 및 평가, Azure Migrate: 서버 마이그레이션으로 보냅니다.<br/><br/>  어플라이언스는 인터넷, ExpressRoute 개인 피어링 또는 Microsoft 피어링 서킷을 통해 Azure에 연결할 수 있습니다. | 어플라이언스는 수집된 데이터를 SSL 포트 443을 통해 Azure Migrate: 검색 및 평가로 보냅니다.<br/><br/> 어플라이언스는 인터넷, ExpressRoute 개인 피어링 또는 Microsoft 피어링 서킷을 통해 Azure에 연결할 수 있습니다. | 어플라이언스는 수집된 데이터를 SSL 포트 443을 통해 Azure Migrate: 검색 및 평가로 보냅니다.<br/><br/> 어플라이언스는 인터넷, ExpressRoute 개인 피어링 또는 Microsoft 피어링 서킷을 통해 Azure에 연결할 수 있습니다. 
**데이터 수집 빈도** | 구성 메타데이터는 30분마다 수집되고 전송됩니다. <br/><br/> 성능 메타데이터는 20초마다 수집되고 10분마다 집계되어 Azure로 데이터 요소가 전송됩니다. <br/><br/> 소프트웨어 인벤토리 데이터는 12시간마다 한 번씩 Azure에 전송됩니다. <br/><br/> 에이전트 없는 종속성 데이터는 5분마다 수집되고, 어플라이언스에 집계되고, 6시간마다 Azure로 전송됩니다. <br/><br/> SQL Server 구성 데이터는 24시간마다 업데이트되고, 성능 데이터는 30초마다 캡처됩니다. <br/><br/> 웹앱 구성 데이터는 24시간마다 한 번 업데이트됩니다. 웹앱에 대한 성능 데이터는 캡처되지 않습니다.| 구성 메타데이터는 30분마다 수집되고 전송됩니다. <br/><br/> 성능 메타데이터는 30초마다 수집되고 10분마다 집계되어 Azure로 데이터 요소가 전송됩니다.|  구성 메타데이터는 30분마다 수집되고 전송됩니다. <br/><br/> 성능 메타데이터는 5분마다 수집되고 10분마다 집계되어 Azure로 데이터 요소가 전송됩니다.
**평가 및 마이그레이션** | Azure Migrate: 검색 및 평가 도구를 사용하여 어플라이언스에서 수집한 메타데이터에서 평가를 만들 수 있습니다.<br/><br/>또한 Azure Migrate: 서버 마이그레이션 도구를 통해 VMware 환경에서 실행 중인 서버 마이그레이션을 시작함으로써 에이전트 없는 서버 복제를 오케스트레이션할 수 있습니다.| Azure Migrate: 검색 및 평가 도구를 사용하여 어플라이언스에서 수집한 메타데이터에서 평가를 만들 수 있습니다. | Azure Migrate: 검색 및 평가 도구를 사용하여 어플라이언스에서 수집한 메타데이터에서 평가를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

어플라이언스 지원 매트릭스를 [검토](migrate-appliance.md)합니다.