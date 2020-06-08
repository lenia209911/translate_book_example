# Модуль 3. Topics часть1

![Kobuki](../.gitbook/assets/image%20%2818%29.png)

![](../.gitbook/assets/image%20%282%29.png)

• ROSject Link: [https://bit.ly/2MBJDb2](https://bit.ly/2MBJDb2) 

• Робот: Turtlebot 2

Предполагаемое время для завершения: 2,5 часа Симуляция: Turtlebot 2 Что вы изучите с этим устройством? 

• Какие темы и как ими управлять 

• Что такое издатель и как его создать 

• Что такое тематические сообщения и как они работают

![Kobuki Robot](../.gitbook/assets/image%20%2810%29.png)

## Часть 1: Издатель\(Publisher\) 

Пример 2.1 

• Создайте новый пакет с именем topic\_publisher\_pkg. При создании пакета добавьте rclcpp и std\_msgs в качестве зависимостей. 

• Внутри папки src пакета создайте новый файл с именем simple\_topic\_publisher.cpp. Внутри этого файла скопируйте содержимое файла simple\_topic\_publisher.cpp 

• Создать файл запуска для запуска этого кода. • Сделайте необходимые изменения в вашем файле CMakeLists.txt и скомпилируйте пакет. 

• Запустите файл запуска для запуска вашего исполняемого файла. 

Данные для примера 2.1

1. Помните, что для того, чтобы иметь возможность использовать инструменты командной строки ROS2, вам сначала необходимо правильно создать исходную среду с помощью следующей команды:

```text
[ ]: source /opt/ros/crystal/setup.bash
```

2. Чтобы выполнить это упражнение, вы можете просто выполнить те же действия, которые вы использовали в предыдущем глава. Это почти то же самое. 

3. Помните, что для создания пакета с roscpp и std\_msgs в качестве зависимостей, вы должны использовать команду, подобную приведенной ниже:

```text
[ ]: ros2 pkg create topic_publisher_pkg --build-type ament_cmake --dependencies rclcpp std_msgs
```

4. Строки для добавления в файл CmakeLists.txt могут выглядеть примерно так:

```text
[ ]: add_executable(simple_publisher_node src/simple_topic_publisher.cpp) 
     ament_target_dependencies(simple_publisher_node rclcpp std_msgs)
     install(TARGETS simple_publisher_node DESTINATION lib/${PROJECT_NAME})
     # Install launch files.
     install(DIRECTORY launch DESTINATION share/${PROJECT_NAME}/ )
```

### C++ программа {2.1}:simple\_topic\_publisher.cpp

```text
[ ]:#include "rclcpp/rclcpp.hpp" 
    #include "std_msgs/msg/int32.hpp"
    int main(int argc, char * argv[])
    {
        rclcpp::init(argc, argv);
        auto node = rclcpp::Node::make_shared("simple_publisher"); auto publisher =
        node->create_publisher<std_msgs::msg::Int32>("counter"); auto message = std::make_shared<std_msgs::msg::Int32>(); message->data = 0;
        rclcpp::WallRate loop_rate(2);
        while (rclcpp::ok()) {
        publisher->publish(message); message->data++; rclcpp::spin_some(node); loop_rate.sleep();
    } 
    rclcpp::shutdown(); 
    return 0;
}
```

Ничего не случилось? Ну... это не совсем так! Вы только что создали тему с именем **/counte**r и опубликовали через нее целое число, которое увеличивается бесконечно. Давайте проверим некоторые вещи

**Topic** как труба. **Узлы используют темы для публикации информации для других узлов, чтобы они могли общаться.** Вы можете в любое время узнать количество тем в системе, выполнив **ros2 topic list**. Вы также можете проверить на конкретную тему. 

На вашей веб-оболочке введите **ros2 topic list** и найдите topic с именем «/counter». 

Выполнить в WebShell \# 1

```text
[ ]: ros2 topic list | grep  '/counter'
```

Вывод WebShell \# 1

```text
[ ]: user ~ $ ros2 topic list | grep '/counter' /counter
```

Здесь вы только что перечислили все темы, запущенные сейчас, и отфильтровали с помощью команды grep те, которые содержат слово /counter. Если он появляется, значит, тема работает так, как должна. Вы можете запросить информацию о теме, выполнив **ros2 topic info.**

Теперь введите **ros2 topic info /counter.** 

Выполнить в WebShell \# 1

```text
[ ]: ros2 topic info /counter
```

Вывод WebShell \# 1

```text
[ ]: user:~$ ros2 topic info /counter Topic: /counter
     Publisher count: 1
     Subscriber count: 0
```

В выводе указывается название темы и количество издателей / подписчиков, которые есть в теме. На данный момент, как вы можете видеть, у него есть только один издатель, который является нашей программой. 

Теперь введите rostopic echo /counter и проверьте вывод темы в режиме реального времени. 

Выполнить в WebShell \# 1

```text
[ ]: ros2 topic echo /counter
```

Вы должны увидеть последовательность последовательных чисел, похожую на следующую:

```text
[ ]: user:~$ ros2 topic echo /counter 
     data: 59
     data: 60
     data: 61
     data: 62
     data: 63
     data: 64
     data: 65
     data: 66
     data: 67
```

Итак... что только что произошло? Давайте объясним это более подробно. Во-первых, давайте раскрошим код, который мы выполнили. Вы можете проверить комментарии в коде ниже, объясняя, что делает каждая строка кода:

```text
[ ]: // Импортируйте все необходимые библиотеки ROS и импортируйте сообщение Int32
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/int32.hpp"
using namespace std::chrono_literals;
int main(int argc, char * argv[])
{
     rclcpp::init(argc, argv);
     // Инициируем узел с именем simple_publisher
     auto node = rclcpp::Node::make_shared("simple_publisher");
     // Создание объекта Publisher, который будет публиковаться в topic /counter
     , сообщения типа Int32
     auto publisher =node->create_publisher<std_msgs::msg::Int32>("counter"); // Создаем переменную с именем 'message' типа Int32
     auto message = std::make_shared<std_msgs::msg::Int32>(); // Инициализируем переменную 'message'
     message->data = 0;
     // Установить частоту публикации 2 Гц
     rclcpp::WallRate loop_rate(2);
     // Создать цикл, который будет идти, пока кто-то не остановит программу
     execution
     while (rclcpp::ok()) {
          // Опубликуем сообщение в переменной 'message' 
          variable publisher->publish(message);
          // Увеличиваем переменную 'message'
          message->data++;
          rclcpp::spin_some(node);
          // Убедитесь, что частота публикации поддерживается на уровне 2 Гц 
          loop_rate.sleep();
     } 
     rclcpp::shutdown(); 
     return 0;
}
```

Таким образом, в основном этот код инициирует узел и создает издателя, который продолжает публиковать последовательность последовательных целых чисел в теме «/counter». Подводя итог: 

Издатель - это узел, который продолжает публиковать сообщения в теме. Так что ... что же **topic**?

Topic - это канал, который действует как канал, где другие узлы ROS могут публиковать или читать информацию. Давайте теперь посмотрим некоторые команды, связанные с темами \(некоторые из них вы уже использовали\). 

Чтобы получить список доступных тем в системе ROS, вы должны использовать следующую команду:

```text
[ ]: ros2 topic list
```

Чтобы прочитать информацию, публикуемую в теме, используйте следующую команду:

```text
[ ]: ros2 topic echo <topic_name>
```

Эта команда начнет печатать всю информацию, которая публикуется в теме. 

Чтобы получить информацию по определенной теме, используйте следующую команду:

```text
[ ]: ros2 topic info <topic_name>
```

Наконец, вы можете проверить различные параметры команды rostopic, используя следующую команду:

```text
[ ]: ros2 topic -h
```

## Состав узла\(node\)

В предыдущем примере вы проверили скрипт C ++ с именем simple\_topic\_publisher.cpp. Этот скрипт был написан с использованием метода программирования старой школы. Мы говорим это потому, что это очень похоже на то, как вы написали бы этот скрипт в ROS1. В ROS2 этот стиль кодирования станет устаревшим. И вы можете спросить... Почему? Ну, это из-за композиции

В ROS2, как заметное отличие от ROS1, введена концепция Composition. По сути, это означает, что у вас будет возможность создавать \(выполнять\) несколько узлов в одном процессе. Вы можете прочитать больше об этом здесь: [https://index.ros.org/doc/ros2/Composition/](https://index.ros.org/doc/ros2/Composition/).Однако, чтобы иметь возможность использовать композицию узлов, вам необходимо программировать свои сценарии более объектно-ориентированным способом. Итак, первый проверенный вами скрипт, simple\_topic\_publisher.cpp, не сможет использовать композицию узлов.

Ниже вы можете взглянуть на скрипт, который делает то же самое, но он закодирован с использованием компонуемого метода с использованием классов.

### C++ программа {2.1b}: simple\_topic\_publisher\_composable.cpp

```text
[ ]:#include "rclcpp/rclcpp.hpp" 
    #include "std_msgs/msg/int32.hpp" 
    #include <chrono>
    using namespace std::chrono_literals;
/ * Этот пример создает подкласс Node и использует std :: bind () для регистрации
* функция-член в качестве обратного вызова от таймера. * /
    class SimplePublisher : public rclcpp::Node 
    {
    public:
    SimplePublisher()
    : Node("simple_publisher"), count_(0) {
    publisher_ = this->create_publisher<std_msgs::msg::Int32>("counter");
    timer_ = this->create_wall_timer(
    500ms, std::bind(&SimplePublisher::timer_callback, this));
    }
    private:
    void timer_callback() {
    auto message = std_msgs::msg::Int32(); message.data = count_;
    count_++;
    publisher_->publish(message); }
    rclcpp::TimerBase::SharedPtr timer_; rclcpp::Publisher<std_msgs::msg::Int32>::SharedPtr publisher_; size_t count_;
};
     int main(int argc, char * argv[])
     {
     rclcpp::init(argc, argv); 
     rclcpp::spin(std::make_shared<SimplePublisher>()); 
     rclcpp::shutdown();
     return 0;
}
```

### Анализ кода

Сначала мы определяем наш класс, который наследуется от класса rclcpp :: Node.

```text
[ ]: class SimplePublisher : public rclcpp::Node
```

Далее у нас есть конструктор нашего класса:

```text
[ ]: SimplePublisher()
```

Внутри конструктора мы инициализируем наш узел, вызывая конструктор супер класса Узел, а также инициализация переменной с именем count\_ в 0.

```text
[ ]: : Node("simple_publisher"), count_(0)
```

Также в конструкторе мы создаем наши объекты publisher __и __timer. Как вы увидите позже, они фактически создаются в закрытом разделе нашего класса как общие указатели на эти объекты. Обратите внимание, что объект timer привязан к функции с именем timer\_callback, которую мы увидим далее. Этот объект таймера будет срабатывать каждые 500 мс.

```text
[ ]: publisher_ = this->create_publisher<std_msgs::msg::Int32>("counter"); 
timer_ = this->create_wall_timer(500ms, std::bind(&SimplePublisher::timer_callback, this));
```

В приватном разделе у нас есть определение функции timercallback, которую мы ввели ранее. Внутри этой функции мы создаем сообщение Int32, которому присваивается значение переменной count\_. Затем мы собираемся увеличить значение переменной count\_ в 1, и мы собираемся опубликовать сообщение в нашей теме. Помните, что эта функция будет вызываться каждые 500 мс, как определено в объекте timer\_.

```text
[ ]: void timer_callback() {
    auto message = std_msgs::msg::Int32(); 
    message.data = count_;
    count_++; publisher_->publish(message);
    }
```

Также в приватном разделе мы создаем общие указатели на наши объекты издателя и таймера, определенные выше, и мы также создаем переменную count.

```text
[ ]: rclcpp::TimerBase::SharedPtr timer_; 
rclcpp::Publisher<std_msgs::msg::Int32>::SharedPtr publisher_; 
size_t count_;
```

Наконец, в основной функции все, что мы делаем, это создаем объект SimplePublisher и заставляем его вращаться, пока кто-нибудь не завершит программу \(Ctrl + C\).

```text
[ ]: int main(int argc, char * argv[])
     {
     rclcpp::init(argc, argv); 
     rclcpp::spin(std::make_shared<SimplePublisher>()); 
     rclcpp::shutdown();
     return 0;
}
```

И это все! Теперь вам решать, какой метод вы будете использовать для создания своих программ на ROS2!

## Сообщения

Как вы могли заметить, темы обрабатывают информацию через сообщения. Есть много разных типов сообщений.

В случае кода, который вы выполняли ранее, тип сообщения был std\_msgs / Int32, но ROS предоставляет много разных сообщений. Вы даже можете создавать свои собственные сообщения, но рекомендуется использовать сообщения ROS по умолчанию, когда это возможно.

Сообщения определяются в файлах .msg, которые находятся в каталоге msg пакета. 

Чтобы получить информацию о сообщении, используйте следующую команду:

```text
[ ]: ros2 msg show <message>
```

#### Пример 2.1

Например, давайте попробуем получить информацию о сообщении std\_msgs / Int32. Введите следующую команду и проверьте вывод. 

Выполнить в WebShell \# 1

```text
[ ]: ros2 msg show std_msgs/Int32
```

Вывод WebShell \# 1

```text
[ ]: user:~$ ros2 msg show std_msgs/Int32 
     int32 data
```

В этом случае сообщение Int32 имеет только одну переменную типа int32 с именем data. Это сообщение Int32 приходит из пакета std\_msgs, и вы можете найти его в каталоге msg. 

Теперь вы готовы создать своего собственного издателя и заставить робота двигаться, так что давайте сделаем это!

#### Пример 2.2

Измените код, который вы использовали ранее, чтобы теперь он публиковал данные в теме / cmd\_vel. 

Скомпилируйте ваш пакет снова. 

Запустите программу и убедитесь, что робот движется. 

#### Данные для примера 2.2

1. Тема / cmd\_vel - это тема, используемая для перемещения робота. 

2. Тип сообщения, используемый темой / cmd\_vel, - geometry\_msgs / Twist. 

3. Чтобы узнать структуру сообщений Twist, вам нужно использовать команду ros2 msg show. 

4. В этом случае робот использует плагин дифференциального привода для перемещения. То есть робот может двигаться только линейно по оси x или вращательно по угловой оси z. Это означает, что единственными значениями, которые необходимо заполнить в сообщении Twist, являются линейный x и угловой z. 

5. Величины сообщения Твист указаны в м / с, поэтому рекомендуется использовать значения от 0 до 1. Например, 0,5 м / с.

6. Чтобы иметь возможность использовать сообщение Twist, вам необходимо включить пакет geometry\_msgs в ваш файл CMakeLists.txt. Вот:

```text
[ ]: find_package(geometry_msgs REQUIRED)

[ ]: ament_target_dependencies(simple_publisher_node rclcpp std_msgs geometry_msgs)
```

7. Помните, что для связи с роботом Turtlebot2, работающим на ROS1, вам необходимо запустить мост ROS1.

### 

\#\#\#

Решения 

Пожалуйста, попробуйте сделать это самостоятельно, если вы не застряли или не нуждаетесь во вдохновении. Вы узнаете намного больше, если будете бороться с каждым упражнением.

