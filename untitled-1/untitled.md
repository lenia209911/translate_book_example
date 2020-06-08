# Модуль 3. Topics часть2

![Kobuki](../.gitbook/assets/image%20%2838%29.png)

![](../.gitbook/assets/image%20%2834%29.png)

• ROSject Link: [https://bit.ly/2MBJDb2](https://bit.ly/2MBJDb2) 

• Робот: Turtlebot 2

Предполагаемое время для завершения: 2,5 часа Что вы изучите с этим устройством?

• Что такое подписчик и как его создать 

• Как создать собственное сообщение

## Часть 1: Подписчик \(Subscriber\)

Вы узнали, что тема - это канал, где узлы могут либо записывать, либо читать информацию. Вы также видели, что можете писать в тему с помощью издателя, поэтому вы можете подумать, что должен быть какой-то аналогичный инструмент для чтения информации из темы. И ты прав! Это называется подписчиком. Подписчик - это узел, который читает информацию из темы. Давайте выполним следующий код:

#### Пример 2.3

• Создайте новый пакет с именем topic\_subscriber\_pkg. При создании пакета добавьте rclcpp и std\_msgs как зависимости. 

• Внутри папки src пакета создайте новый файл с именем simple\_topic\_subscriber.cpp. Внутри этого файла скопируйте содержимое файла simple\_topic\_subscriber.cpp 

• Создать файл запуска для запуска этого кода. 

• Сделайте необходимые изменения в вашем файле CMakeLists.txt и скомпилируйте пакет. 

• Запустите файл запуска для запуска вашего исполняемого файла.

### C++ программа {2.2}: simple\_topic\_subscriber.cpp

```text
[ ]: #include "rclcpp/rclcpp.hpp" 
     #include "std_msgs/msg/int32.hpp"
     rclcpp::Node::SharedPtr g_node = nullptr;
     void topic_callback(const std_msgs::msg::Int32::SharedPtr msg) {
          RCLCPP_INFO(g_node->get_logger(), "I heard: '%d'", msg->data);
          }
          int main(int argc, char * argv[])
          {
               rclcpp::init(argc, argv);
               g_node = rclcpp::Node::make_shared("simple_subscriber"); 
               auto subscription = g_node->create_subscription<std_msgs::msg::Int32> ("counter", topic_callback);
               rclcpp::spin(g_node); 
               rclcpp::shutdown();
               subscription = nullptr; 
               g_node = nullptr; 
               return 0;
}
```

Что происходит? Ничего не случилось снова? Ну, это не совсем так... Давайте сделаем несколько проверок. 

Перейдите к своей веб-оболочке и введите следующее: 

Выполнить в WebShell \# 1

```text
[ ]: ros2 topic echo /counter
```

Так что случилось? Ничего? И что это значит? Это означает, что никто не публикует в теме /counter, поэтому нет информации для чтения. Давайте тогда что-нибудь опубликуем в теме и посмотрим, что получится. Для этого давайте введем новую команду:

```text
[ ]: ros2 topic pub <topic_name> <message_type> <value>
```

Эта команда опубликует указанное вами сообщение с указанным вами значением в теме, которую вы уточнить. 

Откройте другую webshell \(оставьте открытую с rostopic echo\) и введите следующую команду: 

Выполнить в WebShell \# 2

```text
[ ]: ros2 topic pub /counter std_msgs/Int32 "{data: '5'}"
```

Теперь проверьте вывод консоли, где вы снова сделали rostopic echo. 

Тебе следует увидеть как то так: 

Вывод WebShell \# 1

```text
[ ]: user:~$ ros2 topic echo /counter 
    data: 5
    data: 5 
    data: 5 
    data: 5 
    data: 5 
    ...
```

Это означает, что опубликованное вами значение было получено вашей подписной программой \(которая выводит значение на экран\). 

Теперь проверьте вывод оболочки, в которой вы выполнили свой код подписчика. Вы должны увидеть что-то вроде этого:

```text
[ ]: ros2 launch topic_subscriber_pkg simple_topic_subscriber.launch.py
     [INFO] [launch]: process[simple_subscriber_node-1]: started with pid [5900]
     [INFO] [simple_subscriber]: I heard: '5'
     [INFO] [simple_subscriber]: I heard: '5'
     [INFO] [simple_subscriber]: I heard: '5'
     [INFO] [simple_subscriber]: I heard: '5'
     [INFO] [simple_subscriber]: I heard: '5'
     [INFO] [simple_subscriber]: I heard: '5'
     [INFO] [simple_subscriber]: I heard: '5'
```

Прежде чем объяснить все более подробно, давайте объясним код, который вы выполнили.

```text
[ ]: #include "rclcpp/rclcpp.hpp" 
     #include "std_msgs/msg/int32.hpp"
     rclcpp::Node::SharedPtr g_node = nullptr;
// Определить функцию с именем topic_callback, которая получает параметр с именем 'msg'
     void topic_callback(const std_msgs::msg::Int32::SharedPtr msg)
     {
  // Вывести значение 'data' в параметр 'msg'
          RCLCPP_INFO(g_node->get_logger(), "I heard: '%d'", msg->data); }
          int main(int argc, char * argv[])
          {       
               rclcpp::init(argc, argv);
// Инициируем узел с именем 'simple_subscriber'
               g_node = rclcpp::Node::make_shared("simple_subscriber");
// Создание объекта publisher, который будет прослушивать topic /counter
и будет вызывать функцию topic_callback // каждый раз
               reads something from the topic
                    auto subscription = g_node->create_subscription<std_msgs::msg::Int32>
                         ("counter", topic_callback);
// Создать цикл, который будет поддерживать выполнение программы
               rclcpp::spin(g_node);
               rclcpp::shutdown();
               subscription = nullptr; 
               g_node = nullptr; 
               return 0;
     }
```

Итак, давайте объясним, что только что произошло. Вы в основном создали узел подписчика, который прослушивает тему /counter, и каждый раз, когда он что-то читает, он вызывает функцию, которая печатает сообщение. Первоначально ничего не происходило, поскольку никто не публиковал в теме /counter, но когда вы выполнили команду ros2 topic pub, вы опубликовали сообщение в теме /counter, так что ваш подписчик напечатал этот номер, и вы также можете увидеть это сообщение в ros2 topic echo output Теперь все имеет смысл, верно?

## Состав узла \(node\)

Как объяснялось в предыдущей главе, только что проверенный скрипт C ++, sim\_topic\_subscriber.cpp, написан с использованием метода программирования старой школы. Таким образом, он не сможет использовать состав узла. Чтобы использовать состав узла, вы должны использовать скрипт, подобный приведенному ниже.

### C++ программа {2.2b}: simple\_topic\_subscriber\_composable.cpp

```text
[ ]: #include "rclcpp/rclcpp.hpp" 
     #include "std_msgs/msg/int32.hpp" 
     using std::placeholders::_1;
     class SimpleSubscriber : public rclcpp::Node {
     public:
     SimpleSubscriber()
     : Node("simple_subscriber") {
     subscription_ = this->create_subscription<std_msgs::msg::Int32>( "counter", std::bind(&SimpleSubscriber::topic_callback, this,_1)); }
     private:
     void topic_callback(const std_msgs::msg::Int32::SharedPtr msg) {
     RCLCPP_INFO(this->get_logger(), "I heard: '%d'", msg->data); }
     rclcpp::Subscription<std_msgs::msg::Int32>::SharedPtr subscription_; 
     };
          int main(int argc, char * argv[])
          {
               rclcpp::init(argc, argv); 
               rclcpp::spin(std::make_shared<SimpleSubscriber>()); 
               rclcpp::shutdown();
               return 0;
          }
```

Как вы можете видеть, он очень похож на первый сценарий, но в этом случае мы используем класс с именем SimpleSubscriber, который наследуется от узла суперкласса - как в примере, который вы видели в предыдущей главе. 

Теперь давайте сделаем несколько упражнений, чтобы применить на практике то, что вы узнали!

#### Пример 2.4

Измените предыдущий код, чтобы распечатать одометрию робота.

#### Данные для примера 2.4

Одометрия робота публикуется роботом в теме / odom. 

Вам необходимо выяснить, какое сообщение использует тему / odom, и структуру этого сообщения. 

Не забудьте скомпилировать ваш пакет снова, чтобы обновить ваш исполняемый файл.

**ПРИМЕЧАНИЕ:** Помните, что для того, чтобы иметь возможность считывать данные одометрии из В теме / odom симуляции вам нужно будет сначала запустить ROS1 Bridge.

Решени примера 2.4

Пожалуйста, попробуйте сделать это самостоятельно, если вы не застряли или не нуждаетесь во вдохновении. Вы узнаете намного больше, если будете бороться с каждым упражнением.

#### Пример 2.5



1. Добавьте в {пример 2.4} файл C ++, который создает издателя, который указывает возраст робота, к предыдущему пакету.
2. Для этого вам нужно создать новое сообщение с именем Age.msg. Смотрите подробное описание Как подготовить CMakeLists.txt и package.xml для компиляции пользовательских сообщений темы.

Решение примера 2.5

Пожалуйста, попробуйте сделать это самостоятельно, если вы не застряли или не нуждаетесь во вдохновении. Вы узнаете намного больше, если будете бороться с каждым упражнением.

## Как подготовить CMakeLists.txt и package.xml для пользовательских сообщений сообщений pilation

Теперь вы можете быть удивлены... в случае, если мне нужно опубликовать некоторые данные, которые не являются Int32, какой тип сообщения я должен использовать? Вы можете использовать все сообщения, определенные ROS \(список сообщений ros2\). Но, если ни один из них не соответствует вашим потребностям, вы можете создать новый.

 Чтобы создать новое сообщение, вам необходимо выполнить следующие шаги: 

1. Создайте каталог с именем «msg» внутри вашего пакета 

2. Внутри этого каталога создайте файл с именем Name\_of\_your\_message.msg \(дополнительная информация ниже\) 

3. Измените файл CMakeLists.txt \(более подробная информация ниже\) 

4. Измените файл package.xml \(более подробная информация ниже\) 

5. Компиляция и исходный код 

6. Используйте в коде Например, давайте создадим сообщение с указанием возраста, с годами, месяцами и днями.

1\) Создайте каталог msg в вашем пакете.

```text
[ ]: cd ~/ros2_ws/src/<package_name> mkdir msg
```

2\) Файл Age.msg должен содержать это:

```text
[ ]: float32 years
     float32 months
     float32 days
```

3\) В CMakeLists.txt 

Вам нужно отредактировать четыре функции внутри CMakeLists.txt:

• find\_package \(\)   
• rosidl\_generate\_interfaces \(\)

### I. find\_package\(\)

Это то место, куда отправляются все пакеты, необходимые для компиляции сообщений тем, сервисов и действий. В package.xml вы должны указать их как build\_depend и exec\_depend.

```text
[ ]: find_package(ament_cmake REQUIRED) 
     find_package(rclcpp REQUIRED) 
     find_package(std_msgs REQUIRED) 
     find_package(builtin_interfaces REQUIRED) 
     find_package(rosidl_default_generators REQUIRED)
```

### II. rosidl\_generate\_interfaces\(\)

Эта функция включает в себя все сообщения этого пакета \(в папке msg\) для компиляции. Файл должен выглядеть следующим образом.

```text
[ ]: rosidl_generate_interfaces(new_msg "msg/Age.msg")
```

Подводя итог, это минимальное выражение того, что необходимо для работы CMakaelist.txt: 

Примечание: имейте в виду, что имя пакета в следующем примере - topic\_ex, поэтому в вашем случае имя пакета может отличаться.

```text
[ ]:cmake_minimum_required(VERSION 3.5) project(new_msg)
    # По умолчанию C99
    if(NOT CMAKE_C_STANDARD) set(CMAKE_C_STANDARD 99)
    endif()
    # По умолчанию C++14
    if(NOT CMAKE_CXX_STANDARD) set(CMAKE_CXX_STANDARD 14)
    endif()
    if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang") add_compile_options(-Wall -Wextra -Wpedantic)
    endif()
    # Найти зависимости
    find_package(ament_cmake REQUIRED)
    find_package(rclcpp REQUIRED) find_package(std_msgs REQUIRED) 
    find_package(builtin_interfaces REQUIRED) 
    find_package(rosidl_default_generators REQUIRED)
    if(BUILD_TESTING)
    find_package(ament_lint_auto REQUIRED)
    # Следующая строка пропускает линтер, который проверяет авторские права # Удаляет строку, когда авторское право и лицензия присутствуют во всех
    source files
    set(ament_cmake_copyright_FOUND TRUE)
    # следующая строка пропускает cpplint (работает только в git-репо)
    # удалить строку, когда этот пакет является git-repo
    set(ament_cmake_cpplint_FOUND TRUE) 
    ament_lint_auto_find_test_dependencies()
    endif()
    rosidl_generate_interfaces(new_msg"msg/Age.msg")
ament_package()
```

4\) Изменить package.xml 

Во-первых, вам нужно будет установить формат пакета на 3. Обратите внимание, что по умолчанию это будет установлено на 2, поэтому вам придется изменить его вручную.

```text
[ ]: <package format="3">
```

Это должно быть сделано, потому что команде member\_of\_group требуется формат 3. 

Теперь просто добавьте следующие строки в файл package.xml.

```text
[ ]: <build_depend>builtin_interfaces</build_depend> 
<build_depend>rosidl_default_generators</build_depend> 
<exec_depend>builtin_interfaces</exec_depend> 
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group>
```

Примечание: имейте в виду, что имя пакета в следующем примере - new\_msg, поэтому в вашем случае имя пакета может отличаться.

```text
[ ]: <?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format2.xsd"
schematypens="http://www.w3.org/2001/XMLSchema"?> <package format="3">
<name>new_msg</name>
<version>0.0.0</version>
<description>TODO: Package description</description> 
<maintainer email="ubuntu@todo.todo">ubuntu</maintainer> 
<license>TODO: License declaration</license>
<buildtool_depend>ament_cmake</buildtool_depend>
 <depend>rclcpp</depend>
<depend>std_msgs</depend>
<build_depend>builtin_interfaces</build_depend> 
<build_depend>rosidl_default_generators</build_depend> 
<exec_depend>builtin_interfaces</exec_depend> 
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group> 
<test_depend>ament_lint_auto</test_depend>
<test_depend>ament_lint_common</test_depend>
 <export> 
 <build_type>ament_cmake</build_type>
  </export>
 </package>
```

5\) Теперь вы должны скомпилировать сообщения. Для этого вы должны ввести WebShell: 

Выполнить в WebShell \# 1

```text
[ ]: cd ~/ros2_ws
     colcon build --symlink-install --packages-select new_msg 
     source install/setup.bash
```

**ОЧЕНЬ ВАЖНО:** Когда вы компилируете новые сообщения, еще есть дополнительный шаг, прежде чем вы сможете использовать сообщения. Вы должны ввести в Webshell, в ros2\_ws, следующую команду: source install / setup.bash. Это запускает этот bash-файл, который устанавливает, среди прочего, новые сгенерированные сообщения, созданные при сборке colcon. Если вы этого не сделаете, это может привести к ошибке при импорте, говоря, что оно не находит сгенерированное сообщение. 

СОВЕТ 2: Чтобы убедиться, что ваше сообщение было успешно создано, введите в своей веб-оболочке: ros2 msg show new\_msg / Age. Если появится структура сообщения Age, это будет означать, что ваше сообщение было успешно создано и готово для использования в ваших программах ROS. 

Выполнить в WebShell \# 1

```text
[ ]: ros2 msg show new_msg/Age
```

Вывод WebShell \# 1

```text
[ ]: user ~ $ ros2 msg show new_msg/Age 
     float32 years
     float32 months
     float32 days

```

## Использование пользовательских сообщений в файлах Cpp

Вам нужно будет добавить в свой CMakeLists.txt следующие дополнительные строки, чтобы скомпилировать и связать ваш исполняемый файл \(в этом примере он называется publish\_age.cpp\):

```text
[ ]: find_package(new_msg REQUIRED)
        add_executable(age_publisher_node src/publish_age.cpp) 
        ament_target_dependencies(age_publisher_node rclcpp std_msgs new_msg)
        install(TARGETS age_publisher_node DESTINATION lib/${PROJECT_NAME})
```

## Topics мини проекта

![Tultlebot2](../.gitbook/assets/image%20%2844%29.png)

Со всем, что вы узнали за этот курс, вы теперь можете пройти небольшой тест, чтобы собрать все воедино. Подписчики, издатели, сообщения ... вам нужно будет использовать все эти концепции, чтобы добиться успеха!

В этом небольшом проекте вы создадите код, который позволит роботу избежать стены, которая находится перед ним. Чтобы помочь вам в этом, давайте разделим проект на более мелкие единицы:



1. Создайте издателя, который пишет в раздел / cmd\_vel для перемещения робота.
2. Создайте подписчика, который читает из раздела / kobuki / laser / scan. Это тема, где

   лазер публикует свои данные.

3. В зависимости от показаний, которые вы получаете по теме лазера, вам придется изменить данные

   Вы отправляете в раздел / cmd\_vel, чтобы избежать стены. Это значит, использовать значения лазера, чтобы решить.

СОВЕТ 1: Данные, которые публикуются в теме / kobuki / laser / scan, имеют большую структуру. Для этого проекта вам просто нужно обратить внимание на массив «диапазонов».

Выполнить в WebShell \# 1

```text
[ ]: ros2 msg show sensor_msgs/LaserScan
```

Вывод WebShell \# 1

```text
[ ]:user ~ $ ros2 msg show sensor_msgs/LaserScan 
    std_msgs/Header header
        uint32 seq
        time stamp 
        string frame_id
    float32 angle_min
    float32 angle_max
    float32 angle_increment
    float32 time_increment
    float32 scan_time
    float32 range_min
    float32 range_max
    float32[] ranges <-- Используйте только этот 
    float32[] intensities
```

СОВЕТ 2: Массив «диапазонов» имеет много значений. Те, которые находятся в середине массива, представляют расстояния, которые лазер обнаруживает прямо перед ним. Это означает, что значения в середине массива будут теми, которые обнаруживают стену. Итак, чтобы избежать стены, вам просто нужно прочитать эти значения. 

СОВЕТ 3: Лазер имеет дальность 30м. Когда вы получаете показания значений около 30, это означает, что лазер ничего не обнаруживает. Если вы получите значение меньше 30, это будет означать, что лазер обнаруживает какое-то препятствие в этом направлении \(стена\). 

СОВЕТ 4: область действия лазера составляет около 180 градусов справа налево. Это означает, что значения в начале и в конце массива «диапазонов» будут теми, которые связаны с показаниями на боковых сторонах лазера \(слева и справа\), в то время как значения в середине массива будут те, которые связаны с передней частью лазера.

