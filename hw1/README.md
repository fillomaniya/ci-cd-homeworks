# ci-cd-homeworks
скажу честно - разобраться было очень тяжело, не знаю как пойдет дальше.\
здесь я создал pipeline  
![Pipelines](images/Pipelines.png)
---
далее запустил раннер локально прям в системе mac os, но как я понимаю, если у меня получилось пройти верификацию на сатйе с иностранной картой, то там уже автоматом запускается свой раннер. Создал локально, чтобы попробовать, как он работает, и он даже запустился  
![Runners](images/Runners.png)  
---
Следом прописал код, как было на уроке, и создал pages  
![Pages](images/Pages.png)  
![browser](images/Page_in_browser.png)
---
много раз ковырялся с тестами, как на уроке, поэтому список пайплайнов собрался внушительный  
![all](images/all_pipelines.png)  
---
в итоге получилось после создания pages так же сделать исключение из артефактов  
![code](images/code_in_pages.png)  
---
судя по коду внутри это все сработало  
![exclude](images/exclude.png)  
---
но у меня остался вопрос: если все сработало, и ошибок никаких в пайплайне не отобразилось  
![final](images/final_works_pipeline.png)  
---
почему после создания pages в папке репы эта самая директория public не отображается? где она тогда вообще лежит? ведь страница создалась беспроблемно  
---
собственно вот код из эдитора:  
```
image: busybox:latest

stages:
  - build
  - test
  - deploy

build1:
  stage: build
  script:
    - echo "Do your build here"
    - echo one >> file1.txt
  artifacts:
    paths:
      - file1.txt

test1:
  stage: test
  script:
    - echo "Do a test here"
    - echo "For example run a test suite"

test2:
  stage: test
  script:
    - echo "Do another parallel test here"
    - echo "For example run a lint test"

deploy1:
  stage: deploy
  script:
    - echo "Do your deploy here"
  environment: production

pages:
  stage: deploy
  script:
  - mkdir -p public
  - cp file1.txt public/index.html
  - echo public/file_to_exclude.md
  artifacts:
    paths:
    - public
    exclude:
    - public/file_to_exclude.md
  only:
  - main
``` 
еще дополнение: этот комп у меня на время, если вдруг не получится завершить остальные домашки на нем, буду делать с виртуальной машины на другом компе