# 📘 09.05 Azure
## 📳 App Service
### Create App
- plan, runtimes, webapp Create
```bash
# 앱 서비스 요금제
az appservice plan create -g rg-paaslab -n asp-linux --is-linux --number-of-workers 1 --sku B1

# 런타임 스택 확인
az webapp list-runtimes --os-type linux

# 앱 서비스
az webapp create -g rg-paaslab -p asp-linux -n webgbpsj --runtime "NODE:22-lts"
```
### App CI/CD
```bash
# 1. api 앱 구성
# 1.1 [설정] 섹션의 [구성] 링크

# 1.2 [애플리케이션 설정] 탭의 [+새 애플리케이션 설정]
# 이름 : StorageConnectionString
# 값 : 스토리지 계정 연결 문자열

# 2. web 앱 구성
# 2.1 [설정] 섹션의 [구성] 링크

# 2.2 [애플리케이션 설정] 탭의 [+새 애플리케이션 설정]
# 이름 : ApiUrl
# 값 : api 앱의 URL

# 3. api 앱 코드 배포
az login
az webapp deployment source config-zip --resource-group rg-paaslab --src api.zip --name apipsj

# 4. api 앱 동작 테스트

# 5. web 앱 코드 배포
az webapp deployment source config-zip --resource-group rg-paaslab --src web.zip --name webpsj

# 6. web 앱 동작 테스트
