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