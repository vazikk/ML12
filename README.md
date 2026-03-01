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

<br>

<br>

4. Оптимизировать model inference (quantization, pruning, distillation) <br>

   1) quantization - метод уменьшения точности чисел (Например, не 32 цифра после запятой, а 4) <br>

   Это способ со своей моделью: <br>
  ```
   quant_config = {
    "zero_point": True,      # Включаем точное представление нуля 
    "q_group_size": 128,     # Оптимальный размер группы 
    "w_bit": 4,              # 4 бита на вес 
    "version": "GEMM"        # Версия квантования
   }
  ```
   <br>

   Так же можно делать уже с готовыми моделями в AWS через SageMaker <br>


   2) Pruning - удаление лишних весов <br>
   Пример: <br>
   
      ```
      for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear):  # Тип модели
        prune.l1_unstructured(module, name='weight', amount=0.3)  #Где// что удаляем // какой объём
        prune.remove(module, 'weight') # Циклиность
      ```


   3) Distillation - обучение маленькой модели имитировав работу большой модели <br>

   Пример с использованием готовых моделей которые есть в AWS: <br>
   
   <img width="1516" height="593" alt="image" src="https://github.com/user-attachments/assets/db263e79-ebb6-4af1-bea2-bd327badf0b7" /> <br>

   <img width="935" height="445" alt="image" src="https://github.com/user-attachments/assets/aaa7de30-8115-4c99-b10b-178bceaccf36" /> <br>

   <img width="1078" height="835" alt="image" src="https://github.com/user-attachments/assets/b79a2c27-57e5-4ab9-9acd-2d7b7063001c" /> <br>

   <br> 

   Вот пример для своих моделей: 
   ```
      optimizer = torch.optim.Adam(student.parameters(), lr=0.001)
      temperature = 3.0  # можно менять (2.0-5.0)
      num_epochs = 10

      teacher.eval()  # учитель не обучается
      student.train()

      print("Начинаем дистилляцию...")

      for epoch in range(num_epochs):
          total_loss = 0
    
          for batch_X, batch_y in train_loader:
              batch_X = batch_X.to(device)
        
              # Учитель генерирует ответы
              with torch.no_grad():
                  teacher_outputs = teacher(batch_X)
              
              # Студент учится
              student_outputs = student(batch_X)
              
              # Считаем distillation loss
              loss = distillation_loss(student_outputs, teacher_outputs, temperature)
              
              # Обратное распространение
              optimizer.zero_grad()
              loss.backward()
              optimizer.step()
        
              total_loss += loss.item()
    
          print(f"Эпоха {epoch+1}/{num_epochs}, Loss: {total_loss/len(train_loader):.4f}")

   ```

<br>

<br>

5. Использовать graduated storage tiers (hot/cool/archive) <br>

Для каждого s3 можно настроить хранение: <br>

<img width="848" height="576" alt="image" src="https://github.com/user-attachments/assets/2fb14549-a053-4174-a119-9f005c2efaf8" /> <br>

<img width="778" height="708" alt="image" src="https://github.com/user-attachments/assets/d0d166f5-ad59-42e6-b346-2c718c7b71df" /> <br>

<br>

6. Внедрить cost monitoring и budgets alerts <br>

Для оповещения о приближения к бюджету можно настроить Budgets <br>

<img width="1195" height="221" alt="image" src="https://github.com/user-attachments/assets/d80f42a9-25d3-4c07-a2f3-474b9e38fb1d" /> <br>

Так же можно через Cost explorer настраивать и смотреть дашборды. <br>

И для ML можно настроить Cost Anomaly Detection, он будет анализировать историю расходов и смотреть аномалии (резкие скачки, падения, остановки) в тратах и сообщать о них. <br>

<img width="1829" height="700" alt="image" src="https://github.com/user-attachments/assets/d3ab3ca7-d547-4385-91f6-7ab2dbb73a3b" /> <br>



 





    






































