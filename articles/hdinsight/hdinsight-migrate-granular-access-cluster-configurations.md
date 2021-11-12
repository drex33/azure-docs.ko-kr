---
title: 세부적인 역할 기반 액세스 Azure HDInsight 클러스터 구성
description: HDInsight 클러스터 구성에 대한 세분화된 역할 기반 액세스 로 마이그레이션의 일부에 필요한 변경 사항에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: cfaaf73751e188f88d06e6a12fc7e3e7613c7f81
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278211"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>클러스터 구성에 대한 세밀한 역할 기반 액세스로 마이그레이션

중요한 정보를 얻을 수 있도록 보다 세분화된 역할 기반 액세스를 지원하기 위해 몇 가지 중요한 변경 사항이 도입되었습니다. 이러한 변경 사항의 일부로 [영향을 받는 엔터티/시나리오](#am-i-affected-by-these-changes) 중 하나를 사용하는 경우 **2019년 9월 3일부터** 일부 작업이 필요할 수 있습니다.

## <a name="what-is-changing"></a>변경되는 내용

이전에는 비밀을 `*/read` 권한이 있는 사람이면 누구나 사용할 수 있었기 때문에 소유자, 기여자 또는 읽기 권한자 [Azure 역할](../role-based-access-control/rbac-and-directory-admin-roles.md)을 가지고 있는 클러스터 사용자가 HDInsight API를 통해 비밀을 얻을 수 있었습니다. 비밀은 사용자의 역할에서 허용해야 하는 것보다 높은 수준의 액세스를 얻는 데 사용할 수 있는 값으로 정의됩니다. 여기에는 클러스터 게이트웨이 HTTP 자격 증명, 스토리지 계정 키, 데이터베이스 자격 증명 등의 값이 포함됩니다.

2019년 9월 3일부터 이러한 비밀에 액세스하려면 `Microsoft.HDInsight/clusters/configurations/action` 권한이 필요합니다. 즉, 읽기 권한자 역할의 사용자는 사용하여 더 이상 액세스할 수 없습니다. 이 권한이 있는 역할은 기여자, 소유자 및 새 HDInsight 클러스터 운영자 역할(아래 참조)입니다.

또한 기여자 또는 소유자의 관리 권한을 부여하지 않고도 암호를 검색할 수 있는 새 [HDInsight 클러스터 운영자](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) 역할을 도입하고 있습니다. 요약:

| 역할                                  | 이전의                                                                                        | 앞으로 이동       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 판독기                                | - 비밀 포함 읽기 권한.                                                                   | - 비밀 **제외** 읽기 권한 | 
| HDInsight 클러스터 운영자<br>(새 역할) | 해당 없음                                                                                              | - 비밀 포함 읽기/쓰기 권한         | 
| 참가자                           | - 비밀 포함 읽기/쓰기 권한.<br>- 모든 종류의 Azure 리소스를 만들고 관리.<br>- 스크립트 작업 실행.     | 변경 내용 없음 |
| 소유자                                 | - 비밀 포함 읽기/쓰기 권한.<br>- 모든 리소스에 대한 전체 액세스 권한.<br>- 다른 사람에게 액세스 권한을 위임 가능.<br>- 스크립트 작업 실행. | 변경 내용 없음 |

사용자에게 클러스터 암호에 대한 읽기/쓰기 액세스 권한을 부여하는 HDInsight 클러스터 운영자 역할 할당을 추가하는 방법에 대한 자세한 내용은 아래 섹션에서 [사용자에게 Hdinsight 클러스터 운영자 역할 할당 추가](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)를 참조하세요.

## <a name="am-i-affected-by-these-changes"></a>나도 변경의 영향을 받나요?

영향을 받는 엔터티 및 시나리오는 다음과 같습니다.

- [API](#api): `/configurations`또는`/configurations/{configurationName}` 엔드포인트를 사용하는 사용자입니다.
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 버전 1.1.1 이하.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) 버전 3.20.0 이하.
- [Azure Data Lake 및 Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) 2.3.9000.1 버전 이하.
- '[Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) 버전 3.15.0 이하.
- [.NET용 SDK](#sdk-for-net)
    - [버전 1.x 또는 2.x](#versions-1x-and-2x): ConfigurationsOperationsExtensions 클래스에서 `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` 또는 `DisableHttp` 메서드를 사용하는 사용자.
    - [버전 3.x 이상](#versions-3x-and-up): `ConfigurationsOperationsExtensions` 클래스에서 `Get`, `Update`, `EnableHttp` 또는 `DisableHttp` 메서드를 사용하는 사용자.
- [Python용 SDK](#sdk-for-python): `ConfigurationsOperations` 클래스의 `get` 또는 `update` 메서드를 사용하는 사용자.
- [Java용 SDK](#sdk-for-java): `ConfigurationsInner` 클래스의 `update` 또는 `get` 메서드를 사용하는 사용자.
- [Go용 SDK](#sdk-for-go): `ConfigurationsClient` 구조체의 `Get` 또는 `Update` 메서드를 사용하는 사용자.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) 2.0.0 미만.
시나리오에 대한 마이그레이션 단계를 보려면 아래 섹션을 참조하거나 위의 링크를 사용하십시오.

### <a name="api"></a>API

다음 API가 변경되거나 더 이상 사용되지 않습니다.

- [**GET /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration)(중요한 정보 제거)
    - 이전에는 개별 구성 유형(암호 포함)을 가져오는 데 사용되었습니다.
    - 2019년 9월 3일부터 이 API 호출은 암호가 생략된 개별 구성 유형을 반환합니다. 비밀을 비롯한 모든 구성을 얻으려면 새 POST /configurations 호출을 사용합니다. 게이트웨이 설정만 가져오려면 새 POST /getGatewaySettings 호출을 사용합니다.
- [**GET /configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration)(사용되지 않음)
    - 이전에 모든 구성(암호 포함)을 가져오는 데 사용
    - 2019년 9월 3일부터 이 API 호출은 사용되지 않으며 더 이상 지원되지 않습니다. 앞으로 진행되는 구성을 가져오려면 새 POST /configurations 호출을 사용합니다. 중요한 매개 변수가 생략된 구성을 얻으려면 GET /configurations/{configurationName} 호출을 사용합니다.
- [**POST /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)(사용되지 않음)
    - 이전에 게이트웨이 자격 증명을 업데이트하는 데 사용되었습니다.
    - 2019년 9월 3일부터 이 API 호출은 사용되지 않으며 더 이상 지원되지 않습니다. 대신 새 POST /updateGatewaySettings를 사용합니다.

다음과 같은 대체 API가 추가되었습니다.</span>

- [**POST /configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 이 API를 사용하여 암호를 포함한 모든 구성을 가져옵니다.
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 이 API를 사용하여 게이트웨이 설정을 가져옵니다.
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 이 API를 사용하여 게이트웨이 설정(사용자 이름 및/또는 암호)을 업데이트합니다.

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

버전 1.1.1 이하를 사용하는 경우 중단을 방지하려면 [최신 버전의 Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)로 업데이트하세요.

### <a name="azure-toolkit-for-intellij"></a>IntelliJ용 Azure 도구 키트

버전 3.20.0를 사용하는 경우 중단을 방지하려면 [최신 버전의 Azure Toolkit for IntelliJ 플러그 인](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)으로 업데이트합니다.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Microsoft Azure Data Lake 및 Stream Analytics Tools for Visual Studio

중단을 방지하기 위해 2.3.9000.1 이상의 [Azure Data Lake 및 Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview)로 업데이트합니다.  업데이트에 대한 도움말은 [Data Lake Tools for Visual Studio 업데이트](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio)설명서를 참조하세요.

### <a name="azure-toolkit-for-eclipse"></a>Eclipse용 Azure 도구 키트

버전 3.15.0 이하를 사용하는 경우 중단을 방지하기 위해 [최신 버전의 Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse)로 업데이트합니다.

### <a name="sdk-for-net"></a>.NET용 SDK

#### <a name="versions-1x-and-2x"></a>버전 1.x 및 2.x

.NET용 HDInsight SDK의 [2.1.0 버전](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0)으로 업데이트. 이러한 변경의 영향을 받는 메서드를 사용하는 경우 최소한의 코드를 수정해야 할 수 있습니다.

- `ClusterOperationsExtensions.GetClusterConfigurations`는 **더 이상 스토리지 키(핵심 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 중요한 매개 변수를 반환하지 않습니다**.
    - 중요한 매개 변수를 포함한 모든 구성을 검색하려면 앞으로 `ClusterOperationsExtensions.ListConfigurations`를 사용합니다.  '읽기 권한자' 역할이 있는 사용자는 이 메서드를 사용할 수 없습니다. 이렇게 하면 클러스터에 대해 중요한 정보에 액세스할 수 있는 사용자를 세부적으로 제어할 수 있습니다.
    - HTTP 게이트웨이 자격 증명만 검색하려면 `ClusterOperationsExtensions.GetGatewaySettings`를 사용합니다.

- `ClusterOperationsExtensions.GetConnectivitySettings`는 이제 사용되지 않으며 `ClusterOperationsExtensions.GetGatewaySettings`로 대체되었습니다.

- `ClusterOperationsExtensions.ConfigureHttpSettings`는 이제 사용되지 않으며 `ClusterOperationsExtensions.UpdateGatewaySettings`로 대체되었습니다.

- `ConfigurationsOperationsExtensions.EnableHttp` 및 `DisableHttp`는 이제 사용되지 않습니다. 이제 HTTP는 항상 사용하도록 설정되므로 이러한 메서드는 더 이상 필요하지 않습니다.

#### <a name="versions-3x-and-up"></a>버전 3.x 이상

.NET용 HDInsight SDK의 [5.0.0 버전](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) 이상으로 업데이트합니다. 이러한 변경의 영향을 받는 메서드를 사용하는 경우 최소한의 코드를 수정해야 할 수 있습니다.

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get)은 **더 이상 스토리지 키(핵심 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 중요한 매개 변수를 반환하지 않습니다**.
    - 중요한 매개 변수를 포함한 모든 구성을 검색하려면 앞으로 [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list)를 사용합니다.  '읽기 권한자' 역할이 있는 사용자는 이 메서드를 사용할 수 없습니다. 이렇게 하면 클러스터에 대해 중요한 정보에 액세스할 수 있는 사용자를 세부적으로 제어할 수 있습니다. 
    - HTTP 게이트웨이 자격 증명만 검색하려면 [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings)를 사용합니다. 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update)는 이제 사용되지 않으며 [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings)로 대체되었습니다. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) 및 [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp)는 이제 사용되지 않습니다. 이제 HTTP는 항상 사용하도록 설정되므로 이러한 메서드는 더 이상 필요하지 않습니다.

### <a name="sdk-for-python"></a>Python용 SDK

Python용 HDInsight SDK의 [버전 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) 이상으로 업데이트합니다. 이러한 변경의 영향을 받는 메서드를 사용하는 경우 최소한의 코드를 수정해야 할 수 있습니다.

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)은 **더 이상 스토리지 키(핵심 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 중요한 매개 변수를 반환하지 않습니다**.
    - 중요한 매개 변수를 포함한 모든 구성을 검색하려면 앞으로 [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)를 사용합니다.  '읽기 권한자' 역할이 있는 사용자는 이 메서드를 사용할 수 없습니다. 이렇게 하면 클러스터에 대해 중요한 정보에 액세스할 수 있는 사용자를 세부적으로 제어할 수 있습니다. 
    - HTTP 게이트웨이 자격 증명만 검색하려면 [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)를 사용합니다.
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)는 이제 사용되지 않으며 [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)로 대체되었습니다.

### <a name="sdk-for-java"></a>Java용 SDK

Java용 HDInsight SDK의 [1.0.0 버전](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) 이상으로 업데이트합니다. 이러한 변경의 영향을 받는 메서드를 사용하는 경우 최소한의 코드를 수정해야 할 수 있습니다.

- `ConfigurationsInner.get`은 **더 이상 스토리지 키(핵심 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 중요한 매개 변수를 반환하지 않습니다**.
- `ConfigurationsInner.update`는 이제 사용되지 않습니다.

### <a name="sdk-for-go"></a>Go용 SDK

Go용 HDInsight SDK [버전 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) 이상으로 업데이트합니다. 이러한 변경의 영향을 받는 메서드를 사용하는 경우 최소한의 코드를 수정해야 할 수 있습니다.

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get)은 **더 이상 스토리지 키(핵심 사이트) 또는 HTTP 자격 증명(게이트웨이)과 같은 중요한 매개 변수를 반환하지 않습니다**.
    - 중요한 매개 변수를 포함한 모든 구성을 검색하려면 앞으로 [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List)를 사용합니다.  '읽기 권한자' 역할이 있는 사용자는 이 메서드를 사용할 수 없습니다. 이렇게 하면 클러스터에 대해 중요한 정보에 액세스할 수 있는 사용자를 세부적으로 제어할 수 있습니다. 
    - HTTP 게이트웨이 자격 증명만 검색하려면 [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings)를 사용합니다.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update)는 이제 사용되지 않으며 [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)로 대체되었습니다.

### <a name="azhdinsight-powershell"></a>Azure HDInsight PowerShell
중단을 방지하려면 [Az PowerShell version 2.0.0](https://www.powershellgallery.com/packages/Az) 이상으로 업데이트합니다.  이러한 변경의 영향을 받는 메서드를 사용하는 경우 최소한의 코드를 수정해야 할 수 있습니다.
- `Grant-AzHDInsightHttpServicesAccess`는 이제 사용되지 않으며 새로운 `Set-AzHDInsightGatewayCredential` cmdlet으로 대체되었습니다.
- 스토리지 키에 대한 세분화된 역할 기반 액세스를 지원하도록 `Get-AzHDInsightJobOutput`을 업데이트했습니다.
    - HDInsight 클러스터 운영자, 기여자 또는 소유자 역할이 있는 사용자는 영향을 받지 않습니다.
    - 읽기 권한자 역할만 있는 사용자는 `DefaultStorageAccountKey` 매개 변수를 명시적으로 지정해야 합니다.
- `Revoke-AzHDInsightHttpServicesAccess`는 이제 사용되지 않습니다. 이제 HTTP는 항상 사용하도록 설정되므로 이 cmdlet은 더 이상 필요하지 않습니다.
 자세한 내용은 [az.HDInsight 마이그레이션 가이드](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)를 참조하세요.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>사용자에게 HDInsight 클러스터 운영자 역할 할당 추가

[소유자](../role-based-access-control/built-in-roles.md#owner) 역할을 가진 사용자는 중요한 HDInsight 클러스터 구성값(예: 클러스터 게이트웨이 자격 증명 및 스토리지 계정 키)에 대한 읽기/쓰기 권한을 부여하려는 사용자에게 [HDInsight 클러스터 운영자](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) 역할을 할당할 수 있습니다.

### <a name="using-the-azure-cli"></a>Azure CLI 사용

이 역할 할당을 추가하는 가장 간단한 방법은 Azure CLI의 `az role assignment create` 명령을 사용하는 것입니다.

> [!NOTE]
> 소유자 역할을 가진 사용자만 권한을 부여할 수 있으므로 이러한 사용자만 명령을 실행해야 합니다. `--assignee`는 HDInsight 클러스터 운영자 역할을 할당하려는 사용자의 서비스 주체 또는 이메일 주소의 이름입니다. 권한 부족 오류가 표시되면 아래 FAQ를 참조하세요.

#### <a name="grant-role-at-the-resource-cluster-level"></a>리소스(클러스터) 수준에서 역할 부여

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>리소스 그룹 수준에서 역할 부여

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>구독 수준에서 역할 부여

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Azure Portal 사용

또는 Azure Portal를 사용하여 사용자에게 HDInsight 클러스터 운영자 역할 할당을 추가할 수 있습니다. [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md) 문서를 참조하세요.

## <a name="faq"></a>FAQ

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>API 요청 및/또는 도구를 업데이트한 후 403(사용할 수 없음) 응답이 표시되는 이유는 무엇인가요?

클러스터 구성은 이제 세분화된 역할 기반 액세스 제어를 넘어섰으며 액세스하려면 `Microsoft.HDInsight/clusters/configurations/*` 권한이 필요합니다. 이 권한을 얻으려면 구성에 액세스 하려는 사용자 또는 서비스 주체에게 HDInsight 클러스터 운영자, 기여자 또는 소유자 역할을 할당합니다.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Azure CLI 명령을 실행하여 다른 사용자나 서비스 주체에 HDInsight 클러스터 운영자 역할을 할당하는 경우 "권한이 부족하여 작업을 완료할 수 없습니다" 메시지가 표시되는 이유는 무엇인가요?

사용자나 서비스 주체가 이 명령을 실행하려면 소유자 역할을 보유하는 것 외에도 담당자의 개체 ID를 조회할 Azure AD 권한이 있어야 합니다. 이 메시지는 Azure AD 권한이 부족함을 나타냅니다. `-–assignee`인수를 `–assignee-object-id`로 바꾸고 담당자의 개체 ID를 이름 대신 매개 변수로 제공합니다(또는 관리 ID의 경우 보안 주체 ID). 자세한 정보는 [az role assignment create 설명서](/cli/azure/role/assignment#az_role_assignment_create)의 선택적 매개 변수 섹션을 참조하세요.

그래도 문제가 해결되지 않으면 Azure AD 관리자에게 문의하여 올바른 권한을 획득합니다.

### <a name="what-will-happen-if-i-take-no-action"></a>조치를 취하지 않으면 어떻게 되나요?

2019년 9월 3일부터 `GET /configurations` 및 `POST /configurations/gateway` 호출은 더 이상 정보를 반환하지 않으며 `GET /configurations/{configurationName}` 호출은 스토리지 계정 키 또는 클러스터 암호와 같이 중요한 매개 변수를 반환하지 않습니다. 해당 SDK 메서드 및 PowerShell cmdlet의 경우에도 마찬가지입니다.

이전 버전의 도구 중 하나를 사용하는 경우 위에서 언급한 것처럼 이전 버전의 도구를 사용하는 경우를 업데이트할 때까지 더 이상 작동하지 않습니다.

자세한 내용은 시나리오에 대한 이 문서의 해당 섹션을 참조하세요.
