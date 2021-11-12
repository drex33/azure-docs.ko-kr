---
title: Blob Storage에 대한 보안 권장 사항
titleSuffix: Azure Storage
description: Blob Storage에 대한 보안 권장 사항에 대해 알아봅니다. 이 지침을 구현하면 공유 책임 모델에 설명된 대로 보안 의무를 충족하는 데 도움이 됩니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: c3554c60331abacae5796e64cefc0ede7f21b64b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335407"
---
# <a name="security-recommendations-for-blob-storage"></a>Blob Storage에 대한 보안 권장 사항

이 문서에는 Blob Storage에 대한 보안 권장 사항이 포함되어 있습니다. 이러한 권장 사항을 구현하면 공유 책임 모델에 설명된 대로 보안 의무를 충족하는 데 도움이 됩니다. Microsoft가 서비스 공급자의 책임을 이행하는 방법에 대한 자세한 내용은 [클라우드에서의 공동 책임](../../security/fundamentals/shared-responsibility.md)을 참조하세요.

이 문서에 포함 된 권장 사항 중 일부는 Azure에서 리소스를 보호 하는 첫 번째 방어선 인 클라우드에 대해 Microsoft Defender에서 자동으로 모니터링할 수 있습니다. 클라우드 용 Microsoft Defender에 대 한 자세한 내용은 [클라우드 용 Microsoft defender 란?](../../security-center/security-center-introduction.md) 을 참조 하세요.

Microsoft Defender for Cloud는 잠재적 보안 취약성을 식별 하기 위해 Azure 리소스의 보안 상태를 주기적으로 분석 합니다. 그런 다음 이를 해결하는 방법에 대한 권장 사항을 제공합니다. 클라우드 권장 사항에 대 한 Microsoft Defender에 대 한 자세한 내용은 [클라우드 용 Microsoft defender의 보안 권장 사항](../../security-center/security-center-recommendations.md)을 참조 하세요.

## <a name="data-protection"></a>데이터 보호

| 권장 | 주석 | 클라우드 용 Defender |
|-|----|--|
| Azure Resource Manager 배포 모델 사용 | Azure RBAC(역할 기반 액세스 제어) 및 감사, Resource Manager 기반 배포 및 거버넌스, 관리 ID 액세스, 비밀에 대한 Azure Key Vault 액세스, Azure AD 기반 인증 및 Azure Storage 데이터 및 리소스에 대한 액세스 권한 부여를 비롯하여 중요한 보안 향상을 위해 Azure Resource Manager 배포 모델을 사용하여 새 스토리지 계정을 만듭니다. 가능하면 클래식 배포 모델을 사용하는 기존 스토리지 계정을 마이그레이션하여 Azure Resource Manager를 사용합니다. Azure Resource Manager에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요. | - |
| 모든 저장소 계정에 대해 Microsoft Defender를 사용 하도록 설정 | Storage 용 Microsoft Defender는 저장소 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 보안 경고는 활동의 변칙 발생 시 Microsoft Defender for Cloud에서 트리거되고, 의심 스러운 활동의 세부 정보와 위협 조사 및 수정 방법에 대 한 권장 사항을 포함 하 여 전자 메일을 통해 구독 관리자에 게 전송 됩니다. 자세한 내용은 [Storage에 대해 Microsoft Defender 구성](../common/azure-defender-storage-configure.md)을 참조 하세요. | [예](../../security-center/security-center-remediate-recommendations.md) |
| Blob에 대한 일시 삭제 설정 | Blob에 대한 일시 삭제를 통해 Blob 데이터를 삭제한 후 복구할 수 있습니다. Blob의 일시 삭제에 대한 자세한 내용은 [Azure Storage Blob의 일시 삭제](./soft-delete-blob-overview.md)를 참조하세요. | - |
| 컨테이너에 대해 일시 삭제 사용 | 컨테이너에 대해 일시 삭제를 사용하면 컨테이너를 삭제한 후 복구할 수 있습니다. 컨테이너의 일시 삭제에 대한 자세한 내용은 [컨테이너의 일시 삭제](./soft-delete-container-overview.md)를 참조하세요. | - |
| 실수로 또는 악의적으로 삭제 또는 구성 변경 내용을 방지하기 위해 스토리지 계정 잠금 | Azure Resource Manager 잠금을 스토리지 계정에 적용하여 우발적이거나 악의적인 삭제 또는 구성 변경으로부터 계정을 보호합니다. 스토리지 계정을 잠그면 해당 계정의 데이터가 삭제되는 것을 방지할 수 없습니다. 계정 자체가 삭제되는 것을 방지할 뿐입니다. 자세한 내용은 [스토리지 계정에 Azure Resource Manager 잠금 적용](../common/lock-account-resource.md)을 참조하세요.
| 변경할 수 없는 Blob에 중요한 비즈니스 데이터 저장 | WORM(한 번 쓰기, 여러 번 읽기) 상태로 Blob 데이터를 저장하도록 법적 보존 및 시간 기반 보존 정책을 구성합니다. 불변으로 저장된 Blob은 읽을 수 있지만 보존 간격 동안 수정하거나 삭제할 수 없습니다. 자세한 내용은 [변경이 불가능한 스토리지로 업무상 중요한 Blob 데이터 저장](immutable-storage-overview.md)을 참조하세요. | - |
| 스토리지 계정에 대한 보안 전송(HTTPS) 필요 | 스토리지 계정에 대한 보안 전송이 필요한 경우 스토리지 계정에 대한 모든 요청은 HTTPS를 통해 이루어져야 합니다. HTTP를 통해 수행된 모든 요청은 거부됩니다. 모든 스토리지 계정에 대해 항상 보안 전송을 요구하는 것이 좋습니다. 자세한 내용은 [보안 연결을 위해 보안 전송 필요](../common/storage-require-secure-transfer.md)를 참조하세요. | - |
| SAS(공유 액세스 서명) 토큰을 HTTPS 연결로만 제한 | 클라이언트가 SAS 토큰을 사용하여 Blob 데이터에 액세스할 때 HTTPS를 요구하면 도청 위험을 최소화하는 데 도움이 됩니다. 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요. | - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

| 권장 | 주석 | 클라우드 용 Defender |
|-|----|--|
| Azure AD(Azure Active Directory)를 사용하여 Blob 데이터에 대한 액세스 권한 부여 | Azure AD는 Blob Storage에 대한 요청에 권한을 부여하기 위해 공유 키보다 뛰어난 보안과 사용 편의성을 제공합니다. 자세한 내용은 [Azure Storage의 데이터에 대한 액세스 권한 부여](../common/authorize-data-access.md)를 참조하세요. | - |
| Azure RBAC를 통해 Azure AD 보안 주체에 권한을 할당하는 경우 최소 권한의 주체에 유의 | 사용자, 그룹 또는 애플리케이션에 역할을 할당할 때 해당 보안 주체에게 작업을 수행하는 데 필요한 권한만 부여합니다. 리소스에 대한 액세스를 제한하면 의도하지 않은 데이터의 악의적인 오용을 방지할 수 있습니다. | - |
| 사용자 위임 SAS를 사용하여 Blob 데이터에 대한 제한된 액세스 권한을 클라이언트에 부여합니다. | 사용자 위임 SAS는 Azure AD(Azure Active Directory) 자격 증명으로 보호되며, SAS에 지정된 권한으로도 보호됩니다. 사용자 위임 SAS는 범위 및 기능 측면에서 서비스 SAS와 유사하지만 서비스 SAS에 비해 보안 혜택을 제공합니다. 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요. | - |
| Azure Key Vault를 사용하여 계정 액세스 키 보호 | Azure AD를 사용하여 Azure Storage에 대한 요청을 승인하는 것이 좋습니다. 그러나 공유 키 인증을 사용해야 하는 경우에는 Azure Key Vault를 사용하여 계정 키를 보호합니다. 애플리케이션에 키를 저장하는 대신 런타임에 키 자격 증명 모음에서 키를 검색할 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault 모음 개요](../../key-vault/general/overview.md)를 참조하세요. | - |
| 정기적으로 계정 키 다시 생성 | 정기적으로 계정 키를 회전하면 악의적인 행위자에게 데이터를 노출하는 위험을 줄일 수 있습니다. | - |
| 공유 키 승인 허용 안 함 | 스토리지 계정에 대한 공유 키 권한 부여를 허용하지 않으면 Azure Storage는 계정 액세스 키로 권한 부여된 해당 계정에 대한 모든 후속 요청을 거부합니다. Azure AD로 권한이 부여된 보안 요청만 성공합니다. 자세한 내용은 [Azure Storage 계정에 대한 공유 키 권한 부여 방지](../common/shared-key-authorization-prevent.md)를 참조하세요. | - |
| SAS에 권한을 할당하는 경우 최소 권한의 주체를 염두에 두어야 합니다. | SAS를 만들 때 클라이언트가 기능을 수행하는 데 필요한 권한만 지정합니다. 리소스에 대한 액세스를 제한하면 의도하지 않은 데이터의 악의적인 오용을 방지할 수 있습니다. | - |
| 클라이언트에게 발급하는 SAS에 대한 해지 계획 보유 | SAS가 손상되면 가능한 한 빨리 해당 SAS를 해지하는 것이 좋습니다. 사용자 위임 SAS를 해지하려면 사용자 위임 키를 해지하여 해당 키와 관련된 모든 서명을 신속하게 무효화합니다. 저장된 액세스 정책과 연결된 서비스 SAS를 해지하려면 저장된 액세스 정책을 삭제하거나, 정책의 이름을 바꾸거나, 만료 시간을 과거 시간으로 변경할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.  | - |
| 서비스 SAS가 저장된 액세스 정책과 연결되지 않은 경우 만료 시간을 1시간 이하로 설정 | 저장된 액세스 정책과 연결되지 않은 서비스 SAS는 취소할 수 없습니다. 이러한 이유로, SAS가 1시간 이내에 유효하도록 만료 시간을 제한하는 것이 좋습니다. | - |
| 컨테이너 및 Blob에 대한 익명 공용 읽기 액세스 사용 안 함 | 컨테이너 및 해당 Blob에 대한 익명 공용 읽기 액세스는 모든 클라이언트에 해당 리소스에 대한 읽기 전용 액세스 권한을 부여합니다. 시나리오에 필요한 경우를 제외하고 공용 읽기 액세스를 사용하지 않도록 합니다. 스토리지 계정에 대한 익명 공용 액세스를 사용하지 않도록 설정하는 방법을 알아보려면 [컨테이너 및 Blob에 대한 익명 공용 읽기 액세스 구성](anonymous-read-access-configure.md)을 참조하세요.  | - |

## <a name="networking"></a>네트워킹

| 권장 | 주석 | 클라우드 용 Defender |
|-|----|--|
| 스토리지 계정에 대한 TLS(전송 계층 보안)의 최소 필수 버전 구성  | Azure Storage 계정에 대해 최소 버전의 TLS를 구성하여 클라이언트가 더 안전한 TLS 버전으로 해당 계정에 대한 요청을 수행하도록 해야 합니다. 자세한 내용은 [스토리지 계정에 필요한 최소 버전의 TLS(전송 계층 보안) 구성](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.| - |
| 모든 스토리지 계정에서 **보안 전송 필요** 옵션을 사용하도록 설정 | **보안 전송 필요** 옵션을 사용하도록 설정하는 경우 스토리지 계정에 대한 모든 요청은 보안 연결을 통해야 합니다. HTTP를 통한 모든 요청은 실패합니다. 자세한 내용은 [Azure Storage에서 보안 전송 필요](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요. | [예](../../security-center/security-center-remediate-recommendations.md) |
| 방화벽 규칙 사용 | 지정된 IP 주소, IP 범위 또는 Azure VNet(Virtual Network)의 서브넷 목록에서 시작되는 요청에 대한 스토리지 계정에 대한 액세스를 제한하는 방화벽 규칙을 구성합니다. 방화벽 규칙 구성에 대한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요. | - |
| 신뢰할 수 있는 Microsoft 서비스가 스토리지 계정에 액세스하도록 허용 | 스토리지 계정에 대해 방화벽 규칙을 켜면 기본적으로 Azure VNet(Virtual Network) 내에서 작동하는 서비스 또는 허용되는 퍼블릭 IP 주소에서 요청하지 않는 한, 들어오는 데이터 요청이 차단됩니다. 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다. 신뢰할 수 있는 Microsoft 서비스에서 스토리지 계정에 액세스할 수 있도록 허용하는 예외를 추가하여 다른 Azure 서비스에서의 요청을 허용할 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대한 예외를 추가하는 방법에 대한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.| - |
| 프라이빗 엔드포인트 사용 | 프라이빗 엔드포인트는 Azure VNet(가상 네트워크)의 개인 IP 주소를 스토리지 계정에 할당합니다. 프라이빗 링크를 통해 VNet과 스토리지 계정 간의 모든 트래픽을 보호합니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [Azure 프라이빗 엔드포인트를 사용하여 스토리지 계정에 비공개로 연결](../../private-link/tutorial-private-endpoint-storage-portal.md)을 참조하세요. | - |
| VNet 서비스 태그 사용 | 서비스 태그는 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다. Azure Storage에서 지원하는 서비스 태그에 대한 자세한 내용은 [Azure 서비스 태그 개요](../../virtual-network/service-tags-overview.md)를 참조하세요. 서비스 태그를 사용하여 아웃바운드 네트워크 규칙을 만드는 방법을 보여 주는 자습서는 [PaaS 리소스에 대한 액세스 제한](../../virtual-network/tutorial-restrict-network-access-to-resources.md)을 참조하세요. | - |
| 특정 네트워크에 대한 네트워크 액세스 제한 | 액세스를 요구하는 클라이언트를 호스트하는 네트워크에 대한 네트워크 액세스를 제한하면 리소스가 네트워크 공격에 노출되는 것을 줄일 수 있습니다. | [예](../../security-center/security-center-remediate-recommendations.md) |
| 네트워크 라우팅 기본 설정 구성 | Azure Storage 계정에 대한 네트워크 라우팅 기본 설정을 구성하여 Microsoft 글로벌 네트워크 또는 인터넷 라우팅을 사용하여 인터넷을 통해 클라이언트에서 계정으로 네트워크 트래픽을 라우팅하는 방법을 지정할 수 있습니다. 자세한 내용은 [Azure Storage에 대한 네트워크 라우팅 기본 설정 구성](../common/network-routing-preference.md)을 참조하세요. | - |

## <a name="loggingmonitoring"></a>로깅/모니터링

| 권장 | 주석 | 클라우드 용 Defender |
|-|----|--|
| 요청에 권한을 부여하는 방법 추적 | Azure Storage 로깅을 사용하도록 설정하여 Azure Storage의 각 요청에 권한이 부여되었는지 추적합니다. 로그는 OAuth 2.0 토큰을 사용하거나, 공유 키를 사용하거나, SAS(공유 액세스 서명)를 사용하여 요청이 익명으로 수행되었는지 나타냅니다. 자세한 내용은 [Azure Monitor로 Azure Blob Storage 모니터링](monitor-blob-storage.md) 또는 [클래식 모니터링으로 Azure Storage 분석 로깅](../common/storage-analytics-logging.md)을 참조하세요. | - |
| Azure Monitor의 경고 설정 | 설정된 빈도로 리소스 로그를 평가하고 결과에 따라 경고를 발생하도록 로그 경고를 구성합니다. 자세한 내용은 [Azure Monitor의 로그 경고](../../azure-monitor/alerts/alerts-unified-log.md)를 참조하세요. | - |

## <a name="next-steps"></a>다음 단계

- [Azure 보안 설명서](../../security/index.yml)
- [보안 개발 설명서](../../security/develop/index.yml).
