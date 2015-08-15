基本概念
********

这个包括，Motition track,Area Learning,  Depth Perception.

Motion Track
------------

主要是 *视距的计算* ，以及自身的位置变量，Tango采用是增长差分计算方法，初始参考值，是可以设置的，session开始的位置，或者上一次保存的位置。并且采用是四元数的方法。这里另一个问题，累计误差会产生漂移。 这个主要是通过陀螺仪等来度量

Area Learning
-------------

SLAM(Simultaneous Localization and Mapping), 学习周围的环境。并且能够认识当前的环境。

同时它还可以用校正前面的Motion Track 的漂移问题。

另外一个那就是学习效率的问题。同一个环境的白天与晚上，学习出来的向量有没有差别。除了用GPS本身这个的学习是应该也是个难点。

Depth Perception
-----------------

对于所有的可视点的距离的感知。这样的每一个点才是立体的，现在方式主要就是点云了。
同时把 depthMapping 与颜色图结合起来，不就形成了纹理图。 这些都是通过红外传感器来实现的，但是如何红外传感器与那鱼眼镜头结合起来呢。 

并且有了两种的三维数据的保存格式，PCL格式与XYZij模式。基于这种格式，各种常规的计算是如何来的，例如如何点去快速得到周围的点，那另一个
那就是计算几何知识来恢复三维信息。

这个深度的计算就有需要相机的内参与外参数估计与转换。
https://developers.google.com/project-tango/overview/intrinsics-extrinsics

一个常规的思路是这样的。

#. 计算出特征点，这些是未来点云的点。
#. 能过内外参数的估计，估计出图像中每一点的深度，形成三维的点
#. 在通过图片的对应关系，形成纹理，在直接合成三维立体的效果。




.. code-block:: c

   typedef struct TangoCameraIntrinsics {
     TangoCameraId camera_id;
     TangoCalibrationType calibration_type;
     uint32_t width;
     uint32_t height;
     double fx;
     double fy;
     double cx;
     double cy;
     double distortion[5];
   } TangoIntrinsics


内参数采用的是针孔相机模型。 

Device Pose
-----------


.. code-block:: c
   
   typedef struct TangoPoseData {
      int version;
      double timestamp;
      double orientation[4];
      double tranlation[3];
      TangoPoseStatusType status_code;
      TangoCoordinateFramePair Frame;
      int confidence;
      float accuracy;
   } TangoPoseData;
   


坐标系的转换
------------

Tango pose data 以OGL坐标系以及 unity 的坐标系。

TangoEvent
----------

主要用来处理传感器的方面的异常，例如过曝与欠曝等等。


 各种传感器的使用可以查看 `sensor_overview <http://developer.android.com/guide/topics/sensors/sensors_overview.html>`_ 主要是通过消息队列来实现，可以查看*NativeAccelerate* 例子。

.. include:: c
    
   #include <android/sensor.h>

   //初使化
   m_sensorManager = ASensorManager_getInstance();
   m_accelerometerSensor = ASensorManager_getDefaultSensor(m_sensorManager,
           ASENSOR_TYPE_ACCELEROMETER);
   m_sensorEventQueue = ASensorManager_createEventQueue(m_sensorManager,
           mApp->looper, LOOPER_ID_USER, NULL, NULL);
   

   if (running)        
			{
                ASensorEventQueue_enableSensor(m_sensorEventQueue,
                        m_accelerometerSensor);
                // We'd like to get 60 events per second (in us).
                ASensorEventQueue_setEventRate(m_sensorEventQueue,
                        m_accelerometerSensor, (1000L/60)*1000);
            }
			else
			{
                ASensorEventQueue_disableSensor(m_sensorEventQueue,
                        m_accelerometerSensor);


  void Engine::looperIteration(int looperIdent)  //update the sensor data



视觉理程的计算
==============

特征提取，特征匹配跟踪到运动估计的理论框架。寻找特征点，然后利用三点或五点法
求本征方程。立体里程计算是有绝对的尺寸，而单目是没有，但时可以相机的本身运动。
来得到。
