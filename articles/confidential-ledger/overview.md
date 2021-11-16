---
title: Microsoft Azure 기밀 원장 개요
description: 중요한 데이터 레코드를 관리하는 데 사용되는 매우 안전한 서비스인 Azure 기밀 원장의 개요
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 2f0636d5ef4b4e1fa3a9576912630470af7fa388
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337797"
---
# <a name="microsoft-azure-confidential-ledger-preview"></a>Microsoft Azure 기밀 원장(미리 보기)

Microsoft ACL(Azure 기밀 원장)은 중요한 데이터 레코드를 관리하는 데 사용하는 매우 안전한 새로운 서비스입니다. Azure 기밀 원장은 사용 권한이 있는 블록체인 모델을 기반으로 고유한 데이터 무결성 이점을 제공합니다. 여기에는 원장을 추가 전용으로 만드는 불변성, 모든 레코드가 그대로 유지되도록 하는 변조 방지가 포함됩니다.

기밀 원장은 하드웨어 지원 보안 Enclave에서만 실행되며, 긴밀하게 모니터링되며 격리된 런타임 환경으로, 잠재적인 공격을 방지합니다. 또한 원장을 초월할 수 있는 사람은 없습니다(Microsoft도 불가). Azure 기밀 원장은 솔루션으로 직접 설계되어 최소형 TCB(신뢰할 수 있는 컴퓨팅 기반)에서 실행되어 원장 서비스 개발자, 데이터 센터 기술자 및 클라우드 관리자의 액세스를 차단합니다.

Azure 기밀 원장은 중요한 메타데이터 레코드를 수정해서는 안 되는 사용 사례에 적합합니다. 예를 들어 규정 준수 및 보관을 위해 영원히 수정해서는 안 되는 사용 사례가 있습니다. 원장에 저장할 수 있는 항목의 몇 가지 예를 들면 다음과 같습니다.

- 비즈니스 트랜잭션과 관련된 레코드(예: 송금 또는 기밀 문서 편집)
- 신뢰할 수 있는 자산에 대한 업데이트(예: 핵심 애플리케이션 또는 계약)
- 관리 및 제어 변경(예: 액세스 권한 부여)
- 운영 IT 및 보안 이벤트(예: Microsoft Defender for Cloud 경고)

자세한 내용을 보려면 [Microsoft Ignite 2020 Azure 기밀 원장 데모](https://mediusprodstatic.studios.ms/asset-b88de19d-4187-40c4-98f2-a65efc419e2a/OD221_1920x1080_AACAudio_1461.mp4?sv=2018-03-28&sr=b&sig=k5roi6WXnlqK1zP0fs5KYlJd4FD3Nuaf97z%2B2gV0aTs%3D&st=2020-09-22T08%3A05%3A01Z&se=2025-09-22T08%3A10%3A01Z&sp=r&rscd=filename%3DIG20-OD221-Inside%2BAzure%2BDatacenter%2BArchitecture%2Bwith%2BMark%2BRu.mp4)를 시청하세요.

## <a name="key-features"></a>주요 기능

기밀 원장은 신규 또는 기존 애플리케이션에 통합할 수 있는 REST API를 통해 노출됩니다. 기밀 원장은 관리자가 관리 API(컨트롤 플레인)를 활용하여 관리할 수 있습니다. 또한 애플리케이션 코드에서 직접 기능 API(데이터 평면)를 통해 호출할 수도 있습니다. 관리 API는 만들기, 업데이트, 가져오기 및 삭제와 같은 기본 작업을 지원합니다. 기능 API를 사용하면 인스턴스화된 원장과 직접 상호 작용하고 데이터 배치 및 가져오기와 같은 작업을 포함할 수 있습니다.

## <a name="ledger-security"></a>원장 보안

이 섹션에서는 원장에 대한 보안 보호를 정의합니다. 원장 API는 클라이언트 인증서 기반 인증을 사용합니다. 현재 원장은 소유자 역할을 사용하여 인증서 기반 인증 프로세스를 지원합니다. AAD(Azure Active Directory) 기반 인증 및 역할 기반 액세스(예: 소유자, 읽기 권한자 및 기여자)에 대한 지원도 추가될 예정입니다.

원장에 대한 데이터는 TLS 1.2 연결을 통해 전송되고 TLS 1.2 연결은 하드웨어 지원 보안 Enclave(Intel® SGX Enclave) 내에서 종료됩니다. 따라서 아무도 고객의 클라이언트와 기밀 원장 서버 노드 사이의 연결을 가로챌 수 없습니다.

### <a name="ledger-storage"></a>원장 스토리지

기밀 원장은 Azure Storage 계정에 속한 Blob 스토리지 컨테이너에서 블록으로 생성됩니다. 트랜잭션 데이터는 필요에 따라 암호화된 상태로 또는 일반 텍스트로 저장될 수 있습니다. 원장을 만들 때는 [기밀 원장 서비스 주체 등록](register-ledger-service-principal.md)에 설명된 단계에 따라 스토리지 계정을 연결합니다.

기밀 원장은 관리자가 관리 API(제어 평면)를 활용하여 관리할 수 있으며, 애플리케이션 코드에서 직접 기능 API(데이터 평면)를 통해 호출할 수 있습니다. 관리 API는 만들기, 업데이트, 가져오기 및 삭제와 같은 기본 작업을 지원합니다.

기능 API를 사용하면 인스턴스화된 기밀 원장과 직접 상호 작용하고 데이터 배치 및 가져오기와 같은 작업을 포함할 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

- 기밀 원장을 만든 후에는 원장 유형을 변경할 수 없습니다.
- 기밀 원장은 현재 표준 Azure 재해 복구를 지원하지 않습니다. 그러나 Azure 기밀 원장은 여러 독립 노드에서 실행되므로 Azure 지역 내에서 중복도를 기본 제공합니다.
- Azure 기밀 원장을 삭제하면 "영구 삭제"가 발생하므로 삭제 후 데이터를 복구할 수 없습니다.
- Azure 기밀 원장 이름은 전역적으로 고유해야 합니다. 이름이 같은 원장은 형식에 관계없이 허용되지 않습니다.

## <a name="terminology"></a>용어

| 용어 | 정의 |
|--|--|
| ACL | Azure 기밀 원장 |
| 원장 | 트랜잭션의 불변성 추가 레코드(블록체인이라고도 함) |
| Commit | 트랜잭션이 노드에 로컬로 커밋되었다는 확인. 로컬 커밋 자체가 트랜잭션이 원장의 일부임을 보장하지는 않습니다. |
| 글로벌 커밋 | 트랜잭션이 전역적으로 커밋되었으며 원장의 일부라는 확인. |
| Receipt | 트랜잭션이 원장에 의해 처리되었다는 증명. |

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 기밀 원장 아키텍처](architecture.md)
- [빠른 시작: Azure Portal](quickstart-portal.md)
- [빠른 시작: Python](quickstart-python.md)
- [빠른 시작: ARM(Azure Resource Manager) 템플릿](quickstart-portal.md)
