---
title: Azure 기밀 컴퓨팅 개요
description: ACC(Azure 기밀 컴퓨팅) 개요
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77c65a7c5418ebefd7b2414d04bec614eccdc5ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284363"
---
# <a name="what-is-confidential-computing"></a>기밀 컴퓨팅이란?

기밀 컴퓨팅을 사용하면 처리하는 동안 중요한 데이터를 격리할 수 있습니다. 많은 업계에서 기밀 컴퓨팅을 사용하여 기밀 컴퓨팅을 통해 데이터를 보호합니다.

- 금융 데이터 보안
- 환자 정보 보호
- 중요한 정보에 대한 기계 학습 프로세스 실행
- 여러 원본에서 암호화된 데이터 세트에 대한 알고리즘 수행


## <a name="overview"></a>개요
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

클라우드 데이터 보안이 중요하다는 것을 알고 있습니다. 관심 사항에도 주의를 기울이고 있습니다. 중요한 워크로드를 클라우드로 이동할 때 고객이 가질 수 있는 몇 가지 질문은 다음과 같습니다. 

- Microsoft에서 암호화되지 않은 데이터에 액세스하지 못하도록 하려면 어떻게 해야 하나요?
- 회사 내에서 권한이 있는 관리자의 보안 위협을 방지하려면 어떻게 해야 하나요?
- 타사에서 중요한 고객 데이터에 액세스하지 못하도록 방지하는 다른 방법은 무엇인가요?

Azure를 통해 공격 표면을 최소화하여 더 강력한 데이터 보호를 확보할 수 있습니다. Azure는 클라이언트 쪽 암호화 및 서버 쪽 암호화와 같은 모델을 통해 [**미사용 데이터**](../security/fundamentals/encryption-atrest.md)를 보호할 수 있는 많은 도구를 이미 제공하고 있습니다. 또한 Azure는 TLS 및 HTTPS와 같은 보안 프로토콜을 통해 [**전송 중 데이터**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit)를 암호화하는 메커니즘도 제공합니다. 이 페이지에서는 데이터 암호화의 세 번째 단계인 **사용 중 데이터** 암호화를 소개합니다.

## <a name="introduction-to-confidential-computing"></a>기밀 컴퓨팅 소개  

기밀 컴퓨팅은 기밀 컴퓨팅의 채택을 정의 및 가속화하기 위한 기반인 [CCC(기밀 컴퓨팅 컨소시엄)](https://confidentialcomputing.io/)에서 정의한 업계 용어입니다. CCC는 다음과 같이 기밀 컴퓨팅을 정의합니다. 하드웨어 기반 TEE(신뢰할 수 있는 실행 환경)에서 계산을 수행하여 사용 중인 데이터 보호

TEE는 권한 있는 코드만 실행하는 환경입니다. TEE의 모든 데이터는 해당 환경 외부의 코드에서 읽거나 변경할 수 없습니다. 

### <a name="lessen-the-need-for-trust"></a>신뢰의 필요성 완화
클라우드에서 워크로드를 실행하려면 트러스트가 필요합니다. 애플리케이션의 다양한 구성 요소를 사용하는 다양한 공급자에게 이 트러스트를 제공합니다.


**앱 소프트웨어 공급 업체**: 오픈 소스를 사용하거나 사내 애플리케이션 소프트웨어를 빌드하여 온-프레미스를 배포하여 소프트웨어를 신뢰합니다.

**하드웨어 공급 업체**: 온-프레미스 하드웨어 또는 사내 하드웨어를 사용하여 하드웨어를 신뢰합니다. 

**인프라 공급자**: 클라우드 공급자를 신뢰하거나 고유한 온-프레미스 데이터 센터를 관리합니다.

Azure 기밀 컴퓨팅을 사용하면 컴퓨팅 클라우드 인프라의 다양한 측면에서 신뢰 필요성을 줄여 클라우드 공급자를 더 쉽게 신뢰할 수 있습니다. Azure 기밀 컴퓨팅은 호스트 OS 커널, 하이퍼바이저, VM 관리자 및 호스트 관리자에 대한 트러스트를 최소화합니다.

## <a name="next-steps"></a>다음 단계

Azure의 모든 기밀 컴퓨팅 제품에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 기밀 컴퓨팅 서비스 개요](overview-azure-products.md)
