# Порождающие шаблоны:
1. Фабричный метод (Factory Method):
```c#
abstract class LessonFactory
{
    public abstract Lesson CreateLesson();
}

class VocabularyLessonFactory : LessonFactory
{
    public override Lesson CreateLesson()
    {
        return new VocabularyLesson();
    }
}

class SpeakingLessonFactory : LessonFactory
{
    public override Lesson CreateLesson()
    {
        return new SpeakingLesson();
    }
}
```
Код включает в себя реализацию шаблона проектирования "Фабричный метод". Этот шаблон позволяет делегировать создание экземпляров классов наследников суперкласса самим наследникам, что позволяет избежать прямой зависимости между клиентским кодом и конкретными классами, которые создаются.
В данном случае абстрактный класс LessonFactory определяет метод CreateLesson(), который должны реализовать его подклассы. Каждая конкретная фабрика, такая как VocabularyLessonFactory и SpeakingLessonFactory, реализует этот метод, создавая соответствующие уроки.


![Image alt](https://github.com/KurilovNV/PAPS/blob/LabWork6/Lab%20Work%20№6/doc/Factory.png)

2. Абстрактная фабрика (Abstract Factory):
```c#
abstract class LessonAbstractFactory
{
    public abstract Lesson CreateVocabularyLesson();
    public abstract Lesson CreateSpeakingLesson();
}

class EnglishLessonFactory : LessonAbstractFactory
{
    public override Lesson CreateVocabularyLesson()
    {
        return new VocabularyLesson();
    }

    public override Lesson CreateSpeakingLesson()
    {
        return new SpeakingLesson();
    }
}

class Program
{
    static void Main(string[] args)
    {
        LessonAbstractFactory englishLessonFactory = new EnglishLessonFactory();

        Lesson vocabularyLesson = englishLessonFactory.CreateVocabularyLesson();
        Lesson speakingLesson = englishLessonFactory.CreateSpeakingLesson();

        vocabularyLesson.Learn();
        speakingLesson.Learn();
    }
}
```
В данном примере существует абстрактный класс Lesson, который определяет общий интерфейс для всех типов уроков. Это абстракция, которая делает возможным работу с различными типами уроков через общий интерфейс.
Класс LessonAbstractFactory представляет собой абстрактную фабрику, определяющую интерфейс для создания семейства связанных уроков. Он содержит абстрактные методы CreateVocabularyLesson() и CreateSpeakingLesson(), которые должны быть реализованы конкретными фабриками.
В примере представлена одна конкретная реализация абстрактной фабрики - EnglishLessonFactory, которая создает уроки по английскому языку (VocabularyLesson и SpeakingLesson). Каждый метод этой фабрики создает конкретный тип урока, соответствующий семейству уроков.

3. Одиночка (Singleton):
```c#
public class LessonDatabase
{
    private static LessonDatabase instance;
    private LessonDatabase()
    {
        try
        {
            var connection = new SqlConnection(connectionString);
            connection.Open();
            var command = new SqlCommand(sqlCode, connection);
            command.ExecuteNonQuery();
            connection.Close();
        }
        catch (Exception ex)
        {
        }
    }
    public static LessonDatabase GetInstance()
    {
        if (instance == null)
        {
            instance = new LessonDatabase();
        }
        return instance;
    }
    public void SaveLessonData(string lessonData)
    {
        try
        {
            var connection = new SqlConnection("connectionString");
            connection.Open();
            var command = new SqlCommand(sqlCode, connection);
            command.Parameters.AddWithValue("@lessonData", lessonData);
            command.ExecuteNonQuery();
            connection.Close();
        }
        catch (Exception ex)
        {
        }
    }
}
```
Паттерн "Одиночка" (Singleton) используется здесь для того, чтобы гарантировать, что в приложении будет только один экземпляр класса LessonDatabase.
Класс LessonDatabase имеет приватный конструктор, что означает, что его объекты не могут быть созданы напрямую извне класса. Это предотвращает создание нескольких экземпляров класса.
В классе LessonDatabase есть приватное статическое поле instance, которое хранит единственный экземпляр класса.
Класс LessonDatabase имеет статический метод GetInstance(), который используется для получения единственного экземпляра класса. Если экземпляр еще не создан, метод создает новый экземпляр, в противном случае возвращает существующий экземпляр.

# Структурные шаблоны:
1. Декоратор (Decorator):
Шаблон проектирования "Декоратор" позволяет динамически добавлять новую функциональность существующему объекту без изменения его структуры. 
```c#
using System;

public interface IMessageSender
{
    void SendMessage(string message);
}

public class ConcreteMessageSender : IMessageSender
{
    public void SendMessage(string message)
    {
        Console.WriteLine("Message sent: " + message);
    }
}

public abstract class MessageDecorator : IMessageSender
{
    protected IMessageSender decoratedSender;

    public MessageDecorator(IMessageSender decoratedSender)
    {
        this.decoratedSender = decoratedSender;
    }

    public virtual void SendMessage(string message)
    {
        decoratedSender.SendMessage(message);
    }
}

public class EncodingDecorator : MessageDecorator
{
    public EncodingDecorator(IMessageSender decoratedSender) : base(decoratedSender) { }

    public override void SendMessage(string message)
    {
        string encodedMessage = EncodeMessage(message);
        base.SendMessage(encodedMessage);
    }

    private string EncodeMessage(string message)
    {
        return "Encoded: " + message;
    }
}

public class DecodingDecorator : MessageDecorator
{
    public DecodingDecorator(IMessageSender decoratedSender) : base(decoratedSender) { }

    public override void SendMessage(string message)
    {
        string decodedMessage = DecodeMessage(message);
        base.SendMessage(decodedMessage);
    }

    private string DecodeMessage(string message)
    {
        return message.Replace("Encoded: ", "");
    }
}
```
IMessageSender: определяет базовые операции для отправки сообщений. Он является основой для всех конкретных отправщиков и их декораторов.
ConcreteMessageSender: Это конкретная реализация интерфейса IMessageSender, представляющая базовую функциональность отправки сообщений.
MessageDecorator: Это абстрактный класс-декоратор, который реализует тот же интерфейс, что и IMessageSender, чтобы он мог быть использован в качестве отправщика сообщений. Этот класс содержит ссылку на другой объект IMessageSender и делегирует ему вызовы методов.
EncodingDecorator и DecodingDecorator: Это конкретные декораторы, которые расширяют базовый функционал IMessageSender, добавляя дополнительные функции кодирования и декодирования сообщений.
SendMessage метод: Каждый декоратор переопределяет метод SendMessage, чтобы добавить свою специфичную функциональность до или после вызова аналогичного метода базового декоратора.

2. Адаптер (Adapter):
Шаблон "Адаптер" используется, когда нужно соединить два несовместимых интерфейса.
```c#
using System;

public interface IMessageSender
{
    void SendMessage(string message);
}

public class ConcreteMessageSender : IMessageSender
{
    public void SendMessage(string message)
    {
        // Отправка сообщения
    }
}

public class LegacyMessageSender
{
    public void Send(string message)
    {
        // Отправка сообщения в старом формате
    }
}
public class LegacyMessageAdapter : IMessageSender
{
    private LegacyMessageSender legacySender;

    public LegacyMessageAdapter(LegacyMessageSender legacySender)
    {
        this.legacySender = legacySender;
    }

    public void SendMessage(string message)
    {
        legacySender.Send(message);
    }
}
```
В этом примере LegacyMessageAdapter выступает в роли адаптера, который позволяет использовать LegacyMessageSender вместо IMessageSender. Адаптер реализует интерфейс IMessageSender и делегирует вызовы метода SendMessage старому объекту LegacyMessageSender. Таким образом, мы можем использовать старый функционал в новом контексте, не изменяя его кода.

3. Компоновщик (Composite):
Шаблон проектирования "Компоновщик" позволяет клиентам обрабатывать отдельные объекты и их композиции (компоненты) единообразно. Этот шаблон позволяет создавать древовидные структуры объектов, где как отдельные объекты (листья), так и их совокупности (внутренние узлы) могут быть обработаны одинаковым образом.
```c#
public interface ILessonComponent
{
    void Learn();
}
public class Lesson : ILessonComponent
{
    private string lessonName;

    public Lesson(string name)
    {
        lessonName = name;
    }

    public void Learn()
    {
        //
    }
}

public class LessonGroup : ILessonComponent
{
    private string groupName;
    private List<ILessonComponent> children = new List<ILessonComponent>();

    public LessonGroup(string name)
    {
        groupName = name;
    }

    public void Add(ILessonComponent component)
    {
        children.Add(component);
    }

    public void Remove(ILessonComponent component)
    {
        children.Remove(component);
    }

    public void Learn()
    {
        foreach (var component in children)
        {
            component.Learn();
        }
    }
}
```
В этом примере у нас есть интерфейс ILessonComponent, который представляет как отдельные уроки (Lesson), так и группы уроков (LessonGroup). Каждый урок может быть либо конкретным уроком, либо группой уроков. 

4. Прокси (Proxy):
Шаблон проектирования "Прокси" предоставляет заместитель или placeholder для другого объекта, чтобы контролировать доступ к нему. Этот шаблон позволяет добавлять дополнительную логику перед доступом к реальному объекту.
```c#
public interface ILesson
{
    void Learn();
}

public class Lesson : ILesson
{
    private string lessonName;

    public Lesson(string name)
    {
        lessonName = name;
    }

    public void Learn()
    {
        //
    }
}

public class LessonProxy : ILesson
{
    private Lesson realLesson;
    private bool accessGranted;

    public LessonProxy(string name, bool access)
    {
        realLesson = new Lesson(name);
        accessGranted = access;
    }

    public void Learn()
    {
        if (accessGranted)
        {
            realLesson.Learn();
        }
        else
        {
           //
        }
    }
}
```
В этом примере у нас есть интерфейс ILesson, который предоставляет метод Learn() для изучения урока. Затем у нас есть класс Lesson, который реализует этот интерфейс и представляет конкретный урок.
Кроме того, у нас есть класс LessonProxy, который также реализует интерфейс ILesson. Этот класс действует как прокси для реального урока и добавляет дополнительную логику контроля доступа перед вызовом метода Learn().
# Поведенческие шаблоны:
1. Наблюдатель (Observer):
Шаблон проектирования "Наблюдатель" (Observer) используется в случаях, когда объекты зависят друг от друга таким образом, что изменения в одном объекте приводят к автоматическим обновлениям в других объектах
```c#
using System;
using System.Collections.Generic;

// Интерфейс для наблюдателя
public interface IObserver
{
    void Update(string lessonName, string status);
}
public interface ISubject
{
    void Attach(IObserver observer);
    void Detach(IObserver observer);
    void Notify(string lessonName, string status);
}

public class LessonTracker : ISubject
{
    private List<IObserver> observers = new List<IObserver>();

    public void Attach(IObserver observer)
    {
        observers.Add(observer);
    }

    public void Detach(IObserver observer)
    {
        observers.Remove(observer);
    }

    public void Notify(string lessonName, string status)
    {
        foreach (var observer in observers)
        {
            observer.Update(lessonName, status);
        }
    }

    public void LessonCompleted(string lessonName)
    {
        Notify(lessonName, "Completed");
    }
}

public class Student : IObserver
{
    private string name;

    public Student(string name)
    {
        this.name = name;
    }

    public void Update(string lessonName, string status)
    {
      //
    }
}
```
В этом примере LessonTracker является наблюдаемым объектом, а Student - наблюдателем. Когда урок завершается с помощью метода LessonCompleted, LessonTracker уведомляет всех подписанных наблюдателей (студентов) о завершении урока. Каждый студент реализует метод Update, который определяет, как реагировать на уведомления. 

2. Состояние (State):
Шаблон проектирования "Состояние" (State) используется для моделирования объекта таким образом, что его поведение изменяется в зависимости от его внутреннего состояния. 
```c#
public interface IState
{
    void Handle();
}

public class LearningState : IState
{
    public void Handle()
    {
       //
    }
}

public class IdleState : IState
{
    public void Handle()
    {
       //
    }
}

public class Application
{
    private IState currentState;

    public Application()
    {
        currentState = new IdleState();
    }

    public void SetState(IState state)
    {
        currentState = state;
    }

    public void Request()
    {
        currentState.Handle();
    }
}
```
В этом примере у нас есть интерфейс IState, представляющий состояние, а также конкретные реализации состояний: LearningState и IdleState. Контекст приложения Application использует текущее состояние для выполнения соответствующих операций. Когда состояние меняется с помощью метода SetState, поведение приложения также изменяется в соответствии с новым состоянием. 
3. Итератор (Iterator):
Шаблон проектирования "Итератор" (Iterator) предоставляет способ последовательного доступа к элементам коллекции без раскрытия ее внутренней структуры. Этот шаблон полезен, когда нужно обходить коллекцию и работать с ее элементами, не зная, как именно она реализована.
```c#
public interface IIterator<T>
{
    T Current { get; }
    bool HasNext();
    void Next();
}
public class LessonIterator : IIterator<string>
{
    private List<string> lessons;
    private int position = 0;

    public LessonIterator(List<string> lessons)
    {
        this.lessons = lessons;
    }

    public string Current
    {
        get { return lessons[position]; }
    }

    public bool HasNext()
    {
        return position < lessons.Count - 1;
    }

    public void Next()
    {
        position++;
    }
}

public class LessonCollection
{
    private List<string> lessons = new List<string>();

    public void AddLesson(string lesson)
    {
        lessons.Add(lesson);
    }

    public IIterator<string> GetIterator()
    {
        return new LessonIterator(lessons);
    }
}
```
В этом примере у нас есть интерфейс IIterator<T>, который представляет методы для доступа к элементам коллекции, такие как Current, HasNext и Next. Класс LessonIterator реализует этот интерфейс для конкретной коллекции уроков. LessonCollection предоставляет метод GetIterator, который возвращает итератор для обхода уроков. Когда мы получаем итератор с помощью GetIterator и проходимся по урокам с помощью HasNext и Next, мы можем последовательно получить доступ к каждому уроку в коллекции, не зная о ее внутренней структуре. 
4. Стратегия (Strategy):
Шаблон проектирования "Стратегия" (Strategy) позволяет определить семейство алгоритмов, инкапсулировать каждый из них и обеспечить их взаимозаменяемость. Это позволяет выбирать алгоритм во время выполнения программы независимо от клиента, который его использует.
```c#

public interface ILearningStrategy
{
    void Learn();
}
public class VocabularyLearningStrategy : ILearningStrategy
{
    public void Learn()
    {
       //
    }
}

public class SpeakingLearningStrategy : ILearningStrategy
{
    public void Learn()
    {
       //
    }
}

public class LearningContext
{
    private ILearningStrategy learningStrategy;

    public LearningContext(ILearningStrategy strategy)
    {
        learningStrategy = strategy;
    }

    public void SetStrategy(ILearningStrategy strategy)
    {
        learningStrategy = strategy;
    }

    public void ExecuteLearning()
    {
        learningStrategy.Learn();
    }
}
```
В этом примере у нас есть интерфейс ILearningStrategy, представляющий метод Learn, который реализуют конкретные стратегии обучения, такие как VocabularyLearningStrategy и SpeakingLearningStrategy. Контекст LearningContext использует текущую стратегию для выполнения обучения. Когда стратегия меняется с помощью метода SetStrategy, контекст автоматически переключается на новую стратегию.
5. Команда
Шаблон проектирования "Команда" (Command) представляет собой поведенческий шаблон, который превращает запросы или операции в объекты, позволяя передавать их как аргументы, хранить их в стеке, выполнять и отменять.
```c#
public interface ICommand
{
    void Execute();
}

public class AddLessonCommand : ICommand
{
    private LessonRepository repository;
    private Lesson lessonToAdd;

    public AddLessonCommand(LessonRepository repository, Lesson lesson)
    {
        this.repository = repository;
        this.lessonToAdd = lesson;
    }

    public void Execute()
    {
        repository.AddLesson(lessonToAdd);
    }
}
public class CompleteLessonCommand : ICommand
{
    private LessonRepository repository;
    private string lessonName;

    public CompleteLessonCommand(LessonRepository repository, string lessonName)
    {
        this.repository = repository;
        this.lessonName = lessonName;
    }

    public void Execute()
    {
        repository.CompleteLesson(lessonName);
    }
}

public class LessonRepository
{
    private List<Lesson> lessons = new List<Lesson>();

    public void AddLesson(Lesson lesson)
    {
        lessons.Add(lesson);
        Console.WriteLine($"Lesson '{lesson.Name}' added.");
    }

    public void CompleteLesson(string lessonName)
    {
        Lesson lesson = lessons.Find(l => l.Name == lessonName);
        if (lesson != null)
        {
          //
        }
    }
}

public class Lesson
{
    public string Name { get; set; }

    public Lesson(string name)
    {
        Name = name;
    }
}

public class LessonInvoker
{
    private List<ICommand> commands = new List<ICommand>();

    public void SetCommand(ICommand command)
    {
        commands.Add(command);
    }

    public void ExecuteCommands()
    {
        foreach (var command in commands)
        {
            command.Execute();
        }
        commands.Clear();
    }
}
```
В этом примере у нас есть интерфейс ICommand, представляющий метод Execute, который реализуют конкретные команды, такие как AddLessonCommand и CompleteLessonCommand. Класс LessonInvoker представляет собой объект, который выполняет команды. Когда команда добавляется в LessonInvoker с помощью метода SetCommand, она сохраняется в стеке и может быть выполнена позже с помощью метода ExecuteCommands.
