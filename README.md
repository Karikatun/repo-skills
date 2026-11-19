# R.E.P.O. Mod Skills

Навыки для Codex по созданию и проверке модов R.E.P.O. Практические примеры взяты из разработки Обручника и Boobees Valuables. Версии и числа в кейсах относятся к указанным сборкам. Для новой задачи сверяйте установленную игру и зависимости.

Скопируйте нужную папку навыка целиком в каталог навыков агента. Папка содержит `SKILL.md` и, где нужно, `references/`. В Codex можно вызвать установленный навык по имени, например `$repo-mod-new-monster`.

## Unity

[Навыки Unity](unity/README.md) ведут к оригинальным инструкциям по NavMesh, профилированию и Unity Test Framework.

## Монстры

| Навык | Когда применять |
| --- | --- |
| [repo-mod-new-monster](monsters/repo-mod-new-monster/SKILL.md) | Идея, исследование игры и выбор первого игрового сценария |
| [repo-mod-visual-design](monsters/repo-mod-visual-design/SKILL.md) | Концепт, референсы для 3D и подробный дизайн |
| [repo-mod-spec](monsters/repo-mod-spec/SKILL.md) | Наблюдаемые требования, критерии и задачи |
| [repo-mod-asset-production](monsters/repo-mod-asset-production/SKILL.md) | Модель, риг, анимация, звук, физические опоры и импорт |
| [repo-mod-implementation](monsters/repo-mod-implementation/SKILL.md) | Плагин, prefab, регистрация, спавн, здоровье и жизненный цикл |
| [repo-mod-monster-movement](monsters/repo-mod-monster-movement/SKILL.md) | Роуминг, погоня, двери, опоры, застревания и нагрузка |
| [repo-mod-monster-combat-coop](monsters/repo-mod-monster-combat-coop/SKILL.md) | Телеграф, контакты, урон, прерывания и синхронизация |
| [repo-mod-acceptance](monsters/repo-mod-acceptance/SKILL.md) | Тесты, проверка пакета в игре, приёмка и передача результата |

## Общие навыки

| Навык | Когда применять |
| --- | --- |
| [repo-mod-toolchain](basic/repo-mod-toolchain/SKILL.md) | Проверка окружения, инструментов и локальных API |
| [repo-mod-workflow](basic/repo-mod-workflow/SKILL.md) | Планирование, состояние, передача и закрытие изменения |
| [repo-mod-test-isolation](basic/repo-mod-test-isolation/SKILL.md) | Изолированный игровой запуск и защита сохранений |
| [repo-mod-debugging](basic/repo-mod-debugging/SKILL.md) | Диагностика по логам, воспроизведение и проверка исправлений |
| [repo-mod-packaging](basic/repo-mod-packaging/SKILL.md) | Воспроизводимая сборка и проверка Thunderstore ZIP |

Пример запроса

> Используй `$repo-mod-new-monster`. Хочу врага, который вынуждает выбирать между укрытием и бегством. Определи первую игровую проверку и главный технический риск.
