# beta-release-sandbox

Стенд для сравнения двух способов выпуска бета-версии пакета `@sandbox/ui-kit`
из pull request. В npm ничего не публикуется: шаг публикации только печатает
в лог команду, которую выполнил бы.

## Вариант А: по тегу

`.github/workflows/beta-tag.yml`, триггер `push` тега `*-beta.*`.

GitHub исполняет workflow-файл из того коммита, на который указывает тег.
Если тег поставлен на коммит из PR, исполняется версия workflow из PR, и автор
PR может переписать шаг публикации как угодно. В логе это видно по строке
`WORKFLOW SOURCE: ...`.

## Вариант Б: ручной запуск

`.github/workflows/beta.yml`, триггер `workflow_dispatch` с полями `pr` и `feature`.

- Запуск идёт из `main`, поэтому исполняется workflow-файл из `main`
  (`WORKFLOW SOURCE: main`), а код PR только собирается.
- `build` собирает `refs/pull/<pr>/head` без секретов, с правом только на чтение,
  считает версию `<minor+1>-<feature>.<short sha>` и кладёт tarball в артефакт.
- `publish` работает за environment `npm-beta`: без подтверждения reviewer
  не стартует, а запуск не из `main` environment не пропускает.
- `label` вешает на PR метку `beta-<feature>` и оставляет комментарий с командой установки.

## Демо-PR

PR из ветки `feature/context-menu` добавляет компонент и заодно подменяет оба
workflow-файла (`WORKFLOW SOURCE: PULL REQUEST ...`), чтобы показать, какой
из вариантов исполняет чужой код.
