# 📘 09.03 Azure
## 📳 App Service
### 인프라 구축
```bash
1. 스토리지 계정 만들기
- 리소스 그룹 : rg-paaslab
- 이름 : stimg[SUFFIX]
- 위치 : East US
- 성능 : 표준
- 복제 : LRS

2. 만든 스토리지 계정의 연결 문자열 기록
DefaultEndpointsProtocol

3. 컨테이너 만들기
- 이름 : images
- 공용 액세스 수준 : Blob (anonymous read access for blobs only)

4. 샘플 blob 업로드
- grilledcheese.jpg

5. API 웹 앱 만들기
[기본]
- 리소스 그룹 : rg-paaslab
- 이름 : api[SUFFIX]
- 게시 : 코드
- 런타임 스택 : .NET 6 (LTS)
- 운영 체제 : windows
- 위치 : East US
- Windows 플랜 : 새로만들기 -> asp-windows
[모니터링]
Application Insights 사용 : 아니오

6. 프런트엔드 웹 앱 만들기

[기본]
- 리소스 그룹 : rg-paaslab
- 이름 : web[SUFFIX]
- 게시 : 코드
- 런타임 스택 : .NET 6 (LTS)
- 운영 체제 : windows
- 위치 : East US
- Windows 플랜 : asp-imgapp
[모니터링]
- Application Insights 사용 : 아니오
```
### API 연동
```bash
# 1. api 앱 구성
# 1-1 [설정] 섹션의 [구성] 링크

# 1-2 [애플리케이션 설정] 탭의 [+새 애플리케이션 설정]
# 이름: StorageConnectionString
# 값 : 스토리지 계정 연결 문자열

# 2. web 앱 구성
# 2-1 [설정] 섹션의 [구성] 링크

# 2-2 [애플리케이션 설정] 탭의 [+새 애플리케이션 설정]
# 이름 : ApiUrl
# 값 : api 앱의 URL

#3. api 앱 코드 배포
az login
az webapp deployment source config-zip --resource-group rg-paaslab --src api.zip --name apipsj

#4. api 앱 동작 테스트

#5. web 앱 코드 배포
az webapp deployment source config-zip --resource-group rg-paaslab --src web.zip --name webpsj

#6. web 앱 동작 테스트
```