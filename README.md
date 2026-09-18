# perm-test

Тестовый репозиторий для проверки поведения GitHub Actions environments:

1. Ставятся ли в очередь (сериализуются) джобы, ссылающиеся на **один и тот же** environment, или выполняются параллельно.
2. Объединяется ли approval для нескольких джобов, которые становятся pending одновременно (после общего `needs`), в один экран "Review pending deployments".

## Что проверяем

`.github/workflows/env-test.yml` содержит:

- `same-env-a` и `same-env-b` — оба на environment `shared-env`.
- `diff-env-a` и `diff-env-b` — на разных environment (`env-a`, `env-b`).

Все четыре зависят от `setup` и должны стать pending одновременно.

## Как запускать

1. Создать репозиторий `perm-test` в своём личном GitHub-аккаунте, запушить этот код в `main`.
2. В Settings → Environments создать `shared-env`, `env-a`, `env-b` и добавить себя как required reviewer в каждый (чтобы проверить approval-часть).
3. Запушить коммит (или запустить workflow вручную через "Run workflow") и открыть вкладку Actions → сам run.
4. Проверить:
   - На экране approval — появляются ли все pending джобы сразу одним списком с чекбоксами, или отдельными запросами в разное время.
   - После аппрува — по времени старта/конца в логах (`date +%T`) видно, выполнялись ли `same-env-a`/`same-env-b` параллельно или одна ждала другую. Сравнить с `diff-env-a`/`diff-env-b`.
