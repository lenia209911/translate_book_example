# Модуль 2. ROS1 Bridge

![](../.gitbook/assets/image%20%2816%29.png)

• ROSject Link: [https://bit.ly/2B5pnK4](https://bit.ly/2B5pnK4)

• Робот: **BB-8**

## ROS1 Bridge

Предполагаемое время для завершения: 6 часов. Что вы узнаете из этого блока?

• Настройка ROS1 Bridge

• Основные примеры

• Пользовательское отображение сообщений • Упражнения с симуляцией

## Настройка ROS1 Bridge

На момент создания этого учебного пособия ROS2 Crystal не был 100% заменой ROS1, и поэтому ему придется сосуществовать с системами ROS1. Итак, нам нужен какой-то метод для соединения систем ROS2 с системами ROS1. Это особенно верно в Симуляциях Gazebo. Хотя для ROS2 уже подготовлены Gazebo-Simulation, например, роботизированная рука MARA, которую вы будете использовать в этом курсе, созданный командой ErleRobotics, но не так много всего. Итак, вам нужен какой-то способ запуска уже существующих симуляций с использованием ROS1, но способный взаимодействовать с ROS2.

Это именно то место, где **ROS1-Bridge** вступает в игру.

Ros1-Bridge соединяет сообщения от ROS1 и ROS2. Предварительная версия поддерживает некоторые из этих сообщения и большинство сообщений ядра ROS.

Это отображение между сообщениями из ROS1-ROS2 определяется во время компиляции через файлы **yaml**, экспортированные в **package.xml**. Дополнительная информация по этой теме сопоставления: [https://github.com/ros2/ros1\_bridge/blob/master/doc/index.rst](https://github.com/ros2/ros1_bridge/blob/master/doc/index.rst) Но в этом курсе мы сконцентрируемся на использовании только сообщений по умолчанию

##  **ROS1-Bridge Sourcing**

Итак, для этого первого примера мы просто воспроизведем стандартные тесты для ROS1-Bridge. Таким образом, вы получите представление о том, как работает эта система.

Первое, что нужно сделать, - это создать настраиваемую конфигурацию bashrc для моста, чтобы иметь возможность работать с ROS1 и ROS2, необходимые для ROS1-Bridge.

#### **В этом случае он уже создан в вашей рабочей области.**

Выполнить в WebShell \# 1

```text
[ ]: # Check a Custom bahsrc_bridge 
     cat /home/user/.bashrc_bridge 
```

####  **Настройка {4.1}: .bashrc\_bridge**

```text
[ ]: source /opt/ros/melodic/setup.bash 
     source /opt/ros/crystal/local_setup.bash 
     source /home/user/catkin_ws/devel/setup.bash 
     source /home/user/ros2_ws/install/local_setup.bash 
```

ROS1-Bridge основан на нескольких концепциях, но самым основным является тот факт, что оболочка, в которой вы запускаете **ROS1-Bridge**, должна иметь все пути ко всем сообщениям, к которым у нее есть доступ. Это означает, что он должен иметь возможность получать сообщения как ROS1, так и ROS2. Вот почему и **мелодические**, и **кристальные** источники поставляются для доступа к установленным системным пакетам ROS1 и ROS2. Кроме того, он должен иметь доступ к любому рабочему пространству, которое вы можете использовать. В этом случае **catkin\_ws** для ROS1 и **ros2\_ws** для ROS2. 

После того, как **ros1\_bridge** построен правильно, он в основном проверяет, имеют ли темы и службы в ROS1 и ROS2 одинаковые имена, типы сообщений и т. Д. Если это так, он связывает соответствующие темы и услуги. Если вы хотите более подробно узнать, как осуществляется это соответствие, ознакомьтесь с официальной документацией здесь: [https://github.com/ros2/ros1\_bridge/blob/master/doc/index.rst](https://github.com/ros2/ros1_bridge/blob/master/doc/index.rst)

### **Основные примеры**

Здесь мы выполним классические тесты ROS1-Bridge, чтобы лучше познакомиться с ним.

**Важное примечание**: пожалуйста, начните со свежих оболочек, чтобы избежать любых прошлых источников. Это можно сделать, просто перейдя в другой блок и вернувшись к этому, чтобы все сбросить все.

Мы будем использовать **два дополнительных** **bashrc\_rosX**, по одному для ROS1 и ROS2. Опять-таки, эти файлы **уже созданы** для вас, но мы покажем вам на случай, если вы выполните это где-то там, где их нет.

#### **Настройка {4.1}: .bashrc\_ros1**

```text
[ ]: # ROS1 export source source 
     ROS_DISTRO=melodic
     /opt/ros/$ROS_DISTRO/setup.bash 
     /home/user/catkin_ws/devel/setup.bash 
```

####  **Настройка {4.1}: .bashrc\_ros2**

```text
[ ]: # ROS2
     export source source ROS_DISTRO=crystal 
     /opt/ros/$ROS_DISTRO/setup.bash 
     /home/user/ros2_ws/install/local_setup.bash 
```

### **Пример 1a: Запуск моста и пример говорящего и слушающего ROS1-&gt; ROS2**

Для этого вам понадобятся три различных веб-оболочки, которые мы назовем \(для дальнейшего использования\):

• WebShell 1: ROS1-Bridge

• WebShell 2: Talker ROS1

• WebShell 3: Listener ROS2

**ПРИМЕЧАНИЕ**. В Robot Ignite Academy у вас уже работает **roscore**, просто потому что у вас есть Gazebo для ROS1 в симуляциях, использующих ROS1, поэтому вам не нужно ее запускать. Но на пустом локальном компьютере вам нужно будет это сделать, используя **ROS1**.

Выполнить в WebShell \# 1: ROS1-Bridge

Мы запускаем ROS1-Bridge, используя предоставленный **.bashrc\_bridge**.

```text
[ ]: . /home/user/.bashrc_bridge
     export ROS_MASTER_URI=http://localhost:11311 
     ros2 run ros1_bridge dynamic_bridge 
```

Выполнить в WebShell \#2: Talker ROS1

```text
[ ]: . /home/user/.bashrc_ros1 
     rosrun rospy_tutorials talker 
```

Выполнить в WebShell \#3: Listener ROS2

```text
[ ]: . /home/user/.bashrc_ros2
     ros2 run demo_nodes_cpp listener 
```

Вывод WebShell \#1: ROS1-Bridge

```text
[ ]: created 1to2 bridge for topic '/chatter' with ROS 1 type 
     'std_msgs/String' and ROS 2 type 'std_msgs/String'
     [INFO] [ros1_bridge]: Passing message from ROS 1 std_msgs/String to 
     ROS 2 std_msgs/String (showing msg only once per type)
     removed 1to2 bridge for topic '/chatter'
```

Вывод WebShell \#2: Talker ROS1

```text
[ ]: [INFO] [1544551384.459151]: hello world 1544551384.46
     [INFO] [1544551384.559150]: hello world 1544551384.56 
     [INFO] [1544551384.659144]: hello world 1544551384.66 
```

Вывод WebShell \#3: Listener ROS2

```text
[ ]: [INFO] [listener]: I heard: [hello world 1544551383.66] 
     [INFO] [listener]: I heard: [hello world 1544551383.76] 
     [INFO] [listener]: I heard: [hello world 1544551383.86] 
```

### **Пример 1b: Запуск моста и примера talker и listener ROS2-&gt; ROS1**

Для этого вам понадобятся **три** различных веб-оболочки, которые мы назовем \(для дальнейшего использования\):

• WebShell 1: ROS1-Bridge  
• WebShell 2: Listener ROS1  
****• WebShell 3: Talker ROS2

Выполнить в WebShell \# 1: ROS1-Bridge

```text
[ ]: . /home/user/.bashrc_bridge
     export ROS_MASTER_URI=http://localhost:11311 
     ros2 run ros1_bridge dynamic_bridge 
```

Выполнить в WebShell \#2: Listener ROS1

```text
[ ]: . /home/user/.bashrc_ros1
     rosrun roscpp_tutorials listener 
```

Выполнить в WebShell \#3: Talker ROS2

```text
[ ]: . /home/user/.bashrc_ros2
     ros2 run demo_nodes_py talker 
```

Вывод WebShell \#1: ROS1-Bridge

```text
[ ]: created 2to1 bridge for topic '/chatter' with ROS 2 type 
     'std_msgs/String' and ROS 1 type ''
     removed 2to1 bridge for topic '/chatter'
```

WebShell \#2 Output: Listener ROS1

```text
[ ]: [ INFO] [1544551475.461572300]: I heard: [Hello World: 0]
     [ INFO] [1544551476.453461509]: I heard: [Hello World: 1]
     [ INFO] [1544551477.453674267]: I heard: [Hello World: 2]
```

WebShell \#3 Output: Talker ROS2

```text
[ ]: [INFO] [talker]: Publishing: "Hello World: 0"
     [INFO] [talker]: Publishing: "Hello World: 1"
     [INFO] [talker]: Publishing: "Hello World: 2"
```

###  **Пример 2. Запуск моста для службы AddTwoInts**

Для этого вам понадобятся четыре различных веб-оболочки, которые мы назовем \(для дальнейшего использования\):

• WebShell 1: ROS1-Bridge  
• WebShell 2: add\_two\_ints\_server ROS1  
****• WebShell 3: add\_two\_ints\_client ROS2

Выполнить в WebShell \#1: ROS1-Bridge

```text
[ ]: # .bashrc_bridge sources everything and was used for the compilation . /home/user/.bashrc_bridge
     export ROS_MASTER_URI=http://localhost:11311
     ros2 run ros1_bridge dynamic_bridge 
```

Выполнить в WebShell \#2: add\_two\_ints\_server ROS1

```text
[ ]: # .bashrc_bridge sources everything and was used for the compilation 
     . /home/user/.bashrc_ros1
     export ROS_MASTER_URI=http://localhost:11311
     rosrun roscpp_tutorials add_two_ints_server 
```

Выполнить в WebShell \#3: add\_two\_ints\_client ROS2

```text
[ ]: # .bashrc_bridge sources everything and was used for the compilation
     . /home/user/.bashrc_ros2
     ros2 run demo_nodes_cpp add_two_ints_client 
```

Вывод WebShell \#1: ROS1-Bridge

```text
[ ]: Created 2 to 1 bridge for service /add_two_ints 
     Removed 2 to 1 bridge for service /add_two_ints 
```

Вывод WebShell \#2: add\_two\_ints\_server ROS1

```text
[ ]: [ INFO] [1544551634.781450042]: request: x=2, y=3
     [ INFO] [1544551634.782151118]: sending back response: [5] 
```

Вывод WebShell \#3: add\_two\_ints\_client ROS2

```text
[ ]: [INFO] [add_two_ints_client]: Result of add_two_ints: 5 
```

##  **Упражнения с симуляцией**

Теперь, когда мы знаем, как двигаться с помощью ROS1-Bridge, давайте сделаем еще несколько примеров доступа к системам и симуляциям роботов.

Мы сделаем следующие примеры:

• Просмотр изображений, опубликованных в ROS1-Gazebo камерами робота с системами ROS2.

•  Движение робота в симуляции ROS1-Gazebo через ROS2. • Сброс симуляции ROS1-Беседка через ROS2

####  **Пример 4.1**

### **Просмотр изображений, опубликованные в ROS1-Gazebo камерами робота с системами ROS2**

Для этого вам понадобятся три различных веб-оболочки, которые мы назовем \(для дальнейшего использования\):

• WebShell 1: ROS1-Bridge  
****• WebShell 2: Image topic remapper ROS1, remapping the Robots Image topic to the topic

```text
/image
```

• WebShell 3: show\_image ROS2

Выполнить в WebShell \# 1: ROS1-Bridge

```text
[ ]: . /home/user/.bashrc_bridge
     export ROS_MASTER_URI=http://localhost:11311 
     ros2 run ros1_bridge dynamic_bridge 
```

Выполнить в WebShell \# 2: Image topic remapper ROS1

Нам нужно переиздать тему с изображением робота в тему, которую может использовать **ros2** **run** **image\_tools** **show-image**. На момент создания этого курса он поддерживал чтение только из топика / image. Это означает, что если ваш топик «Камера робота» называется **my\_robot** **/** **raw**, этот топик должна быть переиздан в топик **/** **image**. И именно поэтому нам нужно использовать двоичный файл для **повторной** публикации из пакета ROS1 **image\_transport**, чтобы сделать это. См. Следующую официальную документацию для получения дополнительной информации.

```text
[ ]: . /home/user/.bashrc_ros1
     rosrun image_transport republish raw in:=/bb8/camera1/image_raw out:=/image
```

Выполнить в WebShell \# 3: show\_image ROS2 

На данный момент этот графический интерфейс пользователя изображения ROS2 публикуется только из топика с именем **/** **image**. Это означает, что нам придется каким-то образом переназначить его, чтобы иметь возможность получать изображения из любого топика с изображениями ROS1. Смотрите исходный файл для более подробной информации LINK. Но поскольку мы сделали это на предыдущем шаге, и у нас работает **ROS1-Bridge**, просто нужно запустить этот образ, и **ROS1-Bridge** обнаружит, что ROS2 пытается прочитать информацию из раздела **Image**, и подключить их. И поскольку мы переназначили, топик изображения будет зеркальным отображением **/** **bb8 / camera1 / image\_raw**.

```text
[ ]: . /home/user/.bashrc_ros2
     ros2 run image_tools showimage
```

Вывод WebShell \#1: ROS1-Bridge

```text
[ ]: created 1to2 bridge for topic '/image' with ROS 1 type
     'sensor_msgs/Image' and ROS 2 type 'sensor_msgs/Image'
     [INFO] [ros1_bridge]: Passing message from ROS 1 sensor_msgs/Image to 
     ROS 2 sensor_msgs/Image (showing msg only once per type) 
```

Вывод WebShell \#2: Image topic remapper ROS1

```text
[]:
```

Вывод WebShell \#3: Cam2image

```text
[ ]: Received image #camera_link
     [INFO] [showimage]: Received image #camera_link ...
```

  
Теперь вы можете открыть **Графические инструменты**, при этом вы должны увидеть что-то вроде этого: 

Перейдите в окно графического интерфейса \(нажмите значок с экраном в IDE\).

![BB8 Camera](../.gitbook/assets/image%20%2830%29.png)

#### Пример 4.4

Перемещение робота из ROS1-Gazebo с ROS2

Переместим робота, который работает с ROS1 и Gazebo, через ROS2 с помощью ROS1-Bridge.

Для этого вам понадобятся две разные веб-оболочки, которые мы назовем \(для дальнейшего использования\):

• WebShell 1: ROS1-Bridge  
• WebShell 3: Publish movement command through ROS2 Выполнить в WebShell \#1: ROS1-Bridge  


Перемещение робота из ROS1-Gazebo с ROS2

Переместим робота, который работает с ROS1 и Gazebo, через ROS2 с помощью ROS1-Bridge.

Для этого вам понадобятся две разные веб-оболочки, которые мы назовем \(для дальнейшего использования\):

• WebShell 1: ROS1-Bridge  
• WebShell 3: Publish movement command through ROS2 

Выполнить в WebShell \#1: ROS1-Bridge

```text
[ ]: . /home/user/.bashrc_bridge
     export ROS_MASTER_URI=http://localhost:11311
     ros2 run ros1_bridge dynamic_bridge 
```

Выполнить в WebShell \# 3: опубликовать команду движения через ROS2. Чтобы робот повернулся:

```text
[ ]: . /home/user/.bashrc_ros2
     ros2 topic pub /cmd_vel geometry_msgs/Twist "{linear:{x: 0.0,y: 0.0,z: 0.0}, angular:{x: 0.0,y: 0.0,z: 1.0}}" 
```

Вывод WebShell \#1: ROS1-Bridge

```text
[ ]: created 2to1 bridge for topic '/cmd_vel' with ROS 2 type
     'geometry_msgs/Twist' and ROS 1 type ''
     [INFO] [ros1_bridge]: Passing message from ROS 2 geometry_msgs/Twist 
     to ROS 1 geometry_msgs/Twist (showing msg only once per type) 
```

Вывод WebShell \#3: Image topic remapper ROS1

```text
[ ]: publisher: beginning loop publishing #1: 
     geometry_msgs.msg.Twist(linear=geometry_msgs.msg.Vector3(x=0.0, y=0.0, z=0.0), angular=geometry_msgs.msg.Vector3(x=0.0, y=0.0, z=1.0))
     ... 
```

  
И вы должны увидеть поворот робота 

Чтобы робот остановился:

```text
[ ]: . /home/user/.bashrc_ros2
     ros2 topic pub /cmd_vel geometry_msgs/Twist "{linear:{x: 0.0,y: 0.0,z:0.0}, angular:{x: 0.0,y: 0.0,z: 0.0}}" 
```

#### Пример 4.3

Позвоните в службу сброса симуляции через ROS2

Чтобы попрактиковаться в обмене услугами с ROS2 с использованием ROS1-Bridge, мы собираемся сбросить моделирование, вызвав его сервис в Gazebo, называемый / gazebo / reset\_simulation

Для этого вам понадобятся две разные веб-оболочки, которые мы назовем \(для дальнейшего использования\):

• WebShell 1: ROS1-Bridge

• WebShell 2: публикация вызова службы с помощью ROS2.

```text
[ ]: . /home/user/.bashrc_bridge
export ROS_MASTER_URI=http://localhost:11311 ros2 run ros1_bridge dynamic_bridge
```

Выполните в WebShell \# 2: опубликуйте команду движения через ROS2. Чтобы робот повернулся:

```text
[ ]: . /home/user/.bashrc_ros2
     ros2 service call /gazebo/reset_simulation std_srvs/Empty '{}'
```

Вывод WebShell \# 1: ROS1-Bridge

```text
[ ]:Created 2 to 1 bridge for service /bb8/camera1/set_camera_info Created 2 to 1 bridge for service /bb8/imu/service
    Created 2 to 1 bridge
    Created 2 to 1 bridge
    Created 2 to 1 bridge
    Created 2 to 1 bridge
    for service /gazebo/pause_physics for service /gazebo/reset_simulation for service /gazebo/reset_world
    for service /gazebo/unpause_physics
```

Вывод WebShell \# 2: переиздатель topic изображения ROS1

```text
[ ]: requester: making request: std_srvs.srv.Empty_Request() 
     response:
     std_srvs.srv.Empty_Response()
```

Робот должен был вернуться в исходное состояние, прежде чем начать возиться с движением команды.

#### Поздравляем, теперь вы можете объединить ROS1 и ROS2.

