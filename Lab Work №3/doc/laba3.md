# Лабораторная работа 3

# Диаграмма компонентов
 ![Image alt](https://github.com/KurilovNV/PAPS/blob/LabWork3/Lab%20Work%20№3/doc/Диаграмма компонентов.png)


 # Диаграмма последовательностей



# Модель БД
 ![Image alt](https://github.com/KurilovNV/PAPS/blob/LabWork3/Lab%20Work%20№3/doc/База данных.png)

 # Применение основных принципов разработки
- ## DRY.
  Функция CheckAnswer принимает два аргумента: предложенный ответ (proposedAnswer) и правильный ответ (correctAnswer).
  Оба ответа преобразуются в нижний регистр с помощью метода ToLower, чтобы сравнение происходило без учета регистра.
  После этого функция сравнивает оба ответа и возвращает результат сравнения.
  Такая реализация позволяет избежать дублирования кода при сравнении ответов на различные задания, соответствуя принципу DRY.
  ```c#
  class TaskChecker
  {
    public static bool CheckAnswer(string proposedAnswer, string correctAnswer)
    {
        string proposedAnswerLower = proposedAnswer.ToLower();
        string correctAnswerLower = correctAnswer.ToLower();
        return proposedAnswerLower == correctAnswerLower;
     }
  }
  ```

  - ## YAGNI.
  Функция SendMessage принимает идентификатор чата пользователя (chatId) и текст сообщения (message).
  Формируется URL для отправки сообщения через API телеграм-бота.
  Создается объект WebClient для выполнения HTTP-запроса к API телеграм-бота.
  Выполняется GET-запрос к API сформированным URL.
  Полученный ответ проверяется на наличие ошибок.
  Это минимальная реализация функции для отправки сообщения, соответствующая принципу YAGNI (You Ain't Gonna Need It).
    ```c#
    public static void SendMessage(long chatId, string message)
    {
        string apiUrl = $"https://api.telegram.org/bot{BotToken}/sendMessage?chat_id={chatId}&text={WebUtility.UrlEncode(message)}";

        using (WebClient client = new WebClient())
        {
            try
            {
                string response = client.DownloadString(apiUrl);
                Console.WriteLine($"Сообщение отправлено пользователю {chatId}: {message}");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Ошибка при отправке сообщения: {ex.Message}");
            }
        }
    }
    ```
    
  - ## KISS
  Функция AddNoiseToAudio принимает массив байтов, представляющий аудио файл, и просто выводит сообщение о добавлении шума к аудио файлу. Нет излишней сложности или деталей, это соответствует принципу KISS.
    ```c#
    class AudioProcessor
    {
        public static byte[] AddNoiseToAudio(byte[] audioData)
        {
            Console.WriteLine("Noise added to audio file.");
            Random random = new Random();
            byte[] noise = new byte[audioData.Length];
            random.NextBytes(noise);
            for (int i = 0; i < audioData.Length; i++)
            {
                audioData[i] = (byte)(audioData[i] ^ noise[i]);
            }
            return audioData;
        }
     }
    ```

  
# SOLID:
Класс UserRepository отвечает за сохранение и загрузку пользователей из базы данных. 
Класс User представляет собой модель пользователя.
Каждый класс отвечает за свою область ответственности, что соответствует принципу единственной обязанности (Single Responsibility Principle) из SOLID.
  ```c#
  class UserRepository
  {
       private string _filePath;
   
       public UserRepository(string filePath)
       {
           _filePath = filePath;
       }
       public void SaveUser(User user)
       {
           List<User> users = LoadUsers();
           users.Add(user);
           SaveUsers(users);
       }
       public List<User> LoadUsers()
       {
           if (!File.Exists(_filePath))
               return new List<User>();
   
           string json = File.ReadAllText(_filePath);
           return JsonConvert.DeserializeObject<List<User>>(json);
       }
   
       private void SaveUsers(List<User> users)
       {
           string json = JsonConvert.SerializeObject(users);
           File.WriteAllText(_filePath, json);
       }
   }
  class User
  {
      public long ChatId { get; set; }
      public string Username { get; set; }
  }
  ```
