---
title: Azure Migrate 어플라이언스 FAQ
description: Azure Migrate 어플라이언스에 대한 일반적인 질문에 대한 답변입니다.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 11360af784f456559955152772ba099ad4d48d73
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423879"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate 어플라이언스: 일반적인 질문

이 문서에서는 Azure Migrate 어플라이언스에 대한 일반적인 질문에 대한 답변입니다. 다른 질문이 있는 경우 다음 리소스를 확인하세요.

- Azure Migrate에 대한 [일반적인 질문](resources-faq.md)
- [검색, 평가, 종속성 시각화](common-questions-discovery-assessment.md)에 대한 질문
- [서버 마이그레이션](common-questions-server-migration.md)에 대한 질문
- [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에서 질문과 답변 보기

## <a name="what-is-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스란?

Azure Migrate 어플라이언스는 Azure Migrate: 검색 및 평가 도구에서 온-프레미스 또는 클라우드에서 실제 또는 가상 서버를 검색하고 평가하는 데 사용하는 경량 어플라이언스입니다. Azure Migrate: 서버 마이그레이션 도구는 VMware 환경에서 실행되는 온-프레미스 서버의 에이전트 없는 마이그레이션에도 이 어플라이언스를 사용합니다.

Azure Migrate 어플라이언스에 대한 자세한 내용은 다음을 참조하세요.

- 어플라이언스는 온-프레미스에 물리적 서버 또는 가상화된 서버로 배포됩니다.
- 어플라이언스는 온-프레미스 서버를 검색하고 Azure Migrate에 서버 메타데이터 및 성능 데이터를 지속적으로 보냅니다.
- 어플라이언스 검색은 에이전트 없이 수행됩니다. 검색된 서버에는 아무것도 설치되지 않습니다.

어플라이언스에 대해 [자세히 알아봅니다](migrate-appliance.md).

## <a name="how-can-i-deploy-the-appliance"></a>어플라이언스를 배포하려면 어떻게 해야 하나요?

어플라이언스는 다음과 같은 몇 가지 방법을 사용하여 배포할 수 있습니다.

- VMware 또는 Hyper-V 환경([VMware용 OVA 템플릿](how-to-set-up-appliance-vmware.md) 또는 [Hyper-V용 VHD](how-to-set-up-appliance-hyper-v.md))에서 실행되는 서버용 템플릿을 사용하여 이 어플라이언스를 배포할 수 있습니다.
- 템플릿을 사용하지 않으려면 [PowerShell 설치 프로그램 스크립트](deploy-appliance-script.md)를 사용하여 VMware 또는 Hyper-V용 어플라이언스 환경을 배포할 수 있습니다.
- Microsoft Azure Government에서는 PowerShell 설치 프로그램 스크립트를 사용하여 어플라이언스를 배포해야 합니다. [여기](deploy-appliance-script-government.md)에서 배포 단계를 참조하세요.
- 온-프레미스 또는 다른 클라우드의 물리적 또는 가상화된 서버에 대해서는 항상 PowerShell 설치 관리자 스크립트를 사용하여 어플라이언스를 배포합니다. [여기](how-to-set-up-appliance-physical.md)에서 배포 단계를 참조하세요.

## <a name="how-does-the-appliance-connect-to-azure"></a>어플라이언스는 Azure에 어떻게 연결되나요?

어플라이언스는 인터넷을 통해 또는 Azure ExpressRoute를 사용하여 연결할 수 있습니다. 

- 어플라이언스에서 이러한 [Azure URL](./migrate-appliance.md#url-access)에 연결할 수 있는지 확인합니다. 
- Microsoft 피어링에서 ExpressRoute를 사용할 수 있습니다. 퍼블릭 피어링은 사용되지 않으며, 새 ExpressRoute 회로에 사용할 수 없습니다.
- 프라이빗 피어링만 지원되지 않습니다.

## <a name="does-appliance-analysis-affect-performance"></a>어플라이언스 분석이 성능에 영향을 미치나요?

Azure Migrate 어플라이언스는 온-프레미스 서버를 지속적으로 프로파일링하여 성능 데이터를 측정합니다. 이 프로파일링은 프로파일링된 서버의 성능에 거의 영향을 주지 않습니다.

## <a name="can-i-harden-the-appliance"></a>어플라이언스를 강화하려면 어떻게 할 수 있나요?

다운로드 한 템플릿을 사용 하 여 어플라이언스를 만들 때 템플릿에 구성 요소 (예: 바이러스 백신)를 추가할 수 있습니다. Azure 방화벽을 통해 올바른 [url](migrate-appliance.md#public-cloud-urls) 에 대 한 액세스를 허용 했는지 확인 하 고 *%ProgramData%\MicrosoftAzure* 폴더가 바이러스 백신 검사에서 제외 되는지 확인 합니다.

## <a name="what-network-connectivity-is-required"></a>어떤 네트워크 연결이 필요한가요?

어플라이언스는 Azure URL에 액세스할 수 있어야 합니다. URL 목록을 [검토합니다](migrate-appliance.md#url-access).

## <a name="what-data-does-the-appliance-collect"></a>어플라이언스는 어떤 데이터를 수집하나요?

Azure Migrate 어플라이언스가 서버에서 수집하는 데이터에 대한 자세한 내용은 다음 문서를 참조하세요.

- **VMware 환경의 서버**: 수집된 데이터를 [검토합니다](migrate-appliance.md#collected-data---vmware).
- **Hyper-V 환경의 서버**: 수집된 데이터를 [검토합니다](migrate-appliance.md#collected-data---hyper-v).
- **실제 또는 가상 서버**: 수집된 데이터를 [검토합니다](migrate-appliance.md#collected-data---physical).

## <a name="how-is-data-stored"></a>데이터가 어떻게 저장되나요?

Azure Migrate 어플라이언스에서 수집한 데이터는 프로젝트를 만든 Azure 위치에 저장됩니다.

데이터를 저장하는 방법에 대한 자세한 내용은 다음과 같습니다.

- 수집된 데이터는 Microsoft 구독에서 CosmosDB에 안전하게 저장됩니다. 프로젝트를 삭제하면 데이터가 삭제됩니다. 스토리지는 Azure Migrate에서 처리됩니다. 수집된 데이터에 대한 스토리지 계정을 선택할 수 없습니다.
- [종속성 시각화](concepts-dependency-visualization.md)를 사용하는 경우 수집된 데이터는 Azure 구독에서 만든 Azure Log Analytics 작업 영역에 저장됩니다. 구독에서 Log Analytics 작업 영역을 삭제하면 이 데이터가 삭제됩니다.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>연속 프로파일링 중에 업로드되는 데이터의 양은 얼마나 되나요?

Azure Migrate로 전송되는 데이터의 볼륨은 여러 매개 변수에 따라 달라집니다. 예를 들어, 10개의 서버(각각 하나의 디스크와 하나의 NIC 포함)가 있는 프로젝트는 매일 약 50MB의 데이터를 전송합니다. 이 값은 근사치입니다. 실제 값은 디스크 및 NIC의 데이터 요소 수에 따라 달라집니다. 서버, 디스크 또는 NIC 수가 증가하는 경우 전송되는 데이터의 증가는 비선형입니다.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>미사용 데이터 및 전송 중 데이터는 암호화되나요?

다음과 같이 두 가지 데이터 모두 암호화됩니다.

- 메타데이터는 HTTPS를 통해 인터넷으로 Azure Migrate 서비스에 안전하게 전송됩니다.
- 메타데이터는 [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) 데이터베이스 및 Microsoft 구독의 [Azure Blob Storage](../storage/common/storage-service-encryption.md)에 저장됩니다. 메타데이터는 미사용일 때 저장을 위해 암호화됩니다.
- 또한 종속성 분석을 위한 데이터는 전송 중에 보안 HTTPS를 통해 암호화됩니다. 구독의 Log Analytics 작업 영역에 저장됩니다. 데이터는 미사용일 때 종속성 분석을 위해 암호화됩니다.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>어플라이언스는 vCenter Server에 어떻게 연결되나요?

이러한 단계는 어플라이언스를 VMware vCenter Server에 연결하는 방식을 설명합니다.

1. 어플라이언스는 어플라이언스를 설정할 때 제공한 자격 증명을 사용하여 vCenter Server(포트 443)에 연결됩니다.
2. 어플라이언스는 VMware PowerCLI를 통해 vCenter Server를 쿼리하여 vCenter Server에서 관리하는 서버에 대한 메타데이터를 수집합니다.
3. 어플라이언스는 서버에 대한 구성 데이터(코어, 메모리, 디스크, NIC)와 지난 달의 각 서버에 대한 성능 기록을 수집합니다.
4. 수집된 메타데이터는 평가를 위해 Azure Migrate: 검색 및 평가 도구(HTTPS를 통해 인터넷에서)에 전송됩니다.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure Migrate 어플라이언스를 여러 vCenter 서버에 연결할 수 있나요?

아니요. [Azure Migrate 어플라이언스](migrate-appliance.md)와 vCenter Server 사이에 일대일 매핑이 있습니다. 여러 vCenter Server 인스턴스에서 서버를 검색하려면 여러 어플라이언스를 배포해야 합니다.

## <a name="can-a-project-have-multiple-appliances"></a>하나의 프로젝트에 여러 개의 어플라이언스가 있을 수 있나요?

하나의 프로젝트에 여러 개의 어플라이언스를 등록할 수 있습니다. 그러나 한 어플라이언스는 한 프로젝트에만 등록할 수 있습니다.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure Migrate 어플라이언스/복제 어플라이언스는 동일한 vCenter에 연결할 수 있나요?

예. Azure Migrate 어플라이언스(평가 및 에이전트 없는 VMware 마이그레이션에 사용) 및 복제 어플라이언스(VMware에서 실행되는 서버에 대한 에이전트 기반 마이그레이션에 사용)를 모두 동일한 vCenter 서버에 추가할 수 있습니다. 하지만 동일한 서버에 두 어플라이언스를 모두 설정하지는 않아야 합니다. 이러한 방식은 현재 지원되지 않습니다.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>한 어플라이언스로 검색할 수 있는 서버는 몇 대인가요?

VMware 환경에서 최대 1만 대의 서버, Hyper-V 환경에서 최대 5,000대의 서버, 단일 어플라이언스에서 최대 1,000대의 물리적 서버를 검색할 수 있습니다. 온-프레미스 환경에 추가 서버가 있는 경우 [ 평가 스케일링](scale-hyper-v-assessment.md), [VMware 평가 스케일링](scale-vmware-assessment.md) 및 [물리적 서버 평가 스케일링](scale-physical-assessment.md)에 대해 읽어보세요.

## <a name="can-i-delete-an-appliance"></a>어플라이언스를 삭제할 수 있나요?

현재 프로젝트에서 어플라이언스를 삭제하는 것은 지원되지 않습니다.

어플라이언스를 삭제하는 유일한 방법은 어플라이언스와 연결된 프로젝트를 포함하는 리소스 그룹을 삭제하는 것입니다.

그러나 리소스 그룹을 삭제하면 등록된 다른 어플라이언스, 검색된 인벤토리, 평가 및 해당 프로젝트와 연결된 리소스 그룹의 모든 기타 Azure 구성 요소도 삭제됩니다.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>다른 구독 또는 프로젝트에서 어플라이언스를 사용할 수 있나요?

다른 구독 또는 프로젝트에서 어플라이언스를 사용하려면 어플라이언스에서 특정 시나리오(VMware/Hyper-V/물리적)에 대해 PowerShell 설치 관리자 스크립트를 실행하여 기존 어플라이언스를 다시 구성해야 합니다. 이 스크립트는 기존 어플라이언스 구성 요소 및 설정을 정리하여 새 어플라이언스를 배포합니다. 새로 배포된 어플라이언스 구성 관리자를 사용하기 전에 브라우저 캐시를 지워야 합니다.

또한 다시 구성된 어플라이언스에서 기존 프로젝트 키를 다시 사용할 수 없습니다. 원하는 구독/프로젝트에서 새 키를 생성하여 어플라이언스 등록을 완료해야 합니다.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Azure VM에서 어플라이언스를 설정할 수 있나요?

아니요. 현재 이 옵션은 지원되지 않습니다.

## <a name="can-i-discover-on-an-esxi-host"></a>ESXi 호스트에서 검색할 수 있나요?

아니요. VMware 환경에서 서버를 검색하려면 vCenter Server가 있어야 합니다.

## <a name="how-do-i-update-the-appliance"></a>어플라이언스를 업데이트하려면 어떻게 하나요?

기본적으로 어플라이언스와 설치된 에이전트가 자동으로 업데이트됩니다. 어플라이언스는 24시간마다 업데이트를 확인합니다. 실패한 업데이트를 다시 시도합니다.

이러한 자동 업데이트는 어플라이언스 및 어플라이언스 에이전트만 업데이트합니다. 운영 체제는 Azure Migrate 자동 업데이트를 통해 업데이트되지 않습니다. Windows 업데이트를 사용하여 운영 체제를 최신 상태로 유지합니다.

## <a name="can-i-check-agent-health"></a>에이전트 상태를 확인할 수 있나요?

예. 포털에서 Azure Migrate: 검색 및 평가 또는 Azure Migrate: 서버 마이그레이션 도구의 **에이전트 상태** 페이지로 이동합니다. 이 페이지에서 어플라이언스의 Azure와 검색 및 평가 에이전트 간의 연결 상태를 확인할 수 있습니다.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>VMware 어플라이언스에서 여러 서버 자격 증명을 추가할 수 있나요?

예, 이제 소프트웨어 인벤토리(설치된 애플리케이션 검색), 에이전트 없는 종속성 분석 및 SQL Server 인스턴스/데이터베이스의 검색을 수행하기 위해 여러 서버 자격 증명 검색을 지원합니다. 어플라이언스 구성 관리자에서 자격 증명을 제공하는 방법에 대해 [자세히 알아봅니다](tutorial-discover-vmware.md#provide-server-credentials).

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>VMware 어플라이언스에서 어떤 유형의 서버 자격 증명을 추가할 수 있나요?

어플라이언스 구성 관리자에서 도메인/Windows(비도메인)/Linux(비도메인)/SQL Server 인증 자격 증명을 제공할 수 있습니다. 자격 증명을 제공하는 방법과 처리하는 방법에 대해 [자세히 알아보세요](add-server-credentials.md).

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>SQL 검색을 위해 Azure Migrate에서 어떤 유형의 SQL Server 연결 속성 유형이 지원되나요?

Azure Migrate는 Azure Migrate 어플라이언스와 원본 SQL Server 인스턴스 간의 통신을 암호화합니다(암호화 연결 속성이 TRUE로 설정됨). 이러한 연결은 [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate)로 암호화됩니다(TRUE로 설정됨). 전송 계층에서는 SSL을 사용하여 채널을 암호화하고 인증서 체인을 무시하여 신뢰의 유효성을 확인합니다. 어플라이언스 서버는 [인증서의 루트 인증 기관을 신뢰](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)하도록 설정되어야 합니다.

서버에 인증서가 구성되어 있지 않으면 시작할 때 SQL Server에서 로그인 패킷 암호화에 사용되는 자체 서명된 인증서를 생성합니다. [자세히 알아보기](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="next-steps"></a>다음 단계

[Azure Migrate 개요](migrate-services-overview.md)를 참조하세요.
