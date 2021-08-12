---
title: 기능을 사용하는 데 필요한 Azure RBAC 권한
titleSuffix: Azure Network Watcher
description: Network Watcher 기능을 사용하는 데 필요한 Azure 역할 기반 액세스 제어 권한에 대해 알아봅니다.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 47418b9c5235255ff7dbf4a1a151e51e4c9aba58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019672"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Network Watcher 기능을 사용하는 데 필요한 Azure 역할 기반 액세스 제어 권한

Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하면 사용자가 할당된 업무를 완료하는 데 필요한 조직의 멤버에게 특정 작업만을 할당할 수 있습니다. Network Watcher 기능을 사용하려면 Azure에 로그인하는 계정은 [소유자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor) 또는 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) 기본 제공 역할에 할당되거나 다음에 나오는 섹션에서 각 Network Watcher 기능에 대해 나열된 작업을 할당받은 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)에 할당되어야 합니다. Network Watcher의 기능에 대해 자세히 알아보려면 [Network Watcher란?](network-watcher-monitoring-overview.md)을 참조하세요.

## <a name="network-watcher"></a>Network Watcher

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Network Watcher 가져오기                                          |
| Microsoft.Network/networkWatchers/write                             | Network Watcher 만들기 또는 업데이트                             |
| Microsoft.Network/networkWatchers/delete                            | Network Watcher 삭제                                       |

## <a name="nsg-flow-logs"></a>NSG 흐름 로그

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | 흐름 로그 구성                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | 흐름 로그에 대한 쿼리 상태                                    |

## <a name="connection-troubleshoot"></a>연결 문제 해결

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | 연결 문제 해결 테스트 시작
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | 연결 문제 해결 테스트의 쿼리 결과                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | 연결 문제 해결 테스트 실행                             |

## <a name="connection-monitor"></a>연결 모니터

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | 연결 모니터 시작                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | 연결 모니터 중지                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | 연결 모니터 쿼리                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | 연결 모니터 가져오기                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | 연결 모니터 만들기                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | 연결 모니터 삭제                                    |

## <a name="packet-capture"></a>패킷 캡처

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | 패킷 캡처의 상태 쿼리                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | 패킷 캡처 중지                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | 패킷 캡처 가져오기                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | 패킷 캡처 만들기                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | 패킷 캡처 삭제                                        |

## <a name="ip-flow-verify"></a>IP 흐름 확인

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | IP 흐름 확인                                              |

## <a name="next-hop"></a>다음 홉

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | VM에서 다음 홉 가져오기                                     |

## <a name="network-security-group-view"></a>네트워크 보안 그룹 보기

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | 보안 그룹 보기                                           |

## <a name="topology"></a>토폴로지

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | 토폴로지 가져오기                                                   |
| Microsoft.Network/networkWatchers/topology/read                     | 위와 동일                                                  |

## <a name="reachability-report"></a>연결 가능성 보고서

| 작업                                                              | Description                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Azure 연결 가능성 보고서 가져오기                               |


## <a name="additional-actions"></a>추가 작업

Network Watcher 기능에는 다음 작업도 필요합니다.

| 작업                                                           | Description                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Read                                     | Azure 역할 할당과 정책 정의를 가져오는 데 사용됩니다.          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | 구독의 모든 리소스 그룹을 열거하는 데 사용됩니다.    |
| Microsoft.Storage/storageAccounts/Read                              | 지정된 스토리지 계정의 속성을 가져오는 데 사용됩니다.   |
| Microsoft.Storage/storageAccounts/listServiceSas/Action, </br> Microsoft.Storage/storageAccounts/listAccountSas/Action, <br> Microsoft.Storage/storageAccounts/listKeys/Action| [스토리지 계정에 안전하게 액세스](../storage/common/storage-sas-overview.md)하고 스토리지 계정에 쓸 수 있도록 SAS(공유 액세스 서명)를 가져오는 데 사용됩니다. |
| Microsoft.Compute/virtualMachines/Read, </br> Microsoft.Compute/virtualMachines/Write| VM에 로그인하고, 패킷 캡처를 수행하고, 스토리지 계정에 업로드하는 데 사용됩니다.|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Network Watcher 확장이 있는지 확인하고 필요한 경우 설치하는 데 사용됩니다. |
| Microsoft.Compute/virtualMachineScaleSets/Read, </br> Microsoft.Compute/virtualMachineScaleSets/Write| 가상 머신 확장 집합에 액세스하고, 패킷 캡처를 수행하고, 스토리지 계정에 업로드하는 데 사용됩니다.|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read, </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Network Watcher 확장이 있는지 확인하고 필요한 경우 설치하는 데 사용됩니다. |
| Microsoft.Insights/alertRules/*                                     | 메트릭 경고를 설정하는 데 사용됩니다.                                     |
| Microsoft.Support/*                                                 | Network Watcher에서 지원 티켓을 만들고 업데이트하는 데 사용됩니다. |