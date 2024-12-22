# Анализ данных в разработке игр
Отчет по лабораторной работе #3 выполнила:
- Муштей Виолетта Викторовна
- НМТ-232203

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Разработать оптимальный баланс нанесения урона оружием для игры Save RTF.

## Задание 1
### Расширьте варианты доступного оружия в игре. Используйте шаблон таблицы для визуализации оружия игры Save RTF.
Ход работы:
- Расширим варианты оружия добавив лук, катану и базуку.
![Таблица параметров оружия](./table.png)  
- Рассмотрим особенности каждого оружия в отдельности. Лук оптимален на средней дистанции, урон выше среднего из-за небольшой задержки перед выстрелом (необходимость натягивать тетиву).
- Катана. Оружие ближнего боя имеет 100% шанс попадание на расстоянии 1 и 2.
- Базука. Оружие нацеленное на дальнюю дистанцию. Урон самый высокий игре предлагается сбалансировать стоимостью самого оружия и патронов.
![График урона оружия](./viz.png)  

## Задание 2
### Визуализируйте параметры оружия в таблице. Используйте шаблон таблицы для визуализации оружия игры Save RTF. Постройте примеры для следующих математических величин (см. пример в презентации):
- Среднеквадратическое отклонение (СКО)
- Разброс урона оружия
- Вариативность времени отклика игрока (реакция на события)  
Ход работы:
- Базука  
![СКО для базуки](./sko/sko_bazuka.png)  
![Разброс урона для базуки](./dmg/dmg_bazuka.png)  
- Лук  
![СКО для лука](./sko/sko_bow.png)  
![Разброс урона для лука](./dmg/dmg_bow.png)  
- Катана  
![СКО для катаны](./sko/sko_katana.png)  
![Разброс урона для катаны](./dmg/dmg_katana.png)  
- Пулемет  
![СКО для пулемета](./sko/sko_machinegun.png)  
![Разброс урона для пулемета](./dmg/dmg_machinegun.png)  
- Пистолет  
![СКО для пистолета](./sko/sko_pistol.png)  
![Разброс урона для пистолета](./dmg/dmg_pistol.png)  
- Винтовка  
![СКО для винтовки](./sko/sko_rifle.png)  
![Разброс урона для винтовки](./dmg/dmg_rifle.png)  
- Дробовик  
![СКО для дробовика](./sko/sko_shotgun.png)  
![Разброс урона для дробовика](./dmg/dmg_shotgun.png)  
- Снайперка  
![СКО для базуки](./sko/sko_sniper.png)  
![Разброс урона для базуки](./dmg/dmg_sniper.png)  
- Вариативность времени отклика игрока (реакция на события)  
![Отклик игрока](./rpTime/rp.png)  

## Задание 3
### Решение в 80+ баллов должно визуализировать данные из google-таблицы, и с помощью Python передавать переменные в проект Unity. В Python данные также должны быть визуализированы.
Ход работы:
- С помощью скрипта на python заполним таблицу, в которой укажем вероятность попадания из лука в зависимости от расстояния.
``` python
import gspread
import numpy as np

gc = gspread.service_account(filename='lab-3-c8291552863c.json')
sh = gc.open('lab-3')
worksheet = sh.sheet1
sh.sheet1.clear() 

total= 10
bow = [6, 5, 3, 2, 2, 2, 2, 3, 5, 6]

# Заголовки таблицы
row = 1
sh.sheet1.update_acell('A' + str(row), 'Расстояние')
sh.sheet1.update_acell('B' + str(row), 'Вероятность')

for i in range(total):
    row += 1
    ver = round(1-((bow[i]-1)/6), 3)
    sh.sheet1.update_acell('A' + str(row), str(i+1))
    sh.sheet1.update_acell('B' + str(row), str(ver))
```
- Визуализируем данные.  
![Визуализация](./viz2.png)  
- Передадим полученные данные в Unity и в зависимости от вероятности будем воспроизводить звук. Меньше 50% - не попал, больше 50% - попал.
``` C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 50 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 50 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1O386gPuwBCap3aiPN1dM4uxYrGbxThjB_1dTw5AUWw/values/Лист1?key=AIzaSyDJYHFa5HwWAo9PF6pSMwaie9CwnRkNDdU");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;

            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[1]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
```

- Запуск и проверка:
![Вывод в консоль](./console.png)
## Выводы
- Углубилась в разработку баланса.
- Визуализировала разработанные данные.
- В Unity на основе разработанных данных воспроизводила звуки.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**