# Содержание

- [💎 Определение](#определение)
- [⁉️ В каких случаях может понадобиться RXCacheManager ?](#в-каких-случаях-может-понадобиться-RXCacheManager-?)
- [💡 Концепция](#концепция)
- [🗿 Какой тип севера требуется для работы RXCM ?](#какой-тип-севера-требуется-для-работы-RXCM-?)
- [🍑 Какие приемущества использования RXCM ?](#какие-приемущества-использования-RXCM-?)
- [📖 Как устроено веб-хранилище которое может быть закэшированно фраемворком RXCM ?](#как-устроено-веб-хранилище-которое-может-быть-закэшированно-фраемворком-RXCM-?)
- [🗂 Структура папок и файлов](#структура-папок-и-файлов)
   -  [Значение содержимого конфигурационных файлов](#значение-содержимого-конфигурационных-файлов)
- [❔Для чего нужны объекты RXCMOneChange ?](#для-чего-нужны-объекты-RXCMOneChange-?)
- [⚙️Какие типы изменений поддерживает RXCM](#какие-типы-изменений-поддерживает-RXCM)
- [🈚️ Синтаксис конфигурационного словаря из которого будет создан объект RXCMOneChange](#синтаксис-конфигурационного-словаря-из-которого-будет-создан-объект-RXCMOneChange)
    - [Добавление](#добавление)
    - [Удаление](#удаление)
    - [Переименование](#переименование)
    - [Перемещение](#перемещение)
    - [Перезагрузка](#перезагрузка)
- [🛠 Как добавить начальную поддержку RXCM на сервере?](#как-добавить-начальную-поддержку-RXCM-на-сервере?)
- [🔧 Что менять в конфигурационных файлов после изменения содержимого кэша ?](#что-менять-в-конфигурационных-файлов-после-изменения-содержимого-кэша-?)
- [🎯 Подключение RXCM в проект](#подключение-RXCM-в-проект)
- [🚀 Интеграция RXCM в проект](#интеграция-RXCM-в-проект)
- [🏗 Методы управления кэш менеджером](#методы-управления-кэш-менеджером)
- [💼 Снипеты и дополнительные возможности](#снипеты-и-дополнительные-возможности)
    - [Регулируемый тип интернет содениения](#регулируемый-тип-интернет-содениения)
    - [Печать логов в консоль](#печать-логов-в-консоль)
    - [Проверка наличия ошибок](#проверка-наличия-ошибок)
    - [Инициализация скаченных файлов с диска](#инициализация-скаченных-файлов-с-диска)
    - [Удаление конфигурационных объектов из NSUserDefault](#удаление-конфигурационных-объектов-из-NSUserDefault)



## Определение

**RXCM** (RXCacheManager) - это класс который кэширует все изменения происходящие с файлами на сервере  на устройство.

## В каких случаях может понадобиться RXCacheManager ?

**RXCM** может понадобиться при написании клиент-серверного приложения, когда вы хотите автоматический применять те изменения над файлами которые были совершены на сервере.

Например вы имеете кулинарное приложение. На сервер вы добавили некий json с описанием нового блюда, **RXCM** обнаружит изменение веб-хранилща и скачает данный файл.
Или же если вы удалили некий файл или папку из облака, RXCM также удалит их из памяти устройства.


<br>
<br>

![5cc72762379ff](/Documentation/ScreeSnippet/RXCM_DEMO.gif)

<br>
<br>

## Концепция

**RXCM** (RXCacheManager) - это класс который при открытии приложения получает от сервера информацию о состоянии облочного хранилища. Затем **RXCM** сравнивает версию кэша на устройстве и версию хранилища в облаке. После чего совершает обновления. 
![5cc72762379ff](/Documentation/ScreeSnippet/ThePrincipleOfWorkingRXCacheManager.png)

## Какой тип севера требуется для работы RXCM ?

Алгоритм фраемворка **RXCacheManager** является очень гибким и для полноценного функционирования (кэширования всех изменений с сервера на устройства) вам потребуется облачное хранилище которое имеет возможность предоставлять **ПРЯМЫЕ** ссылки на файл.

Например вы можете использовать стандартный репозиторий **github**.

## Какие приемущества использования RXCM ?

Для кэширования всех изменений на сервере вам не требуется иметь собственный сервер c готовым API.
Вы можете использовать любое облако которое моет предоставить прямую ссылку на файл.

Но если же вы уже имеете собственный backend - это только упростит поддержку конфигурационных файлов RXCM.

## Как устроено веб-хранилище которое может быть закэшированно фраемворком RXCM ?

Для того чтобы ваше устройство могло сравнивать версии хранилищ, где-то должны храниться конфигурационные файлы вашего кэша.

#### Структура папок и файлов

1. **RXCacheConfig** - папка в которой храняться все конфигурационные файлы, которые описывают изменения версий.

2. **RXCacheContent** - папка в которой храняться пользовательские файлы, которые подлежат кэшированию.

3. **RXCacheContent**.zip - архив папки RXCacheContent.

4. **HistoryOfVersion** - папка в которой храняться json файлы которые описывает какие изменения были совершены между версией А и Б.

![5cc7281c60e9e](/Documentation/ScreeSnippet/structureAndArrangementOfFilesInRootDirectory.png)

#### Значение содержимого конфигурационных файлов

1. **cacheStatus.json** 

Это файл который описывает текущие состяние кэша. 

(Симметричный объект в фраемворке - **RCXMCacheStatus: NSObject**)

![5cc74f4759656](/Documentation/ScreeSnippet/cacheStatus2.png)

| Ключ                  | Объяснение                                                                                                        |
| --------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `actualCacheVersion`  | сообщает какой номер версии имеет кэш.                                                                            |
| `minimalCacheVersion` | минимальная версия кэша на устройстве для которой примнимы обновления от новых версий которые храняться в облаке. |

#### Для чего нужен ключ "minimalCacheVersion" ?

> Представьте себе, что выпустили первую версию своего приложения 3 года назад и некий пользователь скачал себе на устройство первую версию приложения. После чего пользователь не обновлял это приложение долгое время, а спустя 3 года решил его открыть.
> 
> Ответье пожалуйста, имеет ли смысл выполнять все точечные изменения накопленные за три года ?
> 
> - Нет, потому что их может быть тысячи. Поэтому в данном случае легче скачать архив самой последней версией кэша и распаковать его.
> 
> В первую очередь алгоритм RXCM сравнивает minimalCacheVersion веб хранилища и текущую версию кэша на устройстве. Если версия на устройстве является меньше, то тогда весь кэш удаляется и скачивается архив.

#### В каких случаях мне нужно изменять значение "minimalCacheVersion" ?

> Далее вам станет известно, что каждое изменение на веб-хранилище вы должны будете описать в конфигурационных файлах (дословно -"этот файл находился здесь, а теперь он находиться тут"), если вдруг за одно обновление вы сделали очень большое количество изменений и не хотите их описывать в конфигурационных файлах (потому что это может быть очень долго) - вы можете указать что minimalCacheVersion равняется actualCacheVersion 

```json
{
"actucalCacheVersion" : "1.5",
"minimalCacheVersion" : "1.5",
}
```

> Тогда любая версия кэша на устройстве ниже чем 1.5, будет полностью удалена и будет скачен архив папки RXCacheContent с новым содержимым и распакован на устройстве

| Ключ                                | Объяснение                                                                                                                                                            |
| ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `urlAtListOfURLsToUpdatesByVersion` | ссылка на json который содержит ссылки на модели описывающие изменения по версям.                                                                                     |
| `urlAtZipArchiveOfCacheContent`     | ссылка на архив папки RXCacheContent.                                                                                                                                 |
| `zippedArchiveSizeInMB`             | размер запакованного архива в мегабайтах. Алгоритм RXCM перед скачиванием вычисляет сколько места требуется архив. Если места недостаточно то RXCM возвращает ошибку. |
| `unzippedArchiveSizeInMB`           | размер распакованного архива в мегабайтах. Также алгоритм вычисляет свободное место на диске устройства. Если его окажется недостаточно, то будет возвращена ошибка.  |

2. **listOfURLsToUpdatesByVersion.json**

![5cc74f9dbe1a1](/Documentation/ScreeSnippet/listOfURLsToUpdatesByVersion2.png)

| Ключ                | Объяснение                                                                           |
| ------------------- | ------------------------------------------------------------------------------------ |
| `updatesByVersions` | массив содержит ПРЯМЫЕ ссылки на json модели которые описывают обновления по версиям |

3. **changes_1.0_1.1.json**

![5cc74fbd6e388](/Documentation/ScreeSnippet/changes_1.0_1.1.png)

Файл содержит массив изменений и номера версий между которыми они были совершены .(Симметричного объект фраемворк не имеет).

| Ключ          | Объяснение                                                                                        |
| ------------- | ------------------------------------------------------------------------------------------------- |
| `from` и `to` | номера версий кэша между которыми были применены изменения описанные в массиве `changes`          |
| `changes`     | массив который содержит объекты описывающие изменения которые были совершены между версиями кэша. |

Словарь вложенный в массив `changes` имеет симетричный объект - **RXCMOneChange : NSObject**

**RXCMOneChange** - это класс который описывает изменения которые были совершенны над ОДНИМ файлом. 

Например ранее созданный файл был перемещен на веб-хранилище,  для того чтобы процедура перемещения также была совершена и на устройстве, вы должны добавить один словарь (в котором будет описано изменения) в массив `changes` .

О том как сконфигурировать словарь так чтобы алгоритм RCXM понял что например мы хотим удалить/добавить файл (или совершить другие действия) будет описанно позже.

## Для чего нужны объекты RXCMOneChange ?

Как было написано выше из словарей содержащихся в массиве `changes` в файлах подобных `changes_1.0_1.1.json` будут созданы объекты класса **RXCMOneChange**. После чего массив с объектами **RXCMOneChange** будет передан на исполнение в системный метод фраемворка.

Алгоритм RXCM проанализирует состояние переменных объекта и поймет какого рода изменения должно быть выполненно.

## Какие типы изменений поддерживает RXCM

| Тип изменения  | Описание изменения                                                                                                                                                                                                                                                                                                                                                                                        |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Добавление     | Добавляет(то есть скачивает) файл или создает папку.                                                                                                                                                                                                                                                                                                                                                      |
| Удаления       | Удаляет файл или папку                                                                                                                                                                                                                                                                                                                                                                                    |
| Переименование | Переименновывает файл или папку                                                                                                                                                                                                                                                                                                                                                                           |
| Перемещение    | Перемещает файл или папку                                                                                                                                                                                                                                                                                                                                                                                 |
| "Перезагрузка" | Данный тип действия может быть использован когда вам нужно обновить контент некого файла. Например на веб-хранилище вы имеете некий текстовый файл, он был изменен извне. А теперь вы хотите чтобы содержание текстовых файлов на устройстве и на сервере были идентичны. <br/><br/> Тогда вам нужно выбрать этот тип изменения. Тогда старая копия этого файла будет удалена, а новая скачена с сервера. |

## Синтаксис конфигурационного словаря из которого будет создан объект RXCMOneChange

#### Добавление

Если вы хотите загрузить файл на устройство или создать папку, то оставьте пустым поле `oldPath`, а в `newPath` укажите url на элемент который храниться на веб-хранилище.

```json
"oldPath" : "",
"newPath" : "https://.../RXCacheContent/folder1",
"redownload" : false,
"rename"     : false
}
```

#### Удаление

Если вы хотите удалить (файл или папку), то в поле `oldPath` укажите текущие распаложение элемента, а поле `newPath` оставьте пустым. Таким образом алгоритм RXCM поймет что вы хотите удалить элемент.

```json
{
"oldPath" : "https://.../RXCacheContent/folder1", 
"newPath" : "",
"redownload" : false,
"rename"     : false
}
```

#### Переименование

Если вы хотите переименовать файл или папку, то в поле `oldPath` укажите текущий адрес где расположен элемент, а в поле `newPath` укажите тот же путь но уже с новым названием элемента.
Также установите значение `true` в поле rename.

```json
{
"oldPath" : "https://.../RXCacheContent/folder1",
"newPath" : "https://.../RXCacheContent/Myfolder1",
"redownload" : false,
"rename"     : true
}
```

#### Перемещение

Если вы хотите переместить файл или папку, то в поле `oldPath` укажите текущий адрес где расположен элемент, а в поле `newPath` укажите новый адрес.

```json
{
"oldPath" : "https://.../RXCacheContent/folder1",
"newPath" : "https://.../RXCacheContent/SecretFolder/folder1",
"redownload" : false,
"rename"     : false
}
```

#### Перезагрузка

Если вы хотите чтобы файл был перезагружен (то есть сначала удален с устройства, а потом загружена свежая копия), то введите существующий путь до файла в поле `oldPath` и `newPath`. 
И не забудьте установить значение `true` в поле `redownload`.

⚠️ Внимание. Перезагрузить можно исключительно файл. ПАПКУ с вложенными файлами перезагрузить нельзя.

🍬 Также вы можете единовременно **перезагрузить** и **переместить** и **переименовать** если установите значения `true` в поля `redownload` и `rename` .

```json
{
"oldPath" : "https://.../RXCacheContent/esse.txt",
"newPath" : "https://.../RXCacheContent/esse.txt",
"redownload" : true,
"rename"     : false
}
```

## Как добавить начальную поддержку RXCM на сервере?

#### Шаг 1

Расположите папки **RXCacheConfig**, **RXCacheContent** в одной директории.
Например такой директорией может быть корневая папка вашего репзитория как это сделал я. Пример: https://github.com/m1a7/DemoCacheStorage

#### Шаг 2

Наполните пользовательским контетом папку **RXCacheContent**. И создайте архив **RXCacheContent.zip** который будет располагаться в той же директории.

#### Шаг 3

Создайте файл `cacheStatus.json` в папке **RXCacheConfig** и вставьте в него этот код.

(⚠️ Не забудьте вставить собственные значения в полях относящихся к размеру архива и собственный путь до конфигурационных файлов).

[Copy code](Documentation/TextSnippet/cacheStatus_Snippet.txt)![5cc84cc2388ee](/Documentation/ScreeSnippet/cacheStatusSnippet.png)

#### Шаг 4

Cоздайте файл `listOfURLsToUpdatesByVersion.json` в папке **RXCacheConfig** и вставьте в него этот код.

[Copy code](Documentation/TextSnippet/listOfURLsToUpdatesByVersion_EmptySnippet.txt)

![5cc84e986da8a](/Documentation/ScreeSnippet/listOfURLsToUpdatesByVersion_EmptySnippet.png)

#### Сервер к поддержке RXCM готов !

Все должно было получиться так, как изображено на этой схеме

![5cc7281c60e9e](/Documentation/ScreeSnippet/structureAndArrangementOfFilesInRootDirectory.png) 

## Что менять в конфигурационных файлов после изменения содержимого кэша ?

Каждый раз после того как вы внесете какие-либо изменения в папку RXCacheConent вы имеете два пути:

#### Путь 1.

1. Создать новый файл описывающие изменения между версиями (Например `changes_1.0_1.1.json`) и указать в нем все изменения (добавление новых файлов, удаление старых и т.д.)

2. Удалить старый архив `RXCacheContent.zip` и создать новый.

3. Обновить содержимое `cacheStatus.json` 

```json
// Было
{
"actualCacheVersion"        : "1.0",
"minimalCacheVersion"        : "1.0",

"zippedArchiveSizeInMB"    : "0.27",
"unzippedArchiveSizeInMB" : "0.311"
}
```

```json
// Cтало
{
"actualCacheVersion"        : "1.1",
"minimalCacheVersion"        : "1.0",

"zippedArchiveSizeInMB"    : "20",
"unzippedArchiveSizeInMB" : "30"
}
```

#### Когда использовать подход описанный в "путь 1" ?

Используйте данный подход тогда, когда было привнесено **небольшое количество изменений**. Тогда вам будет легко описывать их в конфигурационном файле  (`changes_1.0_1.1.json`).

Или когда ваш кэш на устройстве уже имеет большое количество контента и вы не хотите чтобы ради одно добавленного файла весь кэш был удален и скачен заново архивом.

Такая ситуация может быть если вы пишите приложения которое может сохранять в offline режим фильм, допустим на устройстве уже храняться разные фильмы которые занимают `10GB` места, и вы добавляете два коротких ролика. В таком случае целесообразно описать все изменения в конфигурационном файле, а не удалять кэш и скачивать большой архив.

#### Путь 2.

1. Удалить старый архив `RXCacheContent.zip` и создать новый.

2. Обновить содержимое `cacheStatus.json`(указав что `minimalCacheVersion` равняется  `actualCacheVersion`). 

```json
// Было
{
"actualCacheVersion"      : "1.0",
"minimalCacheVersion"     : "1.0",

"zippedArchiveSizeInMB"   : "0.27",
"unzippedArchiveSizeInMB" : "0.311"
}
```

```json
// Cтало
{
"actualCacheVersion"     : "1.1",
"minimalCacheVersion"    : "1.1",

"zippedArchiveSizeInMB"   : "20",
"unzippedArchiveSizeInMB" : "30"
}
```

#### Когда использовать подход описанный в "путь 2" ?

Используйте данный подход тогда, когда было привнесено **большое количество мелких изменений** и общий вес кэша является небольшим (до **50мб**). 
Например вы создали много текстовых файлов, поменяли названия старым, что-удалили, что-то модифицировали. В таком случае удобно "повысить" minimalCacheVersion, что не описывать все изменения в конфигурационных файлах.

## Подключение RXCM в проект

#### Шаг 1

Создайте папку **Frameworks** в папке вашего проекта и скопируйте в нее файл **RXCM.framework**.

![5cc878d7b5ac9](/Documentation/ScreeSnippet/addRXCM-1.png)

#### Шаг 2

Перетащите папку вместе с вложеным фрамеворком в структуру проекта.

![5cc879443153e](/Documentation/ScreeSnippet/addRXCM-2.png)

#### Шаг 3

Во вкладке **General** добавьте **RXCM.framework** в секции **Embedded Binaries** и **Linked Frameworks and Libraries**.

![5cc879915cdeb](/Documentation/ScreeSnippet/addRXCM-3.png)

## Интеграция RXCM в проект

Для успешного функционирования кэш менеджера вам потребуется создать объект класса RXCacheManager (или сокращенно RXCM) и реализовать методы делегата (необязательно). 

Для этой цели мы рекомендую выбрать класс **AppDelegate**, потому что объект кэш менеджера должен существовать на протяжении всей жизни приложения + рекомендуется вызывать некоторые методы управления менеджером (например приостановка обновления или возобновления) в стандартных методах **AppDelegate** когда приложение закрывается и открывается.

### Шаг 1

Импортируйте фраемворк в ваш класс.

```objectivec
#import <RXCM/RXCM.h>
```

### Шаг 2

Создайте проперти на объект кэш менеджера.

```objectivec
@property (nonatomic, strong) RXCacheManager* cache;
```

### Шаг 3

Перед созданием объекта, выберите комфортный для вам протокол делегата. 

**RXCacheManager** может работать в двух режимах: 
**1)** Скачивать все файл (системные и пользовательские) самостоятельно.
**2)** Переложить эту ответственность на вас и принимать уже скаченные вами файлы.

Фраемворк предлагает на выбор два протокола:

**RXInternalNetworkCacheManagement**   -  при выборе этого протокола фраемворк скачивает все файлы сам, но вы имеете возможность редактировать параметры сетевого запроса (например добавлять токен в params). (*Данный протокол используется в абсолютном большинстве случаев*).

**RXRemoteNetworkCacheManagement** - при выборе этого протокола вы сами должны скачивать все файлы, и продоставлять фраемворку уже готовые файлы. Такой способ взаимодействия может быть полезным если вы уже имеете сложный собственно написанный сетевой слой, и к например во время любых запросов к вашему серверу пользователь должен заполнять графическую капчу.

Также опеределитесь в какой папке на устройстве вы будите хранить пользовательский кэш.                         Для это цели Apple рекомендует использовать папку `Library/Caches`.
Подробней о рекомендациях Apple можно прочитать [Тут](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html#//apple_ref/doc/uid/TP40010672-CH2-SW1).

```objectivec
@interface AppDelegate () <RXInternalNetworkCacheManagement>
@end
```

```objectivec
- (BOOL)application:(UIApplication *)application 
didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

NSString* basePrefixURLToCache  =         
@"https://raw.githubusercontent.com/m1a7/DemoCacheStorage/master/";

NSString* pathToLocalDirectory = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory,         
NSUserDomainMask,YES) 
firstObject];


self.cache = [RXCacheManager initWithBaseURLPathPrefixToCache: basePrefixURLToCache
andPathToLocalDirectoryWhereWillStoreCache: pathToLocalDirectory
withInternalNetworkDelegate: self];

return YES;
}
```

### Шаг 4

Реализуйте методы делегата. 

**Реализация методов RXInternalNetworkCacheManagement**

[Open image in full size](/Documentation/ScreeSnippet/RXInternalNetworkCacheManagement.png)![5cc8951b024d6](/Documentation/ScreeSnippet/RXInternalNetworkCacheManagement.png)

(Если вы выбрали **RXInternalNetworkCacheManagement** вы можете не реализовывать методы вообще). 

Во всех методах данного делегата вам предоставляется возможность редактировать параметры сетевого запрос с помощью объекта класса **RXCMRequiredComponentsForRequest**.
Вы можете изменить следующие пропери (`url`,`httpMethod`,`params`,`httpFileds`).

Вне зависимости от того, модифицировали **RXCMRequiredComponentsForRequest** или нет, вы обязаны вернуть его в `configureBlock`, так как это изображено на рисунке.

Пример реализации одного из методов протокола:

[Open image in full size](/Documentation/ScreeSnippet/rxcm_willCheckingForMoreActualCacheStatusVersion_Body.png)![5cc899949d596](/Documentation/ScreeSnippet/rxcm_willCheckingForMoreActualCacheStatusVersion_Body.png)

#### или

**Реализация методов RXRemoteNetworkCacheManagement**

[Open image in full size](/Documentation/ScreeSnippet/RXRemoteNetworkCacheManagement.png)![5cc89ecc743f6](/Documentation/ScreeSnippet/RXRemoteNetworkCacheManagement.png)

Пример реализации одного из методов протокола:

[Copy code](Documentation/TextSnippet/rxcm_expectsActualCacheStatusFromURL.txt)![5cc8a1a14caf5](/Documentation/ScreeSnippet/rxcm_expectsActualCacheStatusFromURL.png)

### Шаг 5 (Необязательно)

Также вы можете реализовать вспомогательные методы протокола **RXCacheManagerHelperProtocol**.

Эти методы отображают процесс обновления, уведомляют о завершении обновления или другое.

[Open image in full size](/Documentation/ScreeSnippet/RXCacheManagerHelperProtocol.png)![5cc8afa94410b](/Documentation/ScreeSnippet/RXCacheManagerHelperProtocol.png)

### Шаг 6

Определитесь в каком состоянии вы будите запуска процесс обновления, а в каких приостанавливать.
Мы рекомендуем использовать эти методы для **запуска** и **приостановки**.

```objectivec
- (void)applicationWillResignActive:(UIApplication *)application {
// Called when pressed on Home Button

if (self.cache.enumCacheState == RXCM_WorkingState){
[self.cache startSuspendResumeUpdateProcess];
}
}
```

```objectivec
- (void)applicationDidBecomeActive:(UIApplication *)application {
// Called every time when app became active

[self.cache startSuspendResumeUpdateProcess];
}
```

## Методы управления кэш менеджером

Кэш менеджер имеет enum `RXCM_State`, который описывает текущие состояние кэша.
RXCM - является полностью асинхронным фраемворком. 

```objectivec
typedef NS_ENUM(NSInteger, RXCM_State) {

RXCM_UnknowState = 0,        // State of CacheManager is unknown
RXCM_ReadyToStartState,      // CacheManager is ready to start
RXCM_WorkingState,           // CacheManager is working
RXCM_SuccessFinishedState,   // Update was performed successful
RXCM_FailureFinishedState,   // Update was completed with an error
RXCM_SusspendState           // CacheManager was temporarily suspended
};
```

```objectivec
@interface RXCacheManager : NSObject
...
@property (nonatomic, assign, readonly) RXCM_State enumCacheState;

@end
```

Также вы можете остановить и возобновить работу кэша в любой момент. С помощью этих методов.  

| Метод                                                   | Объяснение                                                                                                                                                                                                                                          |
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| - (RXCM_State) startSuspendResumeUpdateProcess;         | Данный метод является аналогом кнопки play на плээре. Когда вы его вызываете, он будет сменять состояние кэша взависимости от текущего состояния.<br/>Например если процесс обновления сейчас приостановлен, он будет запусчен заново. И на оборот. |
| - (void) checkForMoreActualCacheVersionAndUpdateIfNeed; | Метод который вызывается для проверки новой версии и обновлении кэша если это требуется.                                                                                                                                                            |
| - (void) suspendCheckingAndUpdatingCacheProcess;        | Останавливает процесс обновления если он был запущен.                                                                                                                                                                                               |
| - (void) resumeCheckingAndUpdatingCacheProcess;         | Возобновляет процесс обновления если он был остановлен.                                                                                                                                                                                             |

Мы рекомендуем всегда использовать метод  `-startSuspendResumeUpdateProcess`, это сделает ваш код проще, потому что вам не нужно будет прописывать лишние логические конструкции которые отслеживают состояние кэша.

## Снипеты и дополнительные возможности

### 1. Регулируемый тип интернет содениения

Проперти `whatTypeOfConnectionCanDownloadCache` указывает кэш менеджеру на каком типе интернет соединения кэш может скачивать файлы.

По умолчанию это проперти равняется значению `RXCM_EveryTypeOfInternetConnection`.
Но если вы хотите экономить мобильные данные вашего пользователя, вы можете установить режим работы  `RXCM_WifiConnection`.

```objectivec
/* Enum describes the types of the Internet connection*/
typedef NS_OPTIONS(NSUInteger, RXCM_TypesOfInternetConnection) {
RXCM_NoneOfListed    = 1 << 0, // 0000 0001
RXCM_WifiConnection  = 1 << 1, // 0000 0010
RXCM_WWANConnection  = 1 << 2, // 0000 0100

RXCM_EveryTypeOfInternetConnection  = RXCM_WifiConnection | RXCM_WWANConnection
};
```

```objectivec
@interface RXCacheManager : NSObject
 
@property (nonatomic, assign) RXCM_TypesOfInternetConnection  
whatTypeOfConnectionCanDownloadCache;
@end
```

Если вы указали тип соединения `RXCM_WifiConnection` как единственный дозволенный, а потом внезапно пользователь переключился на `RXCM_WWANConnection`, то если вы реализовали поддержку протокола **RXCacheManagerHelperProtocol**  будет вызван метод

` -rxcm_isAllowToContinueDownloadingOnNewTypeOfInternetConnection`  в котором вы должны спросить у пользователя хочет ли он продолжить скачивание на новом тип соединения.

Пример реализации этого метода:
[Copy code](Documentation/TextSnippet/rxcm_isAllowToContinueDownloadingOnNewTypeOfInternetConnection.txt)![5cc8bbe38c361](/Documentation/ScreeSnippet/rxcm_isAllowToContinueDownloadingOnNewTypeOfInternetConnection.png)

Пример реализации метода который показывает **UIAlertViewController**:

[Copy code](Documentation/TextSnippet/showAlertIsContinueWorkOnNewTypeOfConnection.txt)![5cc8bc98834ca](/Documentation/ScreeSnippet/showAlertIsContinueWorkOnNewTypeOfConnection.png)

### 2. Печать логов в консоль

Установите значение `YES` в проперти`isPrintLogInConsole` чтобы видеть консоль логи.

### 3. Проверка наличия ошибок

Если процесс обновления кэш был завершен с ошибкой, то в можете получить подробную информацию в проперти `errorOccuredAtExecutionUpdate`.

Если вы хотите распечатать ошибку в консоль, то мы вам рекомендуем использовать метод convertErrorToString - который красиво оформит консоль лог.

```objectivec
-(NSString*) convertErrorToString:(NSError*)error;
```

### 4. Инициализация скаченных файлов с диска

Например может быть такая ситуация когда вы выполняете сетевой запрос, а интернет в это время пропал. Тогда вы можете успешно вытащить из памяти нужные вам данные.

**Инициализация NSString из текстового файла.**

[Copy code](Documentation/TextSnippet/InitNSStringFromSandbox.txt)![5cc97b7727e8d](/Documentation/ScreeSnippet/InitNSStringFromSandbox.png)

**Инициализация NSDictionary из json файла.**

[Copy code](Documentation/TextSnippet/initNSDictionaryFromSandbox.txt)![5cc97cafc2c8a](/Documentation/ScreeSnippet/initNSDictionaryFromSandbox.png)

**Инициализация UIImage из .png файла.**

[Copy code](Documentation/TextSnippet/initUIImageFromSandbox.txt)![5cc97e436f561](/Documentation/ScreeSnippet/initUIImageFromSandbox.png)

### 5. Удаление конфигурационных объектов из NSUserDefault

В такой способ вы можете удалить абсолютно все конфигурационные объекты. Это может понадобиться во время тестирования.

```objectivec
[UD    removeObjectForKey:kActualCacheStatus];
[UD    removeObjectForKey:kTempCacheStatus];
[UD    removeObjectForKey:kTempArrayOfOneChange];
[UD    synchronize];
```

**Инициализация портотипа cacheStatus.json и запись в память устройства.**

```objectivec
RXCMCacheStatus* status = [RXCMCacheStatus new];
status.actualCacheVersion  = @"1.0";
status.minimalCacheVersion = @"1.0";
[RXCacheManager saveObjecInUserDefault:status forKey:kActualCacheStatus];
```

## Автор
👨🏼‍💻 [@m1a7](github.com/m1a7) <br>
👌🏻 thisismymail03@gmail.com
