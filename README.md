# Домашнее задание к занятию "08.02 Работа с Playbook"

## Подготовка к выполнению
1. Создайте свой собственный (или используйте старый) публичный репозиторий на github с произвольным именем.
2. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

   P.S. Я не смог получить доступ к ресурсу, который был указан в playbook, для доступа к elastic, поэтому я скачал и elastic и hibana с другого ресурса.      Даже с VPN появлялась ошибка 403

3. Подготовьте хосты в соотвтествии с группами из предподготовленного playbook. 

![Снимок экрана от 2022-08-15 20-35-52](https://user-images.githubusercontent.com/93032289/184694218-23d3b94d-2363-47fc-bd79-e83bd65e83e9.png)

4. Скачайте дистрибутив [java](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) и положите его в директорию `playbook/files/`. 

## Основная часть
1. Приготовьте свой собственный inventory файл `prod.yml`.
2. Допишите playbook: нужно сделать ещё один play, который устанавливает и настраивает kibana.
3. При создании tasks рекомендую использовать модули: `get_url`, `template`, `unarchive`, `file`.
4. Tasks должны: скачать нужной версии дистрибутив, выполнить распаковку в выбранную директорию, сгенерировать конфигурацию с параметрами.

![Снимок экрана от 2022-08-15 20-34-16](https://user-images.githubusercontent.com/93032289/184694223-9accbeb1-52b2-4262-8db2-e0314f6204ec.png)

5. Запустите `ansible-lint site.yml` и исправьте ошибки, если они есть.

Поисправлял ошибки risky-file-permissions: File permissions unset or incorrect, дописав в tasks права - mode: 0644

6. Попробуйте запустить playbook на этом окружении с флагом `--check`.

![Снимок экрана от 2022-08-15 21-19-08](https://user-images.githubusercontent.com/93032289/184694229-c7c6e6c2-4489-4d28-98e2-5f6017cb6e2a.png)

7. Запустите playbook на `prod.yml` окружении с флагом `--diff`. Убедитесь, что изменения на системе произведены.

```
hummer@hummer-X570-GAMING-X:~/8.2_Playbook/playbook$ sudo ansible-playbook site.yml -i inventory/prod.yml --diff
[WARNING]: Found both group and host with same name: kibana

PLAY [Install Java] ***************************************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************************************
ok: [localhost]
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host kibana should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with prior Ansible releases. A future Ansible release
 will default to using the discovered platform python for this host. See https://docs.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for more information. This feature will be 
removed in version 2.12. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [kibana]
[DEPRECATION WARNING]: Distribution Ubuntu 18.04 on host elastic should use /usr/bin/python3, but is using /usr/bin/python for backward compatibility with prior Ansible releases. A future Ansible 
release will default to using the discovered platform python for this host. See https://docs.ansible.com/ansible/2.10/reference_appendices/interpreter_discovery.html for more information. This feature 
will be removed in version 2.12. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
ok: [elastic]

TASK [Set facts for Java 11 vars] *************************************************************************************************************************************************************************
ok: [localhost]
ok: [elastic]
ok: [kibana]

TASK [Upload .tar.gz file containing binaries from local storage] *****************************************************************************************************************************************
ok: [localhost]
ok: [kibana]
ok: [elastic]

TASK [Ensure installation dir exists] *********************************************************************************************************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
-    "mode": "0755",
+    "mode": "0644",
     "path": "/opt/jdk/11.0.16"
 }

changed: [localhost]
--- before
+++ after
@@ -1,4 +1,4 @@
 {
-    "mode": "0755",
+    "mode": "0644",
     "path": "/opt/jdk/11.0.16"
 }

changed: [elastic]
--- before
+++ after
@@ -1,4 +1,4 @@
 {
-    "mode": "0755",
+    "mode": "0644",
     "path": "/opt/jdk/11.0.16"
 }

changed: [kibana]

TASK [Extract java in the installation directory] *********************************************************************************************************************************************************
skipping: [localhost]
skipping: [kibana]
skipping: [elastic]

TASK [Export environment variables] ***********************************************************************************************************************************************************************
ok: [localhost]
ok: [kibana]
ok: [elastic]

PLAY [Install Elasticsearch] ******************************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************************************
ok: [elastic]

TASK [Upload tar.gz Elasticsearch from remote URL] ********************************************************************************************************************************************************
ok: [elastic]

TASK [Create directrory for Elasticsearch] ****************************************************************************************************************************************************************
ok: [elastic]

TASK [Extract Elasticsearch in the installation directory] ************************************************************************************************************************************************
ok: [elastic]

TASK [Set environment Elastic] ****************************************************************************************************************************************************************************
ok: [elastic]

PLAY [Install Kibana] *************************************************************************************************************************************************************************************

TASK [Gathering Facts] ************************************************************************************************************************************************************************************
ok: [kibana]

TASK [Upload tar.gz Kibana from remote URL] ***************************************************************************************************************************************************************
ok: [kibana]

TASK [Create directrory for Kibana (/opt/kibana/7.15.1)] **************************************************************************************************************************************************
--- before
+++ after
@@ -1,4 +1,4 @@
 {
-    "mode": "0755",
+    "mode": "0644",
     "path": "/opt/kibana/7.15.1"
 }

changed: [kibana]

TASK [Extract Kibana in the installation directory] *******************************************************************************************************************************************************
ok: [kibana]

TASK [Set environment Kibana] *****************************************************************************************************************************************************************************
ok: [kibana]

PLAY RECAP ************************************************************************************************************************************************************************************************
elastic                    : ok=10   changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
kibana                     : ok=10   changed=2    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
localhost                  : ok=5    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   

```

8. Повторно запустите playbook с флагом `--diff` и убедитесь, что playbook идемпотентен.
9. Подготовьте README.md файл по своему playbook. В нём должно быть описано: что делает playbook, какие у него есть параметры и теги.

   1. Тэги для java 
   
    - Set facts for Java 11 vars - обозначаем переменную
    - Upload .tar.gz file containing binaries from local storage - загружаем архив установщика
    - Ensure installation dir exists - создаем рабочий каталог
    - Extract java in the installation directory - распаковывае архив в директорию, которая указана у нас в /templates/jdk.sh.j2
    - Export environment variables - делаем экспорт переменных окружения

Install Elastic

установлены тэги elastic для дальнейшего использования и отладки

    - Upload .tar.gz file containing binaries from local storage - загружаем архив установщика
    - Ensure installation dir exists - создаем рабочий каталог
    - Extract java in the installation directory - распаковывае архив в директорию, которая указана у нас в /templates/elk.sh.j2
    - Export environment variables - делаем экспорт переменных окружения
    
Install Kibana

установлены тэги kibana для дальнейшего использования и отладки

    - Upload .tar.gz file containing binaries from local storage - загружаем архив установщика
    - Ensure installation dir exists - создаем рабочий каталог
    - Extract java in the installation directory - распаковывае архив в директорию, которая указана у нас в /templates/elk.sh.j2
    - Export environment variables - делаем экспорт переменных окружения

10. Готовый playbook выложите в свой репозиторий, в ответ предоставьте ссылку на него.

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
