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

![image](https://user-images.githubusercontent.com/106908/60381572-e38b3f80-9a91-11e9-90b4-15864f3e3388.png)

List revisions

```
$ cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions | jq -r '.resources[] | {version, description, guid}'
{
  "version": 1,
  "description": "Initial revision.",
  "guid": "7443df40-a8ed-4b6a-b065-6e1bcc8f546d"
}
```

List deployed revisions

```
$ cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions/deployed | jq -r '.resources[] | {version, description, guid}'
{
  "version": 1,
  "description": "Initial revision.",
  "guid": "7443df40-a8ed-4b6a-b065-6e1bcc8f546d"
}
```

Deploy rev2

```
git checkout rev2
cf push
```

![image](https://user-images.githubusercontent.com/106908/60381521-0ec15f00-9a91-11e9-9f49-5c0abc93f7b2.png)

List revisions

```
$ cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions | jq -r '.resources[] | {version, description, guid}'
{
  "version": 1,
  "description": "Initial revision.",
  "guid": "7443df40-a8ed-4b6a-b065-6e1bcc8f546d"
}
{
  "version": 2,
  "description": "New droplet deployed.",
  "guid": "90a63693-cc28-402c-b9dc-46bdb86cb1ae"
}
```

List deployed revisions

```
$ cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions/deployed | jq -r '.resources[] | {version, description, guid}'
{
  "version": 2,
  "description": "New droplet deployed.",
  "guid": "90a63693-cc28-402c-b9dc-46bdb86cb1ae"
}
```

Deploy rev3

```
git checkout rev3
cf push
```

![image](https://user-images.githubusercontent.com/106908/60381430-cb1a2580-9a8f-11e9-8e33-6211c6c33449.png)

List revisions

```
$ cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions | jq -r '.resources[] | {version, description, guid}'
{
  "version": 1,
  "description": "Initial revision.",
  "guid": "7443df40-a8ed-4b6a-b065-6e1bcc8f546d"
}
{
  "version": 2,
  "description": "New droplet deployed.",
  "guid": "90a63693-cc28-402c-b9dc-46bdb86cb1ae"
}
{
  "version": 3,
  "description": "New droplet deployed.",
  "guid": "ccc4f0bc-1dd6-4e7f-b0ff-88e596ba9bce"
}
```

List deployed revisions

```
$ cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions/deployed | jq -r '.resources[] | {version, description, guid}'
{
  "version": 3,
  "description": "New droplet deployed.",
  "guid": "ccc4f0bc-1dd6-4e7f-b0ff-88e596ba9bce"
}
```

Rollback to rev2

```
REV_VESION=2
REV_GUID=$(cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions | jq -r ".resources[] | select(.version == ${REV_VESION}) | .guid")
cf curl /v3/deployments -X POST -d "{\"relationships\":{\"app\":{\"data\":{\"guid\":\"$(cf app ${APP_NAME} --guid)\"}}},\"revision\":{\"guid\":\"${REV_GUID}\"}}"
```

![image](https://user-images.githubusercontent.com/106908/60381521-0ec15f00-9a91-11e9-9f49-5c0abc93f7b2.png)

List revisions

```
$ cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions | jq -r '.resources[] | {version, description, guid}'
{
  "version": 1,
  "description": "Initial revision.",
  "guid": "7443df40-a8ed-4b6a-b065-6e1bcc8f546d"
}
{
  "version": 2,
  "description": "New droplet deployed.",
  "guid": "90a63693-cc28-402c-b9dc-46bdb86cb1ae"
}
{
  "version": 3,
  "description": "New droplet deployed.",
  "guid": "ccc4f0bc-1dd6-4e7f-b0ff-88e596ba9bce"
}
{
  "version": 4,
  "description": "Rolled back to revision 2.",
  "guid": "efc86d6c-f6f5-467e-af99-f75045836d83"
}
```

List deployed revisions

```
$ cf curl /v3/apps/$(cf app ${APP_NAME} --guid)/revisions/deployed | jq -r '.resources[] | {version, description, guid}'
{
  "version": 4,
  "description": "Rolled back to revision 2.",
  "guid": "efc86d6c-f6f5-467e-af99-f75045836d83"
}
```


