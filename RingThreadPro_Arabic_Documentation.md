# مكتبة RingThreadPro - إدارة متقدمة للخيوط في لغة Ring
================================================

## نظرة عامة
-----------
مكتبة RingThreadPro هي مكتبة شاملة لإدارة الخيوط (Threads) في لغة Ring. توفر المكتبة أدوات قوية للتزامن بين الخيوط وواجهة مرنة لبناء التطبيقات المتزامنة.

## الميزات الأساسية
----------------

1. إدارة الخيوط
--------------
- إنشاء وإدارة الخيوط
  * إنشاء خيوط متعددة
  * دعم تسمية الخيوط
  * تتبع حالة الخيط (جاهز، قيد التشغيل، مكتمل، خطأ، منتهي)
  * أولويات الخيوط (من 1 إلى 10)
  * دمج الخيوط وتبديلها
  * عد الخيوط النشطة

2. أدوات التزامن
--------------
أ) إدارة Mutex
   * إنشاء mutex بسيط
   * دعم mutex التكراري
   * عمليات mutex الموقوتة
   * عمليات القفل وفك القفل
   * وظيفة محاولة القفل

ب) متغيرات الشرط
   * إنشاء وإدارة متغيرات الشرط
   * آليات الإشارة والبث
   * الانتظار على الشروط مع mutex
   * عمليات انتظار موقوتة

ج) حواجز الخيوط
   * نقاط تزامن للخيوط المتعددة
   * إنشاء الحواجز والانتظار
   * تنسيق الخيوط

## الأمثلة العملية

1. مثال الخيوط الأساسي (ThreadExample.ring)
----------------------------------------
```ring
# مثال بسيط لإنشاء خيط
oThreads = new ThreadManager(1)
oThreads.createThread(1, "myFunction()")
oThreads.joinThread(1)
```

2. مثال متقدم (ThreadAdvancedExample.ring)
---------------------------------------
```ring
# مثال لاستخدام mutex
mutex = oThreads.createMutex(1)  # mutex بسيط
oThreads.lockMutex(mutex)
# العمليات المتزامنة هنا
oThreads.unlockMutex(mutex)
```

3. مثال مجمع الخيوط (ThreadPoolExample.ring)
----------------------------------------
```ring
# إنشاء مجمع خيوط مع 4 خيوط عاملة
POOL_SIZE = 4
oThreads = new ThreadManager(POOL_SIZE)

# تعيين أولويات الخيوط
for i = 1 to POOL_SIZE
    oThreads.setThreadPriority(i, 5)
next
```

## الوظائف الرئيسية

1. إدارة الخيوط
-------------
* createThread(nIndex, funcName)
  - إنشاء خيط جديد
  - nIndex: رقم الخيط
  - funcName: اسم الدالة المراد تنفيذها

* setThreadPriority(nIndex, priority)
  - تعيين أولوية الخيط
  - priority: من 1 (الأدنى) إلى 10 (الأعلى)

* setThreadName(nIndex, name)
  - تعيين اسم للخيط
  - يفيد في التتبع والتصحيح

2. التزامن
--------
* createMutex(nType)
  - إنشاء mutex جديد
  - nType: نوع mutex (1: بسيط، 4: تكراري)

* createCondition()
  - إنشاء متغير شرط جديد
  - يستخدم للتنسيق بين الخيوط

* createBarrier(nThreads)
  - إنشاء حاجز للخيوط
  - nThreads: عدد الخيوط التي يجب أن تنتظر

3. التصحيح والمراقبة
-----------------
* enableDebug()
  - تفعيل وضع التصحيح
  - يظهر رسائل تفصيلية عن العمليات

* dumpThreadInfo()
  - عرض معلومات مفصلة عن جميع الخيوط
  - يشمل الحالة والأولوية والأخطاء

## أنماط الاستخدام الشائعة

1. نمط المنتج-المستهلك
--------------------
```ring
# إنشاء موارد مشتركة
queue = []
mutex = oThreads.createMutex(1)
condition = oThreads.createCondition()

# دالة المنتج
func producer
    oThreads.lockMutex(mutex)
    add(queue, "item")
    oThreads.signalCondition(condition)
    oThreads.unlockMutex(mutex)

# دالة المستهلك
func consumer
    oThreads.lockMutex(mutex)
    while len(queue) = 0
        oThreads.waitCondition(condition, mutex)
    end
    item = queue[1]
    del(queue, 1)
    oThreads.unlockMutex(mutex)
```

2. نمط مجمع الخيوط
---------------
```ring
# إنشاء مجمع خيوط
POOL_SIZE = 4
oThreads = new ThreadManager(POOL_SIZE)

# تهيئة العمال
for i = 1 to POOL_SIZE
    oThreads.createThread(i, "worker("+i+")")
next
```

## أفضل الممارسات

1. إدارة الموارد
-------------
- دائماً استخدم mutex لحماية الموارد المشتركة
- نظف الموارد باستخدام destroy() عند الانتهاء
- تأكد من إغلاق جميع الخيوط بشكل صحيح

2. الأداء
-------
- استخدم أولويات الخيوط بحكمة
- تجنب إنشاء خيوط كثيرة
- استخدم مجمعات الخيوط للمهام المتكررة

3. التصحيح
--------
- استخدم وضع التصحيح عند الحاجة
- راقب حالات الخيوط بانتظام
- تتبع وعالج الأخطاء بشكل مناسب

## معالجة الأخطاء

1. أخطاء الخيوط
------------
- تتبع الأخطاء لكل خيط
- تنفيذ استعادة من الأخطاء
- تسجيل حالات الخطأ

2. أخطاء Mutex
-----------
- معالجة فشل القفل
- تجنب الإغلاق المتبادل
- التنظيف المناسب

