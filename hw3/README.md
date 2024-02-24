### Пункты 1, 2, 3, 3

Добавляем окружение `preprod` и `production`. В них используем переменную `$MyLogin` (в настройках она будет иметь разные значения).
```yaml
deploy to preprod:
  stage: deploy
  variables:
    TARGET_ENV: preprod
    MyLogin: "Preprod ROMAN"
  script:
    - echo "Do your deploy here to ${TARGET_ENV}"
    - echo ${DB_SERVER}
    - echo "MyLogin"
    - echo $MyLogin
    - echo "MyPassword"
    - echo $MyPassword
  only:
    - main
  environment:
    name: preprod
```


```yaml
deploy to production:
  stage: deploy
  variables:
    TARGET_ENV: production
    MyLogin: "Production ROMAN"
  script:
    - echo "Do your deploy here to ${TARGET_ENV}"
    - echo ${DB_SERVER}
    - echo "MyLogin"
    - echo $MyLogin
    - echo "MyPassword"
    - echo $MyPassword
  only:
    - main
  environment:
    name: production
```

Настройки переменных. Здесь добавляем переменную `$MyPassword` через интерфейс. Она тоже будет различна для разных сред исполнения. Плюс здесь же добавляем `RUNNER_TOKEN` — он понадобиться для скрипта удаления из пункта 5.
![variables page](images/1.png "variables page")

Pipeline passed
![pipeline passed](images/2.png "pipeline passed")

Deploy to preprod. На скриншоте — уникальные $MyLogin и $MyPassword для preprod
![deploy to preprod](images/3.png "deploy to preprod")

Deploy to production. Для production — свои $MyLogin и $MyPassword
![deploy to production](images/4.png "deploy to production")

### Пункт 4

```yaml
# ------- Cancel -------
cancel:
  stage: stop previous jobs
  image: everpeace/curl-jq
  script:
    - |
      if [ "$CI_COMMIT_REF_NAME" == "main" ]
        then
          (
            echo "Cancel old pipelines from the same branch except last"
            OLD_PIPELINES=$( curl -s -H "PRIVATE-TOKEN: $RUNNER_TOKEN" "https://gitlab.com/api/v4/projects/${CI_PROJECT_ID}/pipelines?ref=${CI_COMMIT_REF_NAME}&status=running" \
                  | jq '.[] | .id' | tail -n +2 )
                  for pipeline in ${OLD_PIPELINES}; \
                      do echo "Killing ${pipeline}" && \
                        curl -s --request POST -H "PRIVATE-TOKEN: ${RUNNER_TOKEN}" "https://gitlab.com/api/v4/projects/${CI_PROJECT_ID}/pipelines/${pipeline}/cancel"; done
          ) || echo "Canceling old pipelines (${OLD_PIPELINES}) failed"
      fi
```


Тут вроде получилось. Pipeline passed
![deploy to production](images/5.png "deploy to production")

