---
title: Azure Kubernetes Service에서 지원되는 Kubernetes 버전
description: AKS(Azure Kubernetes Service)의 Kubernetes 버전 지원 정책 및 클러스터 수명 주기에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.author: jpalma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3c094a40cd0150fe4134bf1352dfbe418346d63b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577710"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 지원되는 Kubernetes 버전

Kubernetes 커뮤니티에서는 대략 3개월마다 부 버전을 릴리스합니다. 최근에 Kubernetes 커뮤니티에서 1.19 버전부터 [각 버전에 대한 지원 기간을 9개월에서 12개월로 늘렸습니다](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/).

부 버전 릴리스에는 새로운 기능과 향상된 기능이 포함됩니다. 패치는 더 자주 릴리스되며(매주 릴리스되기도 함), 부 버전의 중요한 버그 수정용으로만 제공됩니다. 패치 릴리스에는 보안 취약성 또는 주요 버그에 대한 수정이 포함됩니다.

## <a name="kubernetes-versions"></a>Kubernetes 버전

Kubernetes는 표준 [유의적 버전](https://semver.org/) 버전 관리 체계를 각 버전에 사용합니다.

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

버전의 각 번호는 이전 버전과의 일반적인 호환성을 나타냅니다.

* **주 버전** 은 호환되지 않는 API 업데이트 또는 이전 버전과의 호환성이 손상될 수 있으면 변경됩니다.
* **부 버전** 은 다른 부 릴리스의 이전 버전과 호환되는 기능 업데이트가 수행되면 변경됩니다.
* **패치 버전** 은 이전 버전과 호환되는 버그 수정이 수행되면 변경됩니다.

실행하는 부 버전의 최신 패치 릴리스를 실행하는 것을 목표로 합니다. 예를 들어 프로덕션 클러스터는 **`1.17.7`** 에 있습니다. **`1.17.8`** 은 *1.17* 시리즈에 사용할 수 있는 최신 패치 버전입니다. 클러스터가 완전히 패치되고 지원되도록 최대한 빨리 **`1.17.8`** 로 업그레이드해야 합니다.

## <a name="kubernetes-version-support-policy"></a>Kubernetes 버전 지원 정책

AKS는 일반 공급 버전을 모든 SLO 또는 SLA 측정에서 사용하도록 설정되고 모든 지역에서 사용할 수 있는 버전으로 정의합니다. AKS에서 지원하는 Kubernetes의 3개 GA 부 버전은 다음과 같습니다.

* AKS에서 릴리스된 최신 GA 부 버전입니다(N이라고 함).
* 이전 부 버전 2개.
    * 지원되는 각 부 버전은 안정적인 패치를 최대 2개 지원합니다.

또한 AKS는 [미리 보기 사용 약관][preview-terms]에 따라 명시적으로 레이블이 지정된 미리 보기 버전을 지원할 수도 있습니다.

> [!NOTE]
> AKS는 점진적 지역 배포를 포함하는 안전한 배포 방법을 사용합니다. 즉, 모든 지역에서 새 릴리스 또는 새 버전을 사용할 수 있기까지 영업일 기준 최대 10일이 소요될 수 있습니다.

AKS에서 Kubernetes 버전의 지원 기간은 “N-2(N(최신 릴리스) - 2(부 버전))”로 알려져 있습니다.

예를 들어 AKS가 현재 *1.17.a* 를 도입하는 경우 다음 버전에 대한 지원이 제공됩니다.

새 부 버전    |    지원되는 버전 목록
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

여기서 “.문자”는 패치 버전을 나타냅니다.

새로운 부 버전이 도입되면 지원되는 가장 오래된 부 버전 및 패치 릴리스는 사용 중지되고 제거됩니다. 예를 들어 현재 지원되는 버전 목록은 다음과 같습니다.

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS에서 1.18.\*를 릴리스하여 모든 1.15.\* 버전은 30일 내에 지원되지 않습니다.

> [!NOTE]
> 고객이 지원되지 않는 Kubernetes 버전을 실행할 때 클러스터에 대한 지원을 요청하면 업그레이드하라는 메시지가 표시됩니다. 지원되지 않는 Kubernetes 릴리스를 실행하는 클러스터는 [AKS 지원 정책](./support-policies.md)에 포함되지 않습니다.

위의 내용 외에 AKS는 지정된 부 버전의 최대 2개의 **패치** 릴리스를 지원합니다. 따라서 지원되는 버전은 다음과 같습니다.

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

AKS에서 `1.17.9` 및 `1.16.11`을 해제하는 경우 가장 오래된 패치 버전이 더 이상 사용되지 않고 제거되며 지원되는 버전 목록은 다음과 같이 됩니다.

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>지원되는 `kubectl` 버전

[kubectl에 대한 Kubernetes 지원 정책](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)에 따라 *kube-apiserver* 버전에 비례하여 이전 또는 최신 `kubectl`의 부 버전 하나를 사용할 수 있습니다.

예를 들어 *kube-apiserver* 가 *1.17* 에 있으면 해당 *kube-apiserver* 와 함께 `kubectl`의 *1.16* ~ *1.18* 버전을 사용할 수 있습니다.

`kubectl`을 설치하거나 최신 버전으로 업데이트하려면 다음을 실행합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az aks install-cli
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```powershell
Install-AzAksKubectl -Version latest
```
---

## <a name="release-and-deprecation-process"></a>릴리스 및 사용 중단 프로세스

[AKS Kubernetes 릴리스 달력](#aks-kubernetes-release-calendar)에서 예정된 버전 릴리스 및 사용 중단을 참조할 수 있습니다.

새 Kubernetes **부** 버전의 경우
  * AKS는 [AKS 릴리스 정보](https://aka.ms/aks/releasenotes)에 새 버전 릴리스의 계획된 날짜와 함께 각 이전 버전의 사용 중단에 대한 사전 공지를 제거하기 전 최소 30일 전에 게시합니다.
  * 새 버전에서 더 이상 사용되지 않는 API로 인해 클러스터 문제가 발생하는 경우 AKS는 [Azure Advisor](../advisor/advisor-overview.md)를 사용하여 사용자에게 경고합니다. Azure Advisor는 현재 지원되지 않는 경우 사용자에게 경고하는 데도 사용됩니다.
  * AKS는 AKS 및 포털 액세스 권한이 있는 모든 사용자가 사용할 수 있는 [서비스 상태 알림](../service-health/service-health-overview.md)을 게시하고 구독 관리자에게 예정된 버전 제거 날짜가 포함된 이메일을 보냅니다.

    > [!NOTE]
    > 구독 관리자 또는 변경하려는 사용자를 확인하려면 [Azure 구독 관리](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)를 참조하세요.

  * 사용자는 버전 제거 후 **30일 동안** 지원되는 부 버전 릴리스로 업그레이드하여 지원을 계속 받을 수 있습니다.

새 Kubernetes **패치** 버전의 경우
  * 이러한 버전은 패치 버전의 긴급한 특성으로 인해 사용 가능한 대로 서비스에 도입될 수 있습니다.
  * 일반적으로 AKS는 새 패치 버전의 릴리스를 광범위하게 전달하지 않습니다. 그러나 AKS는 적시에 AKS에서 지원하기 위해 사용 가능한 CVE 패치를 지속적으로 모니터링하고 유효성을 검사합니다. 중요한 패치가 있거나 사용자 작업이 필요한 경우 AKS는 새로 사용 가능한 패치로 업그레이드하도록 사용자에게 알립니다.
  * 사용자는 AKS에서 패치 릴리스가 제거된 후 **30일 내에** 지원되는 패치로 업그레이드하고 지원을 계속 받을 수 있습니다.

### <a name="supported-versions-policy-exceptions"></a>지원되는 버전 정책 예외

AKS에는 사전에 알리지 않고 프로덕션에 영향을 주는 하나 이상의 중요한 버그 또는 보안 문제가 있는 신규/기존 버전을 추가하거나 제거할 수 있는 권한이 있습니다.

버그 또는 보안 문제의 심각도에 따라 특정 패치 릴리스를 건너뛰거나 롤아웃을 가속화할 수 있습니다.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal 및 CLI 버전

포털, Azure CLI 또는 Azure PowerShell을 사용하여 AKS 클러스터를 배포하는 경우 클러스터는 기본적으로 N-1 부 버전 및 최신 패치로 설정됩니다. 예를 들어 AKS에서 *1.17.a*, *1.17.b*, *1.16.c*, *1.16.d*, *1.15.e* 및 *1.15.f* 를 지원하는 경우 선택된 기본 버전은 *1.16.c* 입니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

사용자의 구독과 지역에 현재 제공되는 버전을 알아보려면 [az aks get-versions][az-aks-get-versions] 명령을 사용합니다. 다음 예제에는 *EastUS* 지역에 제공되는 Kubernetes 버전이 나열되어 있습니다.

```azurecli-interactive
az aks get-versions --location eastus --output table
```


### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

사용자의 구독과 지역에 현재 제공되는 버전을 알아보려면 [Get-AzAksVersion][get-azaksversion] cmdlet을 사용합니다. 다음 예제에는 *EastUS* 지역에 제공되는 Kubernetes 버전이 나열되어 있습니다.

```azurepowershell-interactive
Get-AzAksVersion -Location eastus
```

---

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes 릴리스 달력

이전 릴리스 기록은 [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History)를 참조하세요.

|  K8s 버전 | 업스트림 릴리스  | AKS 미리 보기  | AKS GA  | 수명 종료 |
|--------------|-------------------|--------------|---------|-------------|
| 1.19  | 2020년 8월 4일  | 2020년 9월   | 2020년 11월  | 1.22 GA |
| 1.20  | 2020년 12월 8일  | 2021년 1월   | 2021년 3월  | 1.23 GA |
| 1.21  | 2021년 4월 8일 | 2021년 5월   | 2021년 7월  | 1.24 GA |
| 1.22  | 2021년 8월 4일 | 2021년 9월   | 11 월 2021  | 1.25 GA |
| 1.23  | 2021년 12월 | 2022년 1월   | 2022년 2월  | 1.26 GA |

## <a name="faq"></a>FAQ

**Microsoft에서 새 Kubernetes 버전을 알리는 방법은 무엇인가요?**

AKS 팀에서 지원이 중단되는 클러스터를 소유한 구독 관리자에게 보내는 이메일뿐만 아니라 설명서, [GitHub](https://github.com/Azure/AKS/releases)에도 새 Kubernetes 버전의 계획된 날짜가 포함된 사전 공지를 게시합니다.  알림 외에도 AKS를 [Azure Advisor](../advisor/advisor-overview.md) 사용 하 여 사용자가 지원 되지 않는 경우 사용자에 게 알리거나 응용 프로그램 또는 개발 프로세스에 영향을 줄 수 있는 사용 되지 않는 api에 대 한 경고를 사용자에 Azure Portal 게 알릴 수 있습니다.

**계속 지원받으려면 Kubernetes 버전을 얼마나 자주 업그레이드해야 하나요?**

Kubernetes 1.19부터 [오픈 소스 커뮤니티의 지원이 1년으로 확장되었습니다](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS는 전적으로 업스트림 약정과 일치하는 패치 및 지원을 사용하도록 설정합니다. 1\.19 이상의 AKS 클러스터의 경우 지원되는 버전을 유지하기 위해 1년에 한 번 이상 업그레이드할 수 있습니다.

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터를 업그레이드하면 어떻게 되나요?**

*n-3* 버전 이전인 경우에는 지원 범위에 해당되지 않음을 의미하며, 업그레이드하라는 메시지를 받게 됩니다. 버전 n-3에서 n-2로 업그레이드하면 지원 정책 내에 다시 포함됩니다. 예를 들면 다음과 같습니다.

- 가장 오래된 지원되는 AKS 버전이 *1.15.a* 이고 *1.14.b* 이전인 경우에는 지원 범위에 포함되지 않습니다.
- *1.14.b* 에서 *1.15.a* 이상으로 성공적으로 업그레이드하면 지원 정책 내에서 다시 지원됩니다.

다운그레이드는 지원되지 않습니다.

**‘지원에 포함되지 않음’는 무엇을 의미하나요?**

'지원되지 않음'은 다음을 의미합니다.
* 실행하는 버전이 지원되는 버전 목록에 없습니다.
* 버전 사용 중단 후 30일의 유예 기간 내에 있는 경우를 제외하고는 지원을 요청할 때 클러스터를 지원되는 버전으로 업그레이드하라는 메시지가 표시됩니다.

또한 AKS는 지원되는 버전 목록에 해당하지 않는 클러스터에 대한 런타임 또는 다른 보증을 수행하지 않습니다.

**고객이 지원되지 않는 부 버전을 사용하여 Kubernetes 클러스터의 크기를 조정하면 어떻게 되나요?**

AKS에서 지원하지 않는 부 버전의 경우 규모 축소 또는 규모 확대가 계속 작동해야 합니다. 서비스 품질이 보장되지 않으므로 클러스터를 다시 지원하도록 업그레이드하는 것이 좋습니다.

**사용자는 Kubernetes 버전을 계속 사용할 수 있나요?**

클러스터가 3개를 초과하는 부 버전에 대해 지원되지 않고 보안 위험이 발생하는 것으로 확인되면 Azure에서 클러스터를 업그레이드하도록 사용자에게 사전에 연락합니다. 추가 작업을 수행하지 않으면 Azure는 사용자를 대신하여 자동으로 클러스터를 업그레이드할 권리를 가집니다.

**노드 풀이 지원되는 AKS 버전 중 하나가 아닌 경우 컨트롤 플레인은 어떤 버전을 지원하나요?**

컨트롤 플레인은 모든 노드 풀의 버전 기간 내에 있어야 합니다. 컨트롤 플레인 또는 노드 풀의 업그레이드에 관한 자세한 내용은 [노드 풀 업그레이드](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)에 대한 설명서를 참조하세요.

**클러스터를 업그레이드하는 동안 여러 AKS 버전을 건너뛸 수 있나요?**

지원되는 AKS 클러스터를 업그레이드하는 경우 Kubernetes 부 버전은 건너뛸 수 없습니다. Kubernetes 제어 평면 [버전 기울이기 정책은](https://kubernetes.io/releases/version-skew-policy/) 부 버전 건너뛰기를 지원 하지 않습니다. 예를 들어 다음과 같이 업그레이드합니다.

  * *1.12.x* -> *1.13.x*: 허용
  * *1.13.x* -> *1.14.x*: 허용
  * *1.12.x* -> *1.14.x*: 허용되지 않음

*1.12.x* -> *1.14.x* 업그레이드의 경우 다음과 같습니다.
1. 먼저 *1.12.x* -> *1.13.x* 업그레이드
1. 다음으로 *1.13.x* -> *1.14.x* 업그레이드

지원 되지 않는 버전에서 지원 되는 최소 버전으로 다시 업그레이드 하는 경우에만 여러 버전을 건너뛸 수 있습니다. 예를 들어 *1.15* 이 지원 되는 최소 부 버전인 경우 지원 되지 않는 *1.10. x* 에서 지원 되는 *1.15* 로 업그레이드할 수 있습니다.

**30일 지원 기간 동안 새 1.xx.x 클러스터를 만들 수 있나요?**

아니요. 버전이 사용되지 않거나 제거된 후에는 해당 버전을 사용하여 클러스터를 만들 수 없습니다. 변경 내용이 롤아웃됨에 따라 버전 목록에서 제거된 이전 버전이 표시되기 시작합니다. 이 프로세스는 지역별 점진적으로 알림부터 2주 간 진행될 수 있습니다.

**새로 사용되지 않는 버전을 사용하고 있지만 새 노드 풀을 추가할 수 있나요? 아니면 업그레이드를 해야 하나요?**

아니요. 사용되지 않는 버전의 노드 풀을 클러스터에 추가할 수는 없습니다. 새 버전의 노드 풀은 추가할 수 있습니다. 그러나 이렇게 하려면 먼저 컨트롤 플레인을 업데이트해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

클러스터를 업그레이드하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az_aks_get_versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[get-azaksversion]: /powershell/module/az.aks/get-azaksversion
