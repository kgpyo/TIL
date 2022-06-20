## Publish 할때, Dll 버전이 Nuget에 명시된 버전과 다른 경우

### 문제사항

프로젝트에 Newtonsoft.Json (version 13.0) 패키지 설치 했으나 실제 배포를 위해 publish 명령 사용 시 구 버전 (version 9.0) dll 이 참조됨

### 원인

- 배포를 위해 `publish -c Release -o out` 명령어를 실행하면 다음과 같이 기존 dll 파일을 덮어씌운다.

```bash
PROJECT_A -> c:\~~\PROJECT_A.dll
PROJECT_A -> C:\~~\out\
PROJECT_B -> c:\~~\PROJECT_B.dll
PROJECT_B -> c:\~~\out\
```

- 기존에 사용하고 있는 테스트 프로젝트가 다음과 같은 의존성을 가진 패키지가 설치되어 있다.

```json
{
  "version": 1,
    "dependencies": {
        "net6.0": {
            "Microsoft.TestPlatform.TestHost": {
                "dependencies": {
                  "Microsoft.TestPlatform.ObjectModel": "16.11.0",
                  "Newtonsoft.Json": "9.0.1"
                }
      		}
        }
    }
}
      
```

> [**프로젝트 종속성 확인**](https://docs.microsoft.com/ko-kr/nuget/concepts/dependency-resolution#dependency-resolution-with-packagereference)
>
> 프로젝트 속성에 아래 정보 추가하면 package.lock.json 파일 생성됨
>
> ```
> <PropertyGroup>
> 	<RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>
> </PropertyGroup>
> ```

## 해결

- publish (게시) 할때, 솔루션이아닌 프로젝트를 직접 명시해서 test 프로젝트를 제외시키거나
- 해당 프로젝트 속성에 [`publishable=false`](  <IsPublishable>false</IsPublishable>) 속성을 추가해서 해결할 수 있다.

## 참조

- [net core 3.0 (and higher) publish project with wrong dependencies #3886 ](https://github.com/dotnet/sdk/issues/3886)
