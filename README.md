# Cloud Foundry Revision demo

```
cf push --no-start
```

Enable revisons

```
APP_NAME=rev
cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/features/revisions -X PATCH -d '{"enabled": true}'
cf start ${APP_NAME}
```

List revisions

```
cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions
```

List deployed revisions

```
cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions/deployed
```