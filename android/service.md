Summary of: [Services](https://developer.android.com/guide/components/services)

* If a component calls ```bindService()``` to create the service and ```onStartCommand()``` is not called, the service runs only as long as the component is bound to it. After the service is unbound from all of its clients, the system destroys it.

* ```onStartCommand()``` calling every time another component invoke ```startService()```

* since **Android Oreo (8.0)** services won't run in **background** (they will only have several minutes window to live after app goes background), only [Foreground Sevice](https://developer.android.com/guide/components/foreground-services), [JobIntentService](https://developer.android.com/reference/androidx/core/app/JobIntentService) (with [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler) under the hood), [WorkManager](https://developer.android.com/topic/libraries/architecture/workmanager) and [AlarmManager](https://developer.android.com/reference/android/app/AlarmManager) allowed.

* ```onStartCommand()``` must return an integer, which represents how the system should continue the service if it was killed due to free resources:
 * **START_STICKY** tells the OS to recreate the service after it has enough memory and call ```onStartCommand()``` again with a null intent. 
 * **START_NOT_STICKY** tells the OS to not bother recreating the service again. 
 * **START_REDELIVER_INTENT** that tells the OS to recreate the service and redeliver the same intent to ```onStartCommand()```

* Since **Android Oreo (8.0)** the system imposes restrictions on using or creating background services unless the app itself is in the foreground. If an app needs to create a foreground service, the app should call ```startForegroundService()```. That method creates a background service, but the method signals to the system that the service will promote itself to the foreground. Once the service has been created, the service must call its ```startForeground()``` method within five seconds. 

* All the descendants of [Service](https://developer.android.com/reference/android/app/Service) class have to manage it's lifecycle by their own. It means, ```stopSelf()``` should be called the job is over or any other component should call ```stopService()```. If necessary, other components can stop the service by calling ```stopService()```. Even if you enable binding for the service, you must always stop the service yourself if it ever receives a call to ```onStartCommand()```

* **Bound service** doesn't have to stop itself, it will stop when the last client unbinds.

* The ```onCreate()``` and ```onDestroy()``` methods are called for all services, whether they're created by ```startService()``` or ```bindService()```.

* In cases when Service was started and then bound, ```stopService()``` or ```stopSelf()``` does not actually stop the service until all clients unbind.

* If your service is used only by the local application and does not need to work across processes, then you can implement your own [Binder](https://developer.android.com/reference/android/os/Binder), otherwise use [Messenger](https://developer.android.com/guide/components/bound-services#Messenger)