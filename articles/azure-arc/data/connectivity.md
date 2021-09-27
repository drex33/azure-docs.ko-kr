---
title: 연결 모드 및 요구 사항
description: 사용자 환경에서 Azure로의 Azure Arc 지원 데이터 서비스 연결 옵션을 설명합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: fa34a8e5e801080f354e13b632917a1a05eabf43
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832644"
---
# <a name="connectivity-modes-and-requirements"></a>연결 모드 및 요구 사항


## <a name="connectivity-modes"></a>연결 모드

Azure Arc 지원 데이터 서비스 환경에서 Azure로의 연결 수준에 대한 여러 옵션이 있습니다. 요구 사항은 비즈니스 정책, 정부 규제 또는 Azure에 대한 네트워크 연결 가능 여부에 따라 달라지므로 다음 연결 모드 중에서 선택할 수 있습니다.

Azure Arc 지원 데이터 서비스를 사용하면 두 가지 ‘연결 모드’로 Azure에 연결할 수 있습니다. 

- 직접 연결 
- 간접 연결

연결 모드를 통해 Azure로 전송되는 데이터의 양과 사용자가 Arc 데이터 컨트롤러와 상호 작용하는 방법을 유연하게 선택할 수 있습니다. 선택한 연결 모드에 따라 Azure Arc 지원 데이터 서비스의 일부 기능을 사용할 수 있거나 사용하지 못할 수 있습니다.

Azure Arc 지원 데이터 서비스를 Azure에 직접 연결하면 사용자가 [Azure Resource Manager API](/rest/api/resources/), Azure CLI, Azure Portal을 사용하여 Azure Arc 데이터 서비스를 운영할 수 있다는 점이 중요합니다. 직접 연결 모드의 환경은 Azure Portal에서 프로비저닝/프로비저닝 해제, 스케일링, 구성 등의 다른 Azure 서비스를 사용하는 방식과 매우 비슷합니다.  Azure Arc 지원 데이터 서비스를 Azure에 간접적으로 연결하는 경우 Azure Portal은 읽기 전용 뷰가 됩니다. 배포한 SQL 관리형 인스턴스와 Postgres 하이퍼스케일 인스턴스의 인벤토리와 해당 인스턴스에 대한 세부 정보를 볼 수 있지만, Azure Portal에서는 이에 대한 작업을 수행할 수 없습니다.  간접 연결 모드에서는 Azure Data Studio, 적절한 CLI 또는 Kubernetes 네이티브 도구(예: kubectl)를 사용하여 로컬에서 모든 작업을 수행해야 합니다.

또한 Azure Active Directory와 Azure 역할 기반 액세스 제어의 경우 이 기능을 제공하려면 Azure에 대한 지속적인 직접 연결에 의존해야 하므로 직접 연결 모드에서만 사용할 수 있습니다.

일부 Azure 연결 서비스는 Blob Storage에 대한 Azure Defender 보안 서비스, Container Insights, Azure Backup과 같이 직접 연결할 수 있는 경우에만 사용할 수 있습니다.

||**간접 연결**|**직접 연결**|**연결 안 함**|
|---|---|---|---|
|**설명**|간접 연결 모드는 Azure에 직접 연결하지 않고 대부분의 관리 서비스를 로컬로 사용자 환경에 제공합니다.  ‘오직’ 인벤토리 및 청구 목적으로만 최소한의 데이터를 Azure로 보내야 합니다. 데이터는 매월 한 번 이상 파일로 내보내지고 Azure에 업로드됩니다.  Azure에 대한 직접 연결 또는 지속적인 연결은 필요하지 않습니다.  Azure에 연결해야 하는 일부 기능과 서비스는 사용할 수 없습니다.|직접 연결 모드는 Azure에 직접 연결을 설정할 수 있는 경우 사용 가능한 모든 서비스를 제공합니다. 연결은 항상 ‘사용자 환경에서’ 시작하여 Azure에 이르며, HTTPS/443과 같은 표준 포트와 프로토콜을 사용합니다.|어떤 방식으로든 Azure와 주고받을 수 있는 데이터는 없습니다.|
|**현재 가용성**| 사용 가능 |미리 보기로 제공됩니다.|현재 지원되지 않습니다.|
|**일반적인 사용 사례**|비즈니스/규정 준수 정책 또는 외부 공격/데이터 반출 문제로 인해 데이터 센터의 데이터 영역 내부 또는 외부 연결을 허용하지 않는 온-프레미스 데이터 센터.  일반적인 예: 금융 기관, 의료, 정부. <br/><br/>에지 사이트가 일반적으로 인터넷에 연결되지 않는 에지 사이트 위치.  일반적인 예: 석유/가스 또는 군용 애플리케이션.  <br/><br/>장기간 사용하지 않으며 일시적인 연결이 있는 에지 사이트 위치.  일반적인 예: 경기장, 유람선. | 퍼블릭 클라우드를 사용하는 조직.  일반적인 예: Azure, AWS 또는 Google Cloud.<br/><br/>인터넷 연결이 일반적으로 제공되고 허용되는 에지 사이트 위치.  일반적인 예: 소매점, 제조.<br/><br/>데이터 센터의 데이터 영역과 인터넷 간의 연결에 대한 정책이 보다 관대한 회사 데이터 센터.  일반적인 예: 규제가 적용되지 않는 비즈니스, 중소 규모 기업|어떤 환경에서든 데이터 환경에 데이터가 오갈 수 없는 엄격한 “에어 갭”이 있는 환경. 일반적인 예: 극비의 정부 시설.|
|**Azure에 데이터를 전송하는 방법**|청구 및 인벤토리 데이터를 Azure로 전송하는 방법에는 세 가지 옵션이 있습니다.<br><br> 1) 데이터를 보안 데이터 영역과 Azure 모두에 연결된 자동 프로세스를 통해 데이터 영역 외부로 내보냅니다.<br><br>2) 데이터를 데이터 영역 내에서 자동 프로세스를 통해 데이터 영역 외부로 내보낸 후 보안 수준이 낮은 지역에 자동으로 복사하고, 보안 수준이 낮은 지역에서 자동 프로세스를 통해 해당 데이터를 Azure에 업로드합니다.<br><br>3) 데이터를 보안 지역 내에서 사용자가 수동으로 내보내고, 보안 지역 외부에 수동으로 가져와서 Azure에 수동으로 업로드합니다. <br><br>처음 두 옵션은 자동으로 실행되도록 예약할 수 있는 자동화된 연속 프로세스로, Azure에서 사용 가능한 연결에 대해서만 Azure에 데이터를 전송하는 데 최소한의 지연이 있습니다.|데이터가 자동으로 계속해서 Azure에 전송됩니다.|데이터가 Azure로 전송되지 않습니다.|

## <a name="feature-availability-by-connectivity-mode"></a>연결 모드별 기능 사용 가능 여부

|**기능**|**간접 연결**|**직접 연결**|
|---|---|---|
|**자동 고가용성**|지원됨|지원됨|
|**셀프서비스 프로비저닝**|지원됨<br/>만들기는 Azure Data Studio, 적절한 CLI 또는 Kubernetes 네이티브 도구(helm, kubectl, oc 등)를 사용하거나 Azure Arc 지원 Kubernetes GitOps 프로비저닝을 사용하여 수행할 수 있습니다.|지원됨<br/>간접 연결 모드 만들기 옵션 외에 Azure Portal, Azure Resource Manager API, Azure CLI 또는 ARM 템플릿을 통해 만들 수도 있습니다. **직접 연결 모드 지원 보류 중**
|**탄력적 확장성**|지원됨|지원됨<br/>**직접 연결 모드 지원 보류 중**|
|**Billing**|지원됨<br/>청구 데이터는 주기적으로 내보내지고 Azure로 전송됩니다.|지원됨<br/>청구 데이터는 자동으로 계속해서 Azure로 전송되며 근 실시간으로 반영됩니다. **직접 연결 모드 지원 보류 중**|
|**인벤토리 관리**|지원됨<br/>인벤토리 데이터는 주기적으로 내보내지고 Azure로 전송됩니다.<br/><br/>Azure Data Studio, Azure Data CLI 또는 `kubectl`과 같은 클라이언트 도구를 사용하여 인벤토리를 로컬로 보고 관리합니다.|지원됨<br/>인벤토리 데이터는 자동으로 계속해서 Azure로 전송되며 근 실시간으로 반영됩니다. 따라서 Azure Portal에서 직접 인벤토리를 관리할 수 있습니다.|
|**자동 업그레이드 및 패치**|지원됨<br/>데이터 컨트롤러는 MCR(Microsoft Container Registry)에 직접 액세스할 수 있어야 합니다. 아니면 MCR에서 컨테이너 이미지를 가져와서 데이터 컨트롤러가 액세스할 수 있는 로컬 프라이빗 컨테이너 레지스트리로 푸시해야 합니다.|지원됨<br/>**직접 연결 모드 지원 보류 중**|
|**자동 백업 및 복원**|지원됨<br/>자동 로컬 백업 및 복원.|지원됨<br/>자동 로컬 백업 및 복원 외에도 ‘필요에 따라’ 장기 오프사이트 보존을 위해 Azure Backup으로 백업을 보낼 수 있습니다. **직접 연결 모드 지원 보류 중**|
|**Monitoring**|지원됨<br/>Grafana 및 Kibana 대시보드를 사용한 로컬 모니터링.|지원됨<br/>로컬 모니터링 대시보드 외에도 ‘필요에 따라’ 모니터링 데이터 및 로그를 Azure Monitor로 보내 한 곳에서 여러 사이트를 대규모로 모니터링할 수 있습니다. **직접 연결 모드 지원 보류 중**|
|**인증**|데이터 컨트롤러 및 대시보드 인증에 로컬 사용자 이름/암호를 사용합니다. 데이터베이스 인스턴스에 대한 연결을 위해 SQL 및 Postgres 로그인 또는 Active Directory(AD는 현재 지원되지 않으며 곧 미리 보기로 제공될 예정)를 사용합니다.  Kubernetes API에 대한 인증에 K8s 인증 공급자를 사용합니다.|간접 연결 모드의 인증 방법 외에도 ‘필요에 따라’ Azure Active Directory를 사용할 수 있습니다. **직접 연결 모드 지원 보류 중**|
|**RBAC(역할 기반 액세스 제어)**|Kubernetes API에서 Kubernetes RBAC를 사용합니다. 데이터베이스 인스턴스에 SQL 및 Postgres RBAC를 사용합니다.|Azure Active Directory 및 Azure RBAC를 사용할 수 있습니다.|
|**Azure Defender**|지원되지 않음|추후 지원 예정|

## <a name="connectivity-requirements"></a>연결 요구 사항

**일부 기능은 Azure 연결이 필요합니다.**

**Azure와의 모든 통신은 항상 사용자 환경에서 시작됩니다.** Azure Portal에서 사용자가 시작한 작업의 경우에도 마찬가지입니다.  Azure 대기열에 추가된 작업이 실질적으로 이 경우에 해당합니다.  사용자 환경의 에이전트는 Azure와의 통신을 시작하여 큐에 있는 작업을 확인하고, 작업을 실행하고, 상태/완료/실패를 Azure에 다시 보고합니다.

|**데이터 형식**|**방향**|**필수/선택**|**추가 비용**|**필요한 모드**|**참고**|
|---|---|---|---|---|---|
|**컨테이너 이미지**|Microsoft Container Registry -> 고객|필수|예|간접 또는 직접|컨테이너 이미지는 소프트웨어를 배포하는 방법입니다.  인터넷을 통해 MCR(Microsoft Container Registry)에 연결할 수 있는 환경에서는 컨테이너 이미지를 MCR에서 직접 끌어올 수 있습니다.  배포 환경에 직접 연결되지 않은 경우 MCR에서 이미지를 끌어와 배포 환경의 프라이빗 컨테이너 레지스트리로 푸시할 수 있습니다.  만들 때, MCR 대신 프라이빗 컨테이너 레지스트리에서 끌어오도록 만들기 프로세스를 구성할 수 있습니다. 이는 자동화된 업데이트에도 적용됩니다.|
|**리소스 인벤토리**|고객 환경 -> Azure|필수|예|간접 또는 직접|데이터 컨트롤러, 데이터베이스 인스턴스(PostgreSQL 및 SQL)의 인벤토리는 청구 목적과 모든 데이터 컨트롤러 및 데이터베이스 인스턴스의 인벤토리를 한 곳에서 만들기 위한 목적으로 Azure에 유지됩니다. 이는 Azure Arc 데이터 서비스를 사용하는 환경이 두 개 이상 있는 경우에 특히 유용합니다.  인스턴스에서 프로비저닝, 프로비전 해제, 스케일 아웃/인, 스케일 업/다운이 이루어지면 Azure에서 인벤토리가 업데이트됩니다.|
|**원격 분석 데이터 청구**|고객 환경 -> Azure|필수|예|간접 또는 직접|청구를 위해 데이터베이스 인스턴스의 사용률을 Azure로 보내야 합니다. |
|**데이터 및 로그 모니터링**|고객 환경 -> Azure|Optional|데이터 볼륨에 따라 달라질 수 있습니다([Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 참조).|간접 또는 직접|로컬에서 수집된 모니터링 데이터 및 로그를 Azure Monitor로 전송하여 여러 환경의 데이터를 한곳에 집계할 수 있고, Azure Machine Learning 등의 데이터를 사용하여 경고와 같은 Azure Monitor 서비스도 사용할 수 있습니다.|
|**Azure RBAC(Azure 역할 기반 액세스 제어)**|고객 환경 -> Azure -> 고객 환경|선택 사항|아니요|직접만|Azure RBAC를 사용하려는 경우 항상 Azure를 사용하여 연결을 설정해야 합니다.  Azure RBAC를 사용하지 않으려는 경우 로컬 Kubernetes RBAC를 사용할 수 있습니다.|
|**AAD(Azure Active Directory)(향후)**|고객 환경 -> Azure -> 고객 환경|Optional|있을 수 있지만 Azure AD에 대한 비용을 이미 지불했을 수 있습니다.|직접만|인증에 Azure AD를 사용하려는 경우 항상 Azure를 사용하여 연결을 설정해야 합니다. 인증에 Azure AD를 사용하지 않으려면 Active Directory를 통해 ADFS(Active Directory Federation Services)를 사용할 수 있습니다. **직접 연결 모드 지원 보류 중**|
|**Backup 및 복원**|고객 환경 -> 고객 환경|필수|예|직접 또는 간접|백업 및 복원 서비스는 로컬 스토리지 클래스를 가리키도록 구성할 수 있습니다. **직접 연결 모드 지원 보류 중**|
|**Azure Backup - 장기 보존(향후)**| 고객 환경 -> Azure | Optional| 예(Azure Storage의 경우) | 직접만 |장기 오프사이트 백업을 위해 Azure Backup에서 로컬로 사용되는 백업을 전송하고, 복원을 위해 로컬 환경으로 다시 가져올 수 있습니다. **직접 연결 모드 지원 보류 중**|
|**Azure Defender 보안 서비스(향후)**|고객 환경 -> Azure -> 고객 환경|Optional|예|직접만|**직접 연결 모드 지원 보류 중**|
|**Azure Portal 프로비저닝 및 구성 변경**|고객 환경 -> Azure -> 고객 환경|선택 사항|아니요|직접만|프로비저닝 및 구성 변경은 Azure Data Studio 또는 적절한 CLI를 사용하여 로컬로 수행할 수 있습니다.  직접 연결 모드에서는 Azure Portal에서 프로비저닝하고 구성을 변경할 수도 있습니다.|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>인터넷 주소, 포트, 암호화, 프록시 서버 지원에 대한 세부 정보

현재는 간접 연결 모드만 일반적으로 사용할 수 있습니다. 이 모드에서는 인터넷에서 사용할 수 있는 서비스에 세 가지 연결만 필요합니다. 이러한 연결 항목은 다음과 같습니다.

- [MCR(Microsoft Container Registry)](#microsoft-container-registry-mcr)
- [Azure Resource Manager API](#azure-resource-manager-apis)
- [Azure Monitor API](#azure-monitor-apis)

Azure와 Microsoft Container Registry에 대한 모든 HTTPS 연결은 공식적으로 서명되고 확인 가능한 인증서를 사용하는 SSL/TLS를 사용하여 암호화됩니다.

다음 섹션에서는 해당 연결에 대한 세부 정보를 제공합니다. 

### <a name="microsoft-container-registry-mcr"></a>MCR(Microsoft Container Registry)

Microsoft Container Registry는 Azure Arc 지원 데이터 서비스 컨테이너 이미지를 호스트합니다.  MCR에서 해당 이미지를 끌어와 프라이빗 컨테이너 레지스트리에 푸시하고 해당 프라이빗 컨테이너 레지스트리에서 컨테이너 이미지를 끌어오도록 데이터 컨트롤러 배포 프로세스를 구성할 수 있습니다.

#### <a name="connection-source"></a>연결 원본

컨테이너 이미지를 끌어오는 각 Kubernetes 노드의 Kubernetes kubelet입니다.

#### <a name="connection-target"></a>연결 대상

`mcr.microsoft.com`

#### <a name="protocol"></a>프로토콜

HTTPS

#### <a name="port"></a>포트

443

#### <a name="can-use-proxy"></a>프록시 사용 가능

예

#### <a name="authentication"></a>인증

없음

### <a name="helm-chart-used-to-create-data-controller-in-direct-connected-mode"></a>직접 연결 모드에서 데이터 컨트롤러를 만드는 데 사용 되는 투구 차트

Azure Arc 데이터 컨트롤러 부트스트래퍼를 프로 비전 하는 데 사용 되는 투구 차트와 사용자 지정 리소스 정의, 클러스터 역할 및 클러스터 역할 바인딩과 같은 클러스터 수준 개체는 Azure Container Registry에서 가져옵니다.

#### <a name="connection-source"></a>연결 원본

컨테이너 이미지를 끌어오는 각 Kubernetes 노드의 Kubernetes kubelet입니다.

#### <a name="connection-target"></a>연결 대상

`arcdataservicesrow1.azurecr.io`

#### <a name="protocol"></a>프로토콜

HTTPS

#### <a name="port"></a>포트

443

#### <a name="can-use-proxy"></a>프록시 사용 가능

예

#### <a name="authentication"></a>인증

없음

### <a name="azure-resource-manager-apis"></a>Azure Resource Manager API
Azure Data Studio 및 Azure CLI는 Azure Resource Manager API에 연결하여 일부 기능에 필요한 데이터를 Azure에 보내고 Azure에서 검색합니다.

#### <a name="connection-source"></a>연결 원본

Azure에 연결하는 Azure Data Studio 또는 Azure CLI를 실행하는 컴퓨터입니다.

#### <a name="connection-target"></a>연결 대상

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>프로토콜

HTTPS

#### <a name="port"></a>포트

443

#### <a name="can-use-proxy"></a>프록시 사용 가능

예

#### <a name="authentication"></a>인증 

Azure Active Directory

### <a name="azure-monitor-apis"></a>Azure Monitor API

Azure Data Studio 및 Azure CLI는 Azure Resource Manager API에 연결하여 일부 기능에 필요한 데이터를 Azure에 보내고 Azure에서 검색합니다.

#### <a name="connection-source"></a>연결 원본

모니터링 메트릭 또는 로그를 Azure Monitor에 업로드하는 Azure CLI를 실행하는 컴퓨터입니다.

#### <a name="connection-target"></a>연결 대상

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>프로토콜

HTTPS

#### <a name="port"></a>포트

443

#### <a name="can-use-proxy"></a>프록시 사용 가능

예

#### <a name="authentication"></a>인증 

Azure Active Directory

> [!NOTE]
> 현재 `az arcdata dc export` 명령을 실행하기 위한 데이터 컨트롤러와 Grafana 및 Kibana 대시보드에 대한 모든 브라우저 HTTPS/443 연결은 자체 서명된 인증서를 사용하여 SSL로 암호화됩니다.  해당 SSL 연결의 암호화를 위해 사용자 고유의 인증서를 제공할 수 있는 기능이 추후에 제공됩니다.

Azure Data Studio에서 Kubernetes API 서버로의 연결은 사용자가 설정한 Kubernetes 인증과 암호화를 사용합니다.  Azure Data Studio 또는 CLI의 각 사용자는 Kubernetes API에 대한 인증된 연결이 있어야 Azure Arc 지원 데이터 서비스와 관련된 여러 작업을 수행할 수 있습니다.
