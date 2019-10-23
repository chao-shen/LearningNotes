# Activity介绍
## A跳转到Activity B，生命周期的执行过程

A跳转到ActivityB：
* A.onPause
* B.onCreate
* B.onStart
* B.onResume
* A.onStop

B按返回键返回Activity A:
* B.onPause
* A.onReStart
* A.onStart
* A.onResume
* B.onStop
* B.onDestroy
