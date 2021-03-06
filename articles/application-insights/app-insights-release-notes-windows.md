---
title: "Windows용 Application Insights에 대한 릴리스 정보"
description: "(더 이상 사용되지 않음) SDK 릴리스 정보"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 62115aba-f806-447b-9cca-76d0e11cfbd5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: joshweb
ROBOTS: NOINDEX,NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 8c5324742e42a1f82bb3031af4380fc5f0241d7f
ms.openlocfilehash: 6868024441e3e0e0d2fdc44499a9b1e00364b31e

---
# <a name="release-notes-for-application-insights-sdk-for-windows-phone-and-store"></a>Windows Phone 및 Store용 Application Insights SDK에 대한 릴리스 정보

> [!WARNING]
> Windows Phone 및 스토어 SDK는 더 이상 지원되지 않습니다. 모바일 및 데스크톱 앱의 배포 및 모니터링을 관리하려면 [HockeyApp](https://www.hockeyapp.net/)을 사용하는 것이 좋습니다.
>  

Application Insights SDK는 라이브 앱에 대한 원격 분석을 [Application Insights](https://azure.microsoft.com/services/application-insights/)에 보내며 여기서 사용량과 성능을 분석할 수 있습니다.

## <a name="version-111"></a>버전 1.1.1
### <a name="windows-sdk"></a>Windows SDK
* Windows Phone Silverlight SDK를 사용할 때 크래시 동안 중단을 수정합니다. 이렇게 변경하면 WindowsAppInitialier.InitializeAsync(...)에 대한 호출 후 ~2초 이후에 발생하는 크래시가 디스크에 보관되고 다음번에 앱을 시작할 때 전송됩니다. 크래시가 호출 후 ~2초 전에 발생하면 무시됩니다.  
* NuGet의 종속성을 Core 및 Microsoft.ApplicationInsights.PersistenceChannel(v1.2.3)의 특정 버전에 설정합니다.   

### <a name="core-sdk"></a>핵심 SDK
* Core는 github에서 관리됩니다. 핵심 SDK의 향후 릴리스 정보는 [github에서](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## <a name="version-11"></a>버전 1.1
### <a name="core-sdk"></a>핵심 SDK
* 이제 응용 프로그램의 종속성 호출에 대한 정보를 포함하는 새로운 원격 분석 유형 ```DependencyTelemetry``` 가 SDK에 도입되었습니다.
* 새 메서드 ```TelemetryClient.TrackDependency``` 는 응용 프로그램의 종속성 호출에 대한 정보를 보낼 수 있습니다.

## <a name="version-100"></a>Version 1.0.0
### <a name="windows-app-sdk"></a>Windows 앱 SDK
* Windows 앱을 새로 초기화  `InitializeAsync()` 메서드를 사용한 새로운 `WindowsAppInitializer` 클래스는 SDK 컬렉션의 초기화를 부트스트래핑을 허용합니다. 이 변경은 보다 정확한 제어 및 이전 ApplicationInsights.config 기법을 통해 중요한 앱 초기화 성능 향상을 가져옵니다. 
* DeveloperMode 더이상 자동으로 설정되지 않습니다. DeveloperMode 동작을 변경하려면 코드에서 지정 해야 합니다.
* NuGet 패키지는 더이상 ApplicationInsights.config를 삽입하지 않습니다. NuGet 패키지를 수동으로 추가하는 경우 새 WindowsAppInitializer를 사용하는 것을 권장합니다.
* ApplicationInsights.config는 `<InstrumentationKey>`에 대해 읽기 전용이며, WindowsAppInitializer 설정에 대한 선호도에서 다른 모든 설정은 무시됩니다.
* SDK는 Store Market을 자동 수집합니다.
* 많은 버그를 수정, 안정성 향상, 성능을 강화 시켰습니다.

### <a name="core-sdk"></a>핵심 SDK
* ApplicationInsights.config 파일은 더이상 필요하지 않습니다. 그리고  NuGet 패키지에 의해 추가되지 않습니다. 코드에서 구성을 전체 지정할 수 있습니다.
* NuGet 패키지를 더이상 솔루션에 대상 파일을 추가할 수 없습니다. 디버그 빌드 동안 Developer Mode의 자동 설정을 제거합니다. DeveloperMode는 코드에서 수동으로 설정해야 합니다.

## <a name="version-017"></a>버전 0.17
### <a name="windows-app-sdk"></a>Windows 앱 SDK
* Windows 앱 SDK는 이제 VS 2015 RC와 Windows 10 기술 미리 보기에 대해 만든 범용 Windows 앱을 지원 합니다.

### <a name="core-sdk"></a>핵심 SDK
* Telementry클라이언트는 InMemory Channel의 기본값입니다.
* 새로운 API `TelemetryClient.Flush()`가 추가되었습니다. 이 Flush 메서드는 해당 클라이언트에 기록하는 모든 원격 분석의 업로드 즉시 차단을 트리거합니다. 프로세스 종료 전에 업로드 수동 트리거를 가능하게 합니다.
* NuGet 패키지에는.Net 4.5 대상이 추가되었습니다. 이 대상은 (제거 BCL 및 EventSource 종속성) 외부 종속성이 없습니다.

## <a name="version-016"></a>버전 0.16
2015-04-28 미리 보기

* SDK는 이제 DNX 대상 플랫폼을 지원하므로 [.NET Core 프레임워크](http://www.dotnetfoundation.org/NETCore5) 응용 프로그램을 모니터링할 수 있습니다.
* ```TelemetryClient``` 의 인스턴스가 더 이상 계측 키를 캐시하지 않습니다. 이제 ```TelemetryClient```에서 계측 키를 명시적으로 설정하지 않으면 ```InstrumentationKey```에서 null을 반환합니다. 일부 원격 분석이 이미 수집된 후 ```TelemetryConfiguration.Active.InstrumentationKey``` 를 설정하면 종속성 수집기, 웹 요청 데이터 모음, 성능 카운터 수집기 같은 원격 분석 모듈에서 새로운 계측 키를 사용하므로 문제가 해결됩니다.

## <a name="version-015"></a>버전 0.15
* 이제 새로운 ```Operation.SyntheticSource``` 속성이 ```TelemetryContext```에 제공됩니다. 이제 원격 분석 항목을 "실제 사용자 트래픽이 아님"으로 표시하고 이 트래픽이 생성되는 방식을 지정할 수 있습니다. 예를 들어 이 속성을 설정하여 테스트 자동화의 트래픽과 부하 테스트 트래픽을 구분할 수 있습니다.
* 채널 논리는 Microsoft.ApplicationInsights.PersistenceChannel이라고 하는 별도의 NuGet로 이동되었습니다. 이제 기본 채널은 InMemoryChannel입니다.
* 새로운 ```TelemetryClient.Flush``` 메서드를 사용하여 버퍼의 원격 분석 항목을 동기적으로 플러시할 수 있습니다.

## <a name="version-013"></a>버전 0.13
이전 버전에 대한 릴리스 정보가 없습니다. 




<!--HONumber=Nov16_HO4-->


