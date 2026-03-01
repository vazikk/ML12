# ML12
1. Audit текущих compute и storage costs <br>

По сути посмотреть все текущие расходы монжо просто в Billing and Cost Management -> Bills или Cost explorer, но для большего удобства, подробностей и сохранения истоиии застрат больше чем за год можно созать Data Export,  который будет сохранять всю инфу о затратах в S3 <br>

<img width="1520" height="537" alt="image" src="https://github.com/user-attachments/assets/8ebfbe2d-3332-4905-9cd9-d824da43ba56" /> <br>
<img width="1502" height="642" alt="image" src="https://github.com/user-attachments/assets/c68a16d1-0011-4033-9595-771bb5d7d2f2" /> <br>

<img width="1727" height="573" alt="image" src="https://github.com/user-attachments/assets/889463af-6493-4989-8328-d02fd3af3412" /> <br>
Вот тут будут появляться отчеты в том формате который мы выбрали (у меня ничего нет потому что создание и загрузка первого отчета происходит от 24 до 48 часов) <br>

Для большего удобства делегирования затрат нужно испоьлзовать Tags (я не смогу показать, потому что это должен payment account делать) <br>
<br>

<br>

2.Реализовать spot instances для training jobs <br>

Собственно это можно настроить в Sagemaker <br>

<img width="946" height="306" alt="image" src="https://github.com/user-attachments/assets/3f9dc22d-47b4-42ce-b6cd-d69662eb2396" /> <br>
<img width="1101" height="431" alt="image" src="https://github.com/user-attachments/assets/d8e44edc-bb68-424d-87ff-fa414b07bb5f" /> <br>

Уведомление о необходимости освободить ресурс будет приходить на почту за 2 минуты до того как AWS остановит job <br>

<br>

<br>

3. Настроить auto-shutdown для idle resources <br>


Продемонстрирую 2 метода:
1) Остновка и запуск сервисов по расписанию: <br>

<img width="1680" height="672" alt="image" src="https://github.com/user-attachments/assets/a0ec5592-e24f-4678-9915-a6dee5bd7ebf" /> <br>
<img width="1525" height="689" alt="image" src="https://github.com/user-attachments/assets/128d0ded-e331-46f3-a73c-1fcea1b2596d" /> <br>
<img width="1487" height="565" alt="image" src="https://github.com/user-attachments/assets/e07600a1-3319-4002-9662-7c9ddeca4f0d" /> <br>
<img width="1617" height="768" alt="image" src="https://github.com/user-attachments/assets/abd2e096-6de8-4cb1-9e10-6eeb9cd8b36d" /> <br>

2) Остановка сервисов на основе метрик: <br>
   
<img width="1895" height="733" alt="image" src="https://github.com/user-attachments/assets/3328535d-ee93-40a6-96c8-7597c878620e" /> <br>
<img width="1488" height="563" alt="image" src="https://github.com/user-attachments/assets/035cb169-6da3-4de1-824a-f0616211f75b" /> <br>







































