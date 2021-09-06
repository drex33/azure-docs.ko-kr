---
title: 가상 네트워크에서 Azure Spring Cloud 문제 해결
description: Azure Spring Cloud 가상 네트워크에 대한 문제 해결 가이드입니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 95b288181180e2409bcb18f4f8233af568bd311c
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567518"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>가상 네트워크에서 Azure Spring Cloud 문제 해결

이 문서는 가상 네트워크에서 Azure Spring Cloud를 사용할 때 발생할 수 있는 다양한 문제를 해결하는 데 도움이 됩니다.

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure Spring Cloud 서비스 인스턴스를 만드는 동안 문제가 발생했습니다.

Azure Spring Cloud의 인스턴스를 만들려면 가상 네트워크에 인스턴스를 배포할 수 있는 권한이 있어야 합니다.  Spring Cloud 서비스 인스턴스는 자체적으로 [가상 네트워크에 Azure Spring Cloud 서비스 권한을 부여](./how-to-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)해야 합니다.

Azure Portal을 사용하여 Azure Spring Cloud 서비스 인스턴스를 설정하는 경우 Azure Portal에서 권한의 유효성을 검사합니다.

[Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하 여 Azure Spring Cloud 서비스 인스턴스를 설정하려면 다음을 확인합니다.

- 구독이 활성 상태입니다.
- 위치는 Azure Spring Cloud에서 지원됩니다.
- 인스턴스에 대한 리소스 그룹이 이미 만들어졌습니다.
- 리소스 이름이 명명 규칙을 준수합니다. 소문자, 숫자 및 하이픈만 포함해야 합니다. 첫 글자는 문자여야 합니다. 마지막 글자는 문자 또는 숫자여야 합니다. 값은 2~32자를 포함해야 합니다.

Resource Manager 템플릿을 사용하여 Azure Spring Cloud 서비스 인스턴스를 설정하려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/templates/syntax.md)를 참조하세요.

### <a name="common-creation-issues"></a>일반적인 만들기 문제

| 오류 메시지 | 수정 방법 |
|------|------|
| Azure Spring Cloud에서 만든 리소스가 정책에 의해 허용되지 않았습니다. | 네트워크 리소스는 자체 가상 네트워크에 Azure Spring Cloud를 배포할 때 생성됩니다. 이러한 생성을 차단하도록 정의된 [Azure Policy](../governance/policy/overview.md)가 있는지 확인하세요. 생성에 실패한 리소스는 오류 메시지에서 찾을 수 있습니다. |
| 필요한 트래픽이 허용 목록에 없습니다. | 필요한 트래픽이 허용 목록에 있는지 확인하려면 [VNET에서 Azure Spring Cloud 실행에 대한 고객 책임](./vnet-customer-responsibilities.md)을 참조하세요. |

## <a name="my-application-cant-be-registered"></a>내 애플리케이션을 등록할 수 없습니다.

이 문제는 가상 네트워크가 사용자 지정 DNS 설정으로 구성된 경우에 발생합니다. 이 경우 Azure Spring Cloud에서 사용하는 프라이빗 DNS 영역은 효과가 없습니다. 사용자 지정 DNS 서버에서 업스트림 DNS 서버로 Azure DNS IP 168.63.129.16을 추가하세요.

## <a name="other-issues"></a>기타 문제

[일반적인 Azure Spring Cloud 문제 해결](./troubleshoot.md)
