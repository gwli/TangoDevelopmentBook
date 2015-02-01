SDK Framework
=============

支持C/Java/Unity的开发。
https://developers.google.com/project-tango/apis/c/c-lifecycle

lifecycle 
---------

.. graphviz::
    
   digraph lifecycle {
      OnCreate -> OnResume->OnPause -> EventsHandling;
   }

再加一些register函数。
