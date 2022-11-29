# РАЗРАБОТКА СИСТЕМЫ МАШИННОГО ОБУЧЕНИЯ
Отчет по лабораторной работе #3 выполнил(а):
- Борщевская Жанна Александровна
- РИ-210911
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | # | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;
## Цель работы 
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.
## Задание 1
Создала новый пустой 3D проект на Unity.
Скачала папку с ML агентом.
В созданный проект добавила ML Agent, выбрав Window - Package Manager - Add Package from disk. Последовательно добавьте .json – файлы:
o ml-agents-release_19 / com,unity.ml-agents / package.json
o	ml-agents-release_19 / com,unity.ml-agents.extensions / package.json
![1](https://user-images.githubusercontent.com/114568072/204621527-05c87a6b-379f-44d4-8823-2d6dd5f53a51.jpg)
Далее запускаем Anaconda Prompt для возможности запуска команд через консоль.
Далее пишем серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек:
o	mlagents 0.28.0;
o	torch 1.7.1;
![2](https://user-images.githubusercontent.com/114568072/204621783-fc87e805-7a22-46a2-997b-e24d436d4446.jpg)
![3](https://user-images.githubusercontent.com/114568072/204621846-81be38b2-53a4-4493-a38a-532403020ee5.jpg)
Создайте на сцене плоскость, куб и сферу так, как показано на рисунке ниже. Создайте простой C# скрипт-файл и подключите его к сфере:
![4](https://user-images.githubusercontent.com/114568072/204622097-f7cf003b-2ced-4bdd-8377-a73091822360.jpg)
В скрипт-файл RollerAgent.cs добавьте код, опубликованный в материалах лабораторных работ 
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}

```

Запустите работу ml-агента
![5](https://user-images.githubusercontent.com/114568072/204622377-d4f1e3f2-80ca-4154-a9d3-465d00a62d2c.jpg)
Вернитесь в проект Unity, запустите сцену, проверьте работу ML-Agent’a.
Сделайте 3, 9, 27 копий модели «Плоскость-Сфера-Куб», запустите симуляцию сцены и наблюдайте за результатом обучения модели.
![6](https://user-images.githubusercontent.com/114568072/204622604-4aab1a65-4a4f-4bab-bef4-ec9507e5ed43.jpg)
![7](https://user-images.githubusercontent.com/114568072/204622676-75cc9c81-9df6-469a-8087-2a063c5e5138.jpg)
![8](https://user-images.githubusercontent.com/114568072/204622717-8b95c5ed-c640-4d4f-b2e5-51414236aa77.jpg)
## Выводы
В данной лабораторной работе я создала ML-агент и натренировала нейросеть, задача которой заключалась в управлении шаром. Задача шара заключалась в том, чтобы оставаться на плоскости находить кубик, смещающийся в заданном случайном диапазоне координат. Шар стал догонять кубик.

