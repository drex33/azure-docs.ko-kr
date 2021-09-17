---
title: Azure Cloud Services로 마이그레이션할 때 발생하는 일반적인 오류 및 알려진 문제(추가 지원)
description: Cloud Services(클래식)에서 클라우드 서비스(추가 지원)로 마이그레이션할 때 발생하는 일반적인 오류 개요
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: ce0c3bb4386e2fcc48ca57a1cd53978fc684d98c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540958"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Azure Cloud Services로 마이그레이션할 때 발생하는 일반적인 오류 및 알려진 문제(추가 지원)

이 문서에서는 Cloud Services(클래식)에서 Cloud Services(추가 지원)로 마이그레이션할 때 발생할 수 있는 알려진 문제 및 일반적인 오류에 대해 설명합니다. 

## <a name="known-issues"></a>알려진 문제
다음 문제를 파악하고 해결하고 있습니다.

| 알려진 문제 | 완화 방법 | 
|---|---|
| 커밋 성공 후 UD별 UD를 다시 시작하는 역할 인스턴스 | 다시 시작 작업은 매월 게스트 OS 롤아웃과 동일한 방법을 따릅니다. 단일 역할 인스턴스를 사용하거나 다시 시작의 영향으로 클라우드 서비스의 마이그레이션을 커밋하지 마십시오.| 
| Azure Portal은 브라우저를 새로 고친 후에는 마이그레이션 상태를 읽을 수 없습니다. | 유효성 검사 및 준비 작업을 다시 실행하여 원래 마이그레이션 상태로 돌아갑니다. | 
| 키 자격 증명 모음에 비밀 리소스로 표시되는 인증서입니다. | 마이그레이션 후 인증서를 인증서 리소스로 다시 업로드하여 Cloud Services(추가 지원)의 업데이트 작업을 간소화합니다. | 
| 배포 레이블은 마이그레이션의 일부로 태그로 저장되지 않습니다. | 마이그레이션 후 태그를 수동으로 만들어 이 정보를 유지합니다.
| 리소스 그룹 이름이 모두 대문자로 되어 있습니다. | 영향을 주지 않습니다. 아직 솔루션을 사용할 수 없습니다. |
| Cloud Services(추가 지원) 잠금의 잠금 이름이 잘못되었습니다. | 영향을 주지 않습니다. 아직 솔루션을 사용할 수 없습니다. | 
| Cloud Services(추가 지원) 포털 블레이드에서 IP 주소 이름이 잘못되었습니다. | 영향을 주지 않습니다. 아직 솔루션을 사용할 수 없습니다. | 
| 마이그레이션된 클라우드 서비스의 업데이트 작업을 수행한 후 가상 IP 주소에 대해 표시된 DNS 이름이 잘못되었습니다. | 영향을 주지 않습니다. 아직 솔루션을 사용할 수 없습니다. | 
| 준비가 완료되면 새 Cloud Services(추가 지원) 배포를 스왑할 수 없음으로 연결하는 것은 허용되지 않습니다. | 새 클라우드 서비스를 스왑 가능으로 준비된 클라우드 서비스로 연결하지 마십시오. | 
| 오류 메시지를 업데이트해야 합니다. | 영향을 주지 않습니다. | 

## <a name="common-migration-errors"></a>일반적인 마이그레이션 오류
일반적인 마이그레이션 오류 및 완화 단계 

| 오류 메시지 | 세부 정보 | 
|---|---|
| API 버전 '2020-10-01-preview'의 `Microsoft.Compute` 네임스페이스에서 리소스 종류를 찾을 수 없습니다. | CloudServices 기능 플래그에 대한 [구독을 등록](in-place-migration-overview.md#setup-access-for-migration)하여 공개 미리 보기에 액세스합니다. | 
| 서버에 내부 오류가 발생했습니다. 요청을 다시 시도하십시오. | 작업을 다시 시도하고, [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html)를 사용하거나 지원에 문의하세요. | 
| 서버에서 클라우드 서비스에 대한 네트워크 리소스를 할당하는 동안 예기치 않은 오류가 발생했습니다. 요청을 다시 시도하십시오. | 작업을 다시 시도하고, [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html)를 사용하거나 지원에 문의하세요. | 
| 클라우드 서비스(클라우드-서비스-이름)의 배포(배포-이름)는 마이그레이션할 가상 네트워크 내에 있어야 합니다. | 가상 네트워크에서 배포를 찾을 수 없습니다. 자세한 내용은 [이](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) 문서를 참조하세요. | 
| 클라우드 서비스(클라우드-서비스-이름)의 배포(배포-이름)가 지역(지역-이름)에 있기 때문에 마이그레이션이 지원되지 않습니다. 허용되는 영역: [사용 가능한 지역 목록] | 지역은 아직 마이그레이션을 지원하지 않습니다. | 
| 역할(역할-이름)과 연결된 서브넷이 없으므로 클라우드 서비스(클라우드-서비스-이름)의 배포(배포-이름)를 마이그레이션할 수 없습니다. 모든 역할을 서브넷과 연결한 다음, 클라우드 서비스의 마이그레이션을 다시 시도합니다. | 마이그레이션 전에 서브넷에 배치하여 클라우드 서비스(클래식) 배포를 업데이트합니다. |  
| 배포에 Azure Resource Manager 구독에 등록되지 않은 기능이 하나 이상 필요하므로 클라우드 서비스(클라우드-서비스-이름)의 배포(배포-이름)는 마이그레이션할 수 없습니다. 이 배포를 마이그레이션하는 데 필요한 모든 기능을 등록합니다. 누락된 기능: [누락된 기능 목록] | 등록된 기능 플래그를 얻으려면 지원에 문의하세요. | 
| 배포의 클라우드 서비스에 두 개의 점유된 슬롯이 있으므로 배포를 마이그레이션할 수 없습니다. 클라우드 서비스의 마이그레이션은 클라우드 서비스에서 유일하게 배포되는 배포에 대해서만 지원됩니다. 클라우드 서비스의 다른 배포를 삭제하여 이 배포의 마이그레이션을 계속합니다. | 자세한 내용은 [지원되지 않는 시나리오](in-place-migration-technical-details.md#unsupported-configurations--migration-scenarios) 목록을 참조하세요. | 
| HostedService(클라우드-서비스-이름)의 배포(배포-이름)가 중간 상태(상태)에 있습니다. 마이그레이션이 허용되지 않습니다. | 배포를 만들거나, 삭제하거나, 업데이트하는 중입니다. 작업이 완료될 때까지 기다리고 다시 시도합니다. | 
| 호스트된 서비스(클라우드-서비스 이름)의 배포(배포-이름)에 예약된 IP가 있지만 예약된 IP 이름이 없습니다. 이 문제를 해결하려면 예약된 IP 이름을 업데이트하거나 Microsoft Azure 서비스 데스크에 문의하십시오. | 클라우드 서비스 배포를 업데이트합니다. | 
| 호스트된 서비스(클라우드-서비스 이름)의 배포(배포-이름)에 예약된 IP(예약된-IP-이름)가 있지만 예약된 IP에 엔드포인트가 없습니다. 이 문제를 해결하려면 예약된 IP에 하나 이상의 엔드포인트를 추가합니다. | 예약된 IP에 엔드포인트를 추가합니다. | 
| HostedService {1}에 배포 {0}의 마이그레이션이 커밋되고 있는 중이며 작업이 완료될 때까지 변경할 수 없습니다.  | 기다리거나 작업을 다시 시도합니다. | 
| HostedService {1}에 배포 {0}의 마이그레이션이 중단되고 있는 중이며 작업이 완료될 때까지 변경할 수 없습니다. | 기다리거나 작업을 다시 시도합니다. |
| HostedService {1}의 배포 {0}에 있는 하나 이상의 VM이 업데이트 작업을 수행하고 있습니다. 이전 작업이 성공적으로 완료될 때까지는 마이그레이션할 수 없습니다. 일정 시간이 지난 후에 다시 시도합니다. | 작업이 완료될 때까지 기다립니다. | 
| HostedService {1}의 배포 {0}에서 마이그레이션에 아직 지원되지 않는 기능(vnet이 아닌 배포)을 사용하므로 마이그레이션이 지원되지 않습니다.| 가상 네트워크에서 배포를 찾을 수 없습니다. 자세한 내용은 [이](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) 문서를 참조하세요. | 
| 가상 네트워크 이름은 Null이거나 비워 둘 수 없습니다. | REST 요청 본문에 가상 네트워크 이름 제공 | 
| 서브넷 이름은 Null이거나 비워 둘 수 없습니다. | REST 요청 본문에 서브넷 이름을 제공합니다. | 
| DestinationVirtualNetwork는 기본값, 새로 만들기 또는 기존 값 중 하나로 설정해야 합니다. | REST 요청 본문에 DestinationVirtualNetwork 속성을 제공합니다. | 
| 기본 VNet 대상 옵션이 구현되어 있지 않습니다. | REST 요청 본문의 DestinationVirtualNetwork 속성에는 "기본" 값이 지원되지 않습니다. | 
| CSPKG를 사용할 수 없으므로 배포 {0}을(를) 마이그레이션할 수 없습니다. | 배포를 업그레이드하고 다시 시도하세요. | 
| ID가 '{0}'인 서브넷이 호스트된 서비스 '{2}'의 배포 '{1}'와(과) 다른 위치에 있습니다. 서브넷의 위치는 '{3}'이고 호스트된 서비스의 위치는 '{4}'입니다.  배포와 동일한 위치에 서브넷을 지정합니다. | 마이그레이션 전 동일한 위치에 서브넷 및 클라우드 서비스를 모두 포함하도록 클라우드 서비스를 업데이트합니다. | 
| HostedService {1}에 배포 {0}의 마이그레이션이 중단되고 있는 중이며 작업이 완료될 때까지 변경할 수 없습니다. | 중단이 완료될 때까지 기다리거나 중단을 다시 시도합니다. [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html)를 사용하거나 그렇지 않으면 지원에 문의하세요. | 
| HostedService {1}의 배포 {0}(은)는 마이그레이션할 준비가 되지 않았습니다. | 커밋 작업을 실행하기 전에 클라우드 서비스에서 준비를 실행합니다. | 
| UnknownExceptionInEndExecute: Contract.Assert failed: rgName이 Null이거나 비어 있습니다. EndExecute에서 RdfeException이 아닌 예외가 수신되었습니다. |   [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html)를 사용하거나 지원에 문의하세요. | 
| UnknownExceptionInEndExecute: 작업이 취소되었습니다. EndExecute에서 RdfeException이 아닌 예외가 수신되었습니다. | [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html)를 사용하거나 지원에 문의하세요. | 
| XrpVirtualNetworkMigrationError: 가상 네트워크 마이그레이션 오류가 발생했습니다. | [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html)를 사용하거나 지원에 문의하세요. | 
| HostedService {1}의 배포 {0}은(는) Virtual Network {2}에 속합니다. 이 HostedService {1}을(를) 마이그레이션하려면 Virtual Network {2}을(를) 마이그레이션합니다. | [Virtual Network 마이그레이션](in-place-migration-technical-details.md#virtual-network-migration)을 참조하세요. | 
| Azure Resource Manager의 리소스 이름에 대한 현재 할당량이 부족하여 마이그레이션을 완료할 수 없습니다. 현재 할당량은 {0}이고 추가 필요는 {1}입니다. 할당량을 올린 후 할당량을 올리고 마이그레이션을 다시 시도하는 지원 요청을 제출합니다.    | 적절한 채널을 따라 할당량 증가를 요청합니다. <br>[네트워킹 리소스에 대한 할당량 증가](../azure-portal/supportability/networking-quota-requests.md) <br>[컴퓨팅 리소스에 대한 할당량 증가](../azure-portal/supportability/per-vm-quota-requests.md) | 
|XrpPaaSMigrationCscfgCsdefValidationMismatch: 배포의 메타 데이터가 유효 하지 않기 때문에 호스팅 서비스 서비스 이름에서 배포 배포 이름에 대 한 마이그레이션을 완료할 수 없습니다. 마이그레이션을 중단 하 고 마이그레이션을 다시 시도 하기 전에 배포를 업그레이드 하세요. 유효성 검사 메시지: 서비스 정의 파일의 서비스 이름 ' 서비스 이름 '이 (가) 서비스 구성 파일의 ' 서비스 이름-파일 이름 '과 일치 하지 않습니다.|.csdef 및 .cscfg 파일의 서비스 이름을 찾습니다.|
|클라우드 서비스 (확장 지원) 리소스를 배포할 때 NetworkingInternalOperationError| 서비스 이름이 역할 이름과 같은 경우 문제가 발생할 수 있습니다. 서비스 및 역할에 diferent names를 사용 하는 것이 좋습니다.|

## <a name="next-steps"></a>다음 단계
마이그레이션 요구 사항에 대한 자세한 내용은 [Azure Cloud Services(추가 지원)로 마이그레이션에 대한 기술 세부 정보](in-place-migration-technical-details.md)를 참조하세요.
