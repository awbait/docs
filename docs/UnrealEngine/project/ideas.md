# Ideas

Сгенерировать огромную карту (для начала можно ограничиться каким-нибудь куском), далее при подключении к игре (серверу), у игрока будет возможность купить себе участок для строительства за N-сумму.


При этом есть несколько вариантов покупки участков, первый вариант:
- Покупка большого участка на последующее деление на более мелкие участки
  - У такого варианта, есть возможность разделить территорию на разные куски, и например провести дороги.
- Сразу мелкие участки для постройки чего либо

Что мы имеем на данный момент:
 - BaseSplineActor - имеет внутри себя какой-то сплайн с N кол-вом точек (BaseSpline)
 - Далее при наведении мышки на этот сплайн, мы должны её снапить к нему
 - При нажатии ЛКМ мы устанавливаем SplinePoint
 - Далее мы в реалтайме должны видеть куда тащим линию (постоянно обновлять последнюю точку на координатах мышки?)

 - Так же необходим DecalGenerator, который будет рисовать линии на сплайне


Идеи:

- Можно купить или арендовать кабинет, или какое-либо место под постройку чего либо
- Допустим кто-то построил магазин, и может продавать там места под постройку
