---
title: 인바운드/아웃바운드 IP 주소
description: 인바운드 및 아웃바운드 IP 주소가 Azure App Service에서 어떻게 사용되는지와 언제 변경되는지 및 앱에 필요한 주소를 찾는 방법에 대해 설명합니다.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: ea667fcfe70e109038d74e7c1fa0281bbc2b20bb
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122538986"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure App Service의 인바운드 및 아웃바운드 IP 주소

[Azure App Service](overview.md)는 [App Service Environment](environment/intro.md)를 제외한 다중 테넌트 서비스입니다. App Service Environment에 없는 앱([격리 계층](https://azure.microsoft.com/pricing/details/app-service/)에 있지 않음)은 다른 앱과 네트워크 인프라를 공유합니다. 결과적으로, 앱의 인바운드 및 아웃바운드 IP 주소는 다를 수 있으며 특정 상황에서 변경할 수 있습니다.

[App Service Environment](environment/intro.md)는 전용 네트워크 인프라를 사용하므로 App Service Environment에서 실행되는 앱은 인바운드 및 아웃바운드 연결 모두에 대해 고정 전용 IP 주소를 가져옵니다.

## <a name="how-ip-addresses-work-in-app-service"></a>App Service에서 IP 주소가 작동하는 방식

App Service 앱은 App Service 요금제에서 실행되고 App Service 요금제는 Azure 인프라의 배포 단위 중 하나(내부적으로는 웹 공간이라고 함)에 배포됩니다. 각 배포 단위에는 하나의 공용 인바운드 IP 주소와 [아웃바운드 IP 주소](#find-outbound-ips) 집합을 포함하는 가상 IP 주소 집합이 할당됩니다. 동일한 배포 단위의 모든 App Service 요금제와 이러한 계획에서 실행되는 앱 인스턴스는 동일한 가상 IP 주소 집합을 공유합니다. App Service Environment([격리된 계층](https://azure.microsoft.com/pricing/details/app-service/)의 App Service 요금제)의 경우 App Service 요금제는 배포 단위 그 자체이므로 가상 IP 주소는 결과적으로 App Service 요금제 전용입니다.

배포 단위 간에 App Service 요금제를 이동할 수 없으므로 일반적으로 앱에 할당된 가상 IP 주소는 동일하게 유지되지만 예외가 있습니다.

## <a name="when-inbound-ip-changes"></a>인바운드 IP가 변경되는 경우

스케일 아웃 인스턴스의 수에 관계 없이 각 앱에는 단일 인바운드 IP 주소가 있습니다. 다음 작업 중 하나를 수행할 때 인바운드 IP 주소가 변경될 수 있습니다.

- 앱을 삭제하고 다른 리소스 그룹에서 다시 만듭니다(배포 단위가 바뀔 수 있음).
- 리소스 그룹 _및_ 지역 조합에서 마지막 앱을 삭제하고 다시 만듭니다(배포 단위가 바뀔 수 있음).
- 인증서 갱신 중과 같이 기존 IP 기반 TLS/SSL 바인딩을 삭제합니다(자세한 정보는 [인증서 갱신](configure-ssl-certificate.md#renew-certificate) 참조).

## <a name="find-the-inbound-ip"></a>인바운드 IP 찾기

로컬 터미널에서 다음 명령을 실행합니다.

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>고정 인바운드 IP 가져오기

경우에 따라 앱에 대해 고정 전용 IP 주소를 사용하고자 할 수 있습니다. 고정 인바운드 IP 주소를 가져오려면 [사용자 지정 도메인 보안](configure-ssl-bindings.md#secure-a-custom-domain)을 설정해야 합니다. 실제로 앱을 보호하는 TLS 기능이 필요하지 않은 경우 이 바인딩에 대한 자체 서명된 인증서를 업로드할 수도 있습니다. IP 기반 TLS 바인딩에서 인증서는 IP 주소 자체에 바인딩되므로 App Service는 고정 IP 주소를 프로비저닝하여 이를 수행합니다. 

## <a name="when-outbound-ips-change"></a>아웃바운드 IP가 변경되는 경우

스케일 아웃 인스턴스의 수에 관계 없이 각 앱에는 지정된 시간에 정해진 아웃바운드 IP 주소 갯수가 있습니다. App Service 앱에서 가령 백 엔드 데이터베이스로의 모든 아웃바운드 연결은 원래 IP 주소로 아웃바운드 IP 주소 중 하나를 사용합니다. 사용할 IP 주소는 런타임에 임의로 선택됩니다. 따라서 백 엔드 서비스는 앱에 대한 아웃바운드 IP 주소의 방화벽을 열어야 합니다.

앱에 대한 아웃바운드 IP 주소 집합은 다음 작업 중 하나를 수행할 때 변경됩니다.

- 앱을 삭제하고 다른 리소스 그룹에서 다시 만듭니다(배포 단위가 바뀔 수 있음).
- 리소스 그룹 _및_ 지역 조합에서 마지막 앱을 삭제하고 다시 만듭니다(배포 단위가 바뀔 수 있음).
- 하위 계층(**기본**, **표준**, **프리미엄**), **프리미엄V2** 계층, **프리미엄V3** 계층 사이에 앱을 스케일링합니다(IP 주소를 세트에 추가하거나 세트에서 뺄 수 있음).

가격 책정 계층에 관계 없이 앱에서 사용할 수 있는 모든 아웃바운드 IP 주소 집합을 찾을 수 있습니다. 이는 `possibleOutboundIpAddresses` 속성이나 Azure Portal의 **추가 아웃바운드 IP 주소** 필드(위치는 **속성** 블레이드)에서 찾을 수 있습니다. [아웃바운드 IP 찾기](#find-outbound-ips)를 참조하세요.

## <a name="find-outbound-ips"></a>아웃바운드 IP 찾기

Azure Portal의 앱에서 현재 사용하는 아웃바운드 IP 주소를 찾으려면 앱의 왼쪽 탐색 창에서 **속성** 을 클릭합니다. 이러한 필드는 **아웃바운드 IP 주소** 필드에 나열됩니다.

[Cloud Shell](../cloud-shell/quickstart.md)에서 다음 명령을 실행하면 동일한 정보를 찾을 수 있습니다.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

가격 책정 계층에 관계 없이 앱에 사용 가능한 _모든_ 아웃바운드 IP 주소를 찾으려면 앱의 왼쪽 탐색에서 **속성** 을 클릭합니다. 이러한 필드는 **추가 아웃바운드 IP 주소** 필드에 나열됩니다.

[Cloud Shell](../cloud-shell/quickstart.md)에서 다음 명령을 실행하면 동일한 정보를 찾을 수 있습니다.

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="get-a-static-outbound-ip"></a>고정 아웃바운드 IP 가져오기
가상 네트워크 NAT 게이트웨이와 함께 지역 VNet 통합을 사용하여 고정 공용 IP 주소를 통해 트래픽을 전달함으로써 앱에서 아웃바운드 트래픽의 IP 주소를 제어할 수 있습니다. [지역 VNet 통합](/azure/app-service/web-sites-integrate-with-vnet)은 **표준**, **프리미엄**, **프리미엄V2**, **프리미엄V3** App Service 요금제에서 사용할 수 있습니다. 이 설정에 관한 자세한 내용은 [NAT 게이트웨이 통합](./networking/nat-gateway-integration.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

원본 IP 주소에서 인바운드 트래픽을 제한하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [고정 IP 제한](app-service-ip-restrictions.md)
