---
title: 파일 포함
description: 파일 포함
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 10/27/2021
ms.author: dasto
ms.openlocfilehash: 1811faeeb5de3e300a35f7107c5cdf23bf20151c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520517"
---
### <a name="provider-limits--quota"></a>공급자 제한 및 할당량

Azure Quantum Service는 자사 및 타사 서비스 공급자를 모두 지원합니다. 타사 공급자는 제한 및 할당량을 소유합니다. 사용자는 타사 공급자를 구성할 때 Azure Portal 제안 및 제한을 볼 수 있습니다. 

Microsoft 자사 최적화 솔루션 공급자에 대해 게시된 할당량 제한은 아래에서 확인할 수 있습니다. 

#### <a name="learn--develop-sku"></a>SKU 학습 및 개발

| 리소스 | 제한 |
| --- | --- |
| CPU 기반 동시 작업 | 최대<sup>5개의</sup> 동시 작업 |
| FPGA 기반 동시 작업 | 최대<sup>2개의</sup> 동시 작업 |
| CPU 기반 해 찾기 시간 | 매월 20시간  |
| FPGA 기반 해 찾기 시간 | 매월 1시간  |

Learn & Develop SKU에서 할당량 한도 증가를 요청할 **수 없습니다.** 대신 SKU 규모의 성능으로 전환해야 합니다.

#### <a name="performance-at-scale-sku"></a>SKU 규모의 성능

| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| CPU 기반 동시 작업 | 최대 100개의 동시 작업<sup></sup> | 기본 제한과 동일 |
| FPGA 기반 동시 작업 | 최대<sup>10개의</sup> 동시 작업 | 기본 제한과 동일 |
| 해 찾기 시간 | 매월 1,000시간  | 매월 최대 50,000 시간 |

한도 증가를 요청하려면 Azure 지원 문의합니다.

자세한 내용은 [Azure Quantum 가격 책정 페이지](https://aka.ms/AQ/Pricing)를 참조하세요.
타사 제품에 대한 자세한 내용은 Azure Portal 관련 공급자 가격 책정 페이지를 검토하세요.

<sup>1</sup> 동시에 큐에 대기할 수 있는 작업 수를 설명합니다.
