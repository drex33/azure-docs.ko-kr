---
title: Azure HDInsight DomainNotFound 오류로 클러스터 만들기 실패
description: Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법입니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 953feca9542d73776aee749eff416ba3f2e04921
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079969"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>시나리오: Azure HDInsight DomainNotFound 오류로 클러스터 만들기 실패

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 방법을 설명합니다.

## <a name="issue"></a>문제

Enterprise Security Package(HDI Secure) 클러스터 만들기가 `DomainNotFound` 오류 메시지와 함께 실패합니다.

## <a name="cause"></a>원인

잘못된 DNS 설정.

## <a name="resolution"></a>해결 방법

도메인에 가입된 클러스터가 배포되면 HDI는 AAD DS에서 내부 사용자 이름 및 암호를 만들고(각 클러스터에 대해) 모든 클러스터 노드를 이 도메인에 조인합니다. 도메인 가입은 Samba 도구를 사용하여 수행됩니다. 다음 필수 조건이 충족되는지 확인합니다.

* 도메인 이름은 DNS를 통해 확인되어야 합니다.
* 도메인 컨트롤러의 IP 주소는 클러스터가 배포되는 가상 네트워크에 대한 DNS 설정에서 설정해야 합니다.
* 가상 네트워크가 AAD DS의 가상 네트워크와 피어되는 경우 수동으로 수행해야 합니다.
* DNS 전달자를 사용하는 경우 도메인 이름이 가상 네트워크 내에서 올바르게 확인되어야 합니다.
* NSG(보안 정책)는 도메인 가입을 차단해서는 안 됩니다.

### <a name="additional-debugging-steps"></a>추가 디버깅 단계

* 동일한 서브넷에 Windows VM 배포, 사용자 이름 및 암호를 사용하여 컴퓨터에 도메인 가입(제어판 UI를 통해 수행할 수 있음) 또는

* 동일한 서브넷에 Ubuntu VM 배포 및 머신에 도메인 가입
  * SSH를 컴퓨터에 실행
  * sudo su
  * 사용자 이름 및 암호로 스크립트 실행
  * 스크립트는 ping하고 필요한 구성 파일을 만든 다음 도메인을 만듭니다. 성공하면 DNS 설정이 양호합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]