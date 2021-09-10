---
title: StartupServices.xml에서 Service Fabric 애플리케이션에 대한 서비스 구성 정의
description: StartupServices.xml을 사용하여 ApplicationManifest.xml에서 서비스 수준 구성을 분리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 69ec795bab910f8f2b030ab5758698d3fdbae824
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681380"
---
# <a name="introducing-startupservicesxml-in-service-fabric-application"></a>Service Fabric 애플리케이션의 StartupServices.xml 도입
이 기능은 Service Fabric 애플리케이션 디자인에 StartupServices.xml 파일을 도입합니다. 이 파일은 ApplicationManifest.xml의 DefaultServices 섹션을 호스트합니다. 이 구현을 사용하면 DefaultServices 및 서비스 정의 관련 매개 변수가 기존 ApplicationManifest.xml에서 StartupServices.xml이라는 새 파일로 이동됩니다. 이 파일은 Visual Studio에서 각 기능(Build/Rebuild/F5/Ctrl+F5/Publish)에 사용됩니다.

참고 - StartupServices.xml은 Visual Studio 배포에만 사용할 수 있으며, Visual Studio와 함께 배포된 패키지(StartupServices.xml 포함)가 ARM 배포 서비스와 충돌하지 않게 하기 위한 것입니다. StartupServices.xml은 애플리케이션 패키지의 일부로 패키지되지 않습니다. DevOps 파이프라인에서 지원되지 않으며, 고객은 ARM을 통하거나 원하는 구성의 cmdlet을 통해 애플리케이션에 개별 서비스를 배포해야 합니다.

## <a name="existing-service-fabric-application-design"></a>기존 Service Fabric 애플리케이션 디자인
각 Service Fabric 애플리케이션에 대해 ApplicationManifest.xml은 애플리케이션에 대한 모든 서비스 관련 정보의 원본입니다. ApplicationManifest.xml은 모든 매개 변수, ServiceManifestImport, DefaultServices로 구성됩니다. 구성 매개 변수는 ApplicationParameters의 Cloud.xml/Local1Node.xml/Local5Node.xml 파일에 기재되어 있습니다.

애플리케이션에 새 서비스가 추가되면 이 새 서비스 매개 변수의 ServiceManifestImport 및 DefaultServices 섹션이 ApplicationManifest.xml 내에 추가됩니다. 구성 매개 변수는 ApplicationParameters의 Cloud.xml/Local1Node.xml/Local5Node.xml 파일에 추가됩니다.

사용자가 Visual Studio에서 Build/Rebuild 함수를 클릭하면 ApplicationManifest.xml에서 ServiceManifestImport, Parameters, DefaultServices 섹션이 수정됩니다. 구성 매개 변수는 ApplicationParameters 아래의 Cloud.xml/Local1Node.xml/Local5Node.xml 파일에서도 편집됩니다.

사용자가 F5/Ctrl+F5/Publish를 트리거하면 애플리케이션 및 서비스가 ApplictionManifest.xml의 정보에 따라 배포 또는 게시됩니다.  구성 매개 변수는 ApplicationParameters 아래의 Cloud.xml/Local1Node.xml/Local5Node.xml 파일에서 사용됩니다.

![Service Fabric 애플리케이션에 대한 기존 디자인][exisiting-design-diagram]

샘플 ApplicationManifest.xml 

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
    <Parameter Name="Web1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Web1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Web1Type" TargetReplicaSetSize="[Web1_TargetReplicaSetSize]" MinReplicaSetSize="[Web1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Web1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="new-service-fabric-application-design-with-startupservicesxml"></a>StartupServices.xml을 사용하는 새로운 Service Fabric 애플리케이션 디자인
이 디자인에서는 서비스 수준 정보(예: 서비스 정의 및 서비스 매개 변수)와 애플리케이션 수준 정보(ServiceManifestImport 및 ApplicationParameters)가 명확하게 구분됩니다. StartupServices.xml은 모든 서비스 수준 정보를 포함하는 반면, ApplicationManifest.xml은 모든 애플리케이션 수준 정보를 포함합니다. 도입되는 또 다른 변경 사항은 서비스 매개 변수에 대한 구성만 있는 StartupServiceParameters 아래에 /Local1Node.xml/Local5Node.xml Cloud.xml을 추가하는 것입니다. ApplicationParameters 아래의 기존 Cloud.xml/Local1Node.xml/Local5Node.xml은 애플리케이션 수준 매개 변수 구성만 포함합니다.

애플리케이션에 새 서비스가 추가되면 애플리케이션 수준 매개 변수 및 ServiceManifestImport가 ApplicationManifest.xml에 추가됩니다. 애플리케이션 매개 변수에 대한 구성은 ApplicationParameters 아래의 Cloud.xml/Local1Node.xml/Local5Node.xml 파일에 추가됩니다. 서비스 정보 및 서비스 매개 변수는 StartupServices.xml에 추가되고 서비스 매개 변수에 대한 구성은 StartupServiceParameters 아래의 Cloud.xml/Local1Node.xml/Local5Node.xml에 추가됩니다.

프로젝트의 Build/Rebuild를 진행하는 동안 ServiceManifestImport를 수정하면 ApplicationManifest.xml에서 애플리케이션 매개 변수가 발생합니다. 애플리케이션 매개 변수의 구성은 ApplicationParameters 아래의 Cloud.xml/Local1Node.xml/Local5Node.xml 파일에서도 편집됩니다. 서비스 관련 정보는 StartupServices.xml에서 편집되고 서비스 매개 변수는 StartupServiceParameters 아래의 Cloud.xml/Local1Node.xml/Local5Node.xml에서 편집됩니다.

Visual Studio에서 F5/Ctrl+F5/Publish가 트리거되면 ApplictionManifest.xml 및 ApplicationParameters 아래의 Cloud.xml/Local1Node.xml/Local5Node.xml 파일의 애플리케이션 매개 변수의 정보에 따라 애플리케이션이 배포/게시됩니다. 각 서비스는 StartupServices.xml 및 StartupServiceParameters 아래의 Cloud.xml/Local1Node.xml/Local5Node.xml 파일의 서비스 매개 변수 구성의 서비스 정보를 사용하여 개별적으로 시작됩니다.

![StartupServices.xml을 사용하는 Service Fabric 애플리케이션용 새 디자인][new-design-diagram]

이러한 서비스 매개 변수 및 애플리케이션 매개 변수는 그림과 같이 애플리케이션을 게시(마우스 오른쪽 클릭->게시)하기 전에 편집할 수 있습니다.

![새 디자인에서 게시하기 옵션][publish-application]

새 디자인의 샘플 ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
</ApplicationManifest>
```

샘플 StartupServices.xml 파일
```xml
<?xml version="1.0" encoding="utf-8"?>
<StartupServicesManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                         xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <Services>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </Services>
</StartupServicesManifest>
```

startupServices.xml 기능은 SDK 버전 5.0.516.9590 이상에서 모든 새 프로젝트에 대해 사용 가능합니다. 작업자 서비스의 경우, Microsoft.ServiceFabric.Actors NuGet 버전 5.0.516 이상에서 사용 가능합니다. 이전 버전의 SDK를 사용하여 만든 프로젝트는 최신 SDK와 완전히 호환됩니다. 이전 프로젝트를 새 디자인으로 마이그레이션하는 것은 지원되지 않습니다. 사용자가 최신 버전의 SDK에서 StartupServices.xml 없이 Service Fabric 애플리케이션을 생성하려는 경우, 아래 그림에 표시된 것처럼 “프로젝트 템플릿 선택 도움말” 링크를 클릭해야 합니다.

![새 디자인에서 새 애플리케이션 만들기 옵션][create-new-project]


## <a name="next-steps"></a>다음 단계
- [Service Fabric 애플리케이션 모델](service-fabric-application-model.md)에 대해 알아봅니다.
- [Service Fabric 애플리케이션 및 서비스 매니페스트 ](service-fabric-application-and-service-manifests.md)에 대해 알아봅니다.

<!--Image references-->
[exisiting-design-diagram]: ./media/service-fabric-startupservices/existing-design.png
[new-design-diagram]: ./media/service-fabric-startupservices/new-design.png
[publish-application]: ./media/service-fabric-startupservices/publish-application.png
[create-new-project]: ./media/service-fabric-startupservices/create-new-project.png

