# Слоты и регистрация косметики

## От штатного образца до пакета

- В SDK найди prefab нужного типа в `Assets/REPO/Game/Resources/cosmetics`. Сопоставь иерархию с экземпляром в игре той же версии. Проверь `Cosmetic.meshParents`, порядок вариантов и типы, для которых игра меняет форму. [Руководство REPOLib SDK](https://repomods.com/apis/repolib/sdk/cosmetics.html)
- В Unity свяжи REPOLib Cosmetic Asset с Vanilla Cosmetic Asset, укажи показанное имя, тип, редкость и параметры окраски. Для каждого элемента зарезервируй постоянный ID вида `modname:item`. Префикс `vanilla:` зарезервирован. Предпочтительный вариант загрузки по ссылке использует `Prefab Ref`, пустое `Prefab` и запись prefab в `Mod → Extra Bundle Files`. [Руководство REPOLib SDK](https://repomods.com/apis/repolib/sdk/cosmetics.html)
- Для регистрации из плагина загрузи AssetBundle с `CosmeticContent` и передай его в `REPOLib.Modules.Cosmetics.RegisterCosmetic`. Выбирай одну точку регистрации. Сохрани существующие IDs при обновлении, даже если меняются название, модель и миниатюра. [API REPOLib](https://repomods.com/apis/repolib/api/cosmetics.html)
- При установке SDK подбери Unity Editor под целевую сборку игры. В корень ZIP добавь `manifest.json`, `README.md` и `icon.png` 256×256, затем файлы мода. Зависимости бери из манифеста проверенного пакета. [Настройка SDK](https://repomods.com/apis/repolib/sdk/start.html), [формат пакета Thunderstore](https://wiki.thunderstore.io/mods/creating-a-package)

## Размещение из Boobees Suits

Эти назначения относятся к R.E.P.O. 0.5.3 и Boobees Suits 0.3.8. Для другой одежды сначала сверь слоты штатных предметов и соседство.

Штатные ориентиры из локальной сборки: `Cosmetic - Arm Left - Knit Sleeve` и `Cosmetic - Arm Right - Short Sleeve` для рукавов, `Cosmetic - Foot Left - Tube Sock` для носка, `Cosmetic - Body Top - Basic Shirt` для груди, `Cosmetic - Hat - Cap` для крепления наверху головы. Для области головы вокруг глаз можно начать с `Cosmetic - Head Top Overlay - Soot Eyes`, для смены формы нижней части головы с `Cosmetic - Head Bottom Overlay - Kiwi`, для двух областей корпуса с `Cosmetic - Body Top Overlay - Damaged` и `Cosmetic - Body Bottom Overlay - Cracks`.

| Деталь | Категория | Особенность посадки |
| --- | --- | --- |
| Усики | `Hat` | Крепятся к верхней части головы. |
| Верх и низ капюшона | `HeadTopOverlay`, `HeadBottomOverlay` | Следуют разным частям головы. Нижний prefab имеет два варианта формы. |
| Грудь | `BodyTop` | Следует за верхом корпуса, собственная деформация внутри предмета. |
| Полосатый край | `BodyTopOverlay` | Заменяет нижнюю накидку, которая исчезала вместе с `BodyBottom` в приседе. Два варианта формы. |
| Крылья | `BodyBottomOverlay` | Категория для меню. Собственный экземпляр перепривязывается к верхнему корпусу на время экипирования. |
| Рукава | `ArmLeft`, `ArmRight` | Индивидуальные крепление, локальная ось и модель для каждой стороны. |
| Носочки | `FootLeft`, `FootRight` | Типы ног по сторонам, посадка по модели каждой ноги. |

В локальной сборке реестр `PlayerCosmetics.cosmeticParents` содержит отдельные родителей для замены геометрии и аксессуаров. Например, `ArmLeft` и `ArmLeftMesh` совпадают по точке крепления, а локальный поворот второго вокруг Y примерно +90°. Справа соответствующий поворот −90°. При переносе формы между этими категориями пересчитай преобразование. Подробные позиции и масштабы есть в навыке `repo-mod-cosmetic-fit`.

При несовместимости головы, корпуса и нижнего элемента проверь `BodyTop_Overlap_HeadBottom`, `HeadBottom_Overlap_BodyTop`, `BodyTop_Overlap_BodyBottom_Front` и `BodyTop_Overlap_BodyBottom_Back`. При проверке обзора учти `Player_LookDown_Restrict`. Эти флаги влияют и на соседнюю штатную косметику. [Таблица типов REPOLib SDK](https://repomods.com/apis/repolib/sdk/cosmetics.html)

## Предмет отсутствует или выглядит неправильно

1. По логу установи, прочитал ли загрузчик архив и регистрацию. Проверь активную версию игры и REPOLib, совпадение ID и имя категории. Исправление одного из случаев отсутствия модовой косметики при первом показе в мультиплеере отмечено в REPOLib 4.1.0. Локальные проверки этой сборки проведены с 4.5.1. Проверь разблокировку отдельно. [История версий REPOLib](https://github.com/ZehsTeam/REPOLib/blob/main/CHANGELOG.md)
2. Если запись есть, проверь разрешение ссылки prefab, фактического родителя, видимость в стойке и приседе, активный вариант формы и материал. Скриншот «просвечивания» сам по себе совместим с прозрачным шейдером, обратным winding, отсечением меша и клиппингом тела. Для каждой причины нужна своя проверка. [Unity Skinned Mesh Renderer](https://docs.unity3d.com/6000.0/Documentation/Manual/class-SkinnedMeshRenderer.html)
3. Сними миниатюру из реального меша с игровыми цветами, освещением, прозрачным фоном и одинаковыми отступами. Для Boobees Suits отработали квадратные спрайты 256×256. Сверь размер и формат по текущему штатному предмету.
