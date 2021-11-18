---
title: Apache Cassandra에 대 한 Azure Managed Instance의 보안-개요
description: Apache Cassandra에 대 한 Azure Managed Instance에서 제공 하는 데이터베이스 보안 모범 사례 및 주요 기능에 대해 알아봅니다. 이를 통해 데이터베이스 위반을 예방, 감지 및 대응할 수 있습니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 96b03347adcabdd5bd25d4f57c5ea80946832610
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723326"
---
# <a name="security-in-azure-managed-instance-for-apache-cassandra---overview"></a>Apache Cassandra에 대 한 Azure Managed Instance의 보안-개요

이 문서에서는 데이터베이스 위반을 예방, 감지 및 대응 하는 데 도움이 되는 Apache Cassandra Azure Managed Instance에서 제공 하는 데이터베이스 보안 모범 사례 및 주요 기능에 대해 설명 합니다.

## <a name="how-do-i-secure-my-database"></a>내 데이터베이스를 보호하는 방법

데이터 보안은 사용자, 고객 그리고 데이터베이스 공급자 간의 공동 책임입니다. 선택한 데이터베이스 공급자에 따라 담당하는 책임의 양이 달라질 수 있습니다. 온-프레미스 솔루션을 선택한 경우 끝점 보호부터 하드웨어의 물리적 보안까지 모든 항목을 제공해야 하며 이것은 쉬운 작업이 아닙니다. Apache Cassandra에 대 한 Azure Managed Instance와 같은 관리 되는 서비스를 선택 하는 경우 관심 영역이 줄어듭니다. 

## <a name="how-does-azure-managed-instance-secure-my-database"></a>Azure Managed Instance 내 데이터베이스를 보호 하는 방법


|보안 요구 사항|Apache Cassandra의 보안 접근 방식에 대 한 Azure Managed Instance|
|---|---|
|네트워크 보안| Apache Cassandra 리소스에 대 한 Azure 관리 되는 인스턴스는 개인 Ip를 사용 하 여 가상 네트워크에만 삽입 되는 각 리소스에 대 한 Nic (네트워크 인터페이스 카드)를 사용 하 여 Microsoft 테 넌 트에서 호스팅됩니다. 이 서비스에 노출 된 공용 Ip가 없습니다.|
|자동 온라인 백업|Apache Cassandra 데이터 센터에 대 한 Azure Managed Instance은 4 시간 마다 백업 되 고 이틀 동안 보존 됩니다. 백업은 로컬 저장소 계정에 저장 됩니다.|
|삭제된 데이터 복원|자동 온라인 백업을 사용 하 여 실수로 삭제 했을 수 있는 데이터를 복구할 수 있습니다. 삭제 이벤트 이후 약 이틀 이내에 데이터를 백업할 수 있습니다.|
|HTTPS/SSL/TLS 및 디스크 암호화 | Apache Cassandra에 대 한 Azure Managed Instance에서는 모든 데이터가 미사용 시 암호화 됩니다. 서버 SSL (TLS 1.2) 및 노드 간 암호화가 적용 됩니다. 클라이언트 SSL은 선택적 구성입니다. 디스크에서 데이터를 암호화 하기 위한 고객 관리 키가 지원 됩니다. 자세한 내용은 [여기](customer-managed-keys.md) 를 참조 하세요. |
|공격 모니터|Apache Cassandra에 대 한 Azure Managed Instance는 [Azure Monitor](../azure-monitor/overview.md)와 통합 됩니다. 감사 로깅 및 활동 로그를 사용하여 계정에서 정상 및 비정상적인 활동을 모니터링할 수 있습니다. 리소스에서 수행 된 작업, 작업을 시작한 사람, 작업 상태, 작업 상태 및 기타 많은 작업을 볼 수 있습니다.|
|공격에 대응|잠재적인 공격을 보고 하기 위해 Azure 지원에 연락한 경우 5 단계 인시던트 대응 프로세스가 시작 됩니다. 5 단계 프로세스의 목표는 문제가 감지 되 고 조사가 시작 된 후에 최대한 빠르게 정상적인 서비스 보안 및 작업을 복원 하는 것입니다.|
|패치된 서버|Apache Cassandra에 대 한 Azure Managed Instance는 관리 되는 데이터베이스로 서버를 관리 하 고 패치를 자동으로 수행할 필요가 없습니다.|
|인증| 최신 인증 목록은 전체 [Azure 규정 준수 사이트](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

Microsoft 인증에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/)를 참조하세요.
