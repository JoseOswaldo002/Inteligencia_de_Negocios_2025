## Comando para ejecutar en linux
```docker
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=`Mipassw0rd123!`" \
-p 1422:1433 --name sqlserverBI \
-v sqlserver-volume:/var/opt/mssql \
-d mcr.microsoft.com/mssql/server:2025-latest
```
## Comando para ejecutar en windows
```docker
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=Mipassw0rd123!" `
-p 1422:1433 --name sqlserverBI `
-v sqlserver-volume:/var/opt/mssql `
-d mcr.microsoft.com/mssql/server:2025-latest
```

bbb744610da31eff5b7d34dfaf0eb9d55af997c804c4b416fddc54779948c878