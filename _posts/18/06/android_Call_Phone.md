---
title: android调用拨打电话（包括运行时权限及动态权限）
date: 2018-06-30 00:00:00
tag: [android,note]
---
## android调用拨打电话
```java
 /**
     * 拨打电话（跳转到拨号界面，用户手动点击拨打）
     *
     * @param phoneNum 电话号码
     */
    public void diallPhone(String phoneNum) {
        Intent intent = new Intent(Intent.ACTION_DIAL);
        Uri data = Uri.parse("tel:" + phoneNum);
        intent.setData(data);
        startActivity(intent);
    }
```
```java
/**
     * 拨打电话（直接拨打电话）
     *
     * @param phoneNum 电话号码
     */
    public void callPhone(String phoneNum) {
        Intent intent = new Intent(Intent.ACTION_CALL);
        Uri data = Uri.parse("tel:" + phoneNum);
        intent.setData(data);
        startActivity(intent);
    }
```
```java
<uses-permission android:name="android.permission.CALL_PHONE" />
```
## 自动拨打电话在6.0以后即使加了权限也会crash，因为动态权限的问题,解决如下
```java
private static final int MY_PERMISSIONS_REQUEST_CALL_PHONE = 1;
// 检查是否获得了权限（Android6.0运行时权限）
if (ContextCompat.checkSelfPermission(MainActivity.this, 
    Manifest.permission.CALL_PHONE)!= PackageManager.PERMISSION_GRANTED){
    // 没有获得授权，申请授权
    Toast.makeText(MainActivity.this, "请授权！", Toast.LENGTH_LONG).show();
    if (ActivityCompat.shouldShowRequestPermissionRationale(MainActivity.this,
        Manifest.permission.CALL_PHONE)){
        // 返回值：
        //如果app之前请求过该权限,被用户拒绝, 这个方法就会返回true.
        //如果用户之前拒绝权限的时候勾选了对话框中”Don’t ask again”的选项,那么这个方法会返回false.
        //如果设备策略禁止应用拥有这条权限, 这个方法也返回false.
        // 弹窗需要解释为何需要该权限，再次请求授权
        Toast.makeText(MainActivity.this, "请授权！", Toast.LENGTH_LONG).show();
        // 帮跳转到该应用的设置界面，让用户手动授权
        Intent intent = new Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
        Uri uri = Uri.fromParts("package", getPackageName(), null);
        intent.setData(uri);
        startActivity(intent);
    } else {
        // 不需要解释为何需要该权限，直接请求授权
        ActivityCompat.requestPermissions(MainActivity.this, 
            new String[]{Manifest.permission.CALL_PHONE}, MY_PERMISSIONS_REQUEST_CALL_PHONE);
    }
} else {
    callPhone(String phoneNum);
}
```
# 参考
- [android调用拨打电话（包括运行时权限）](https://blog.csdn.net/bin622/article/details/72453622)
- [解决Android6.0+拨打电话权限问题](https://blog.csdn.net/lty406910111/article/details/56288529)