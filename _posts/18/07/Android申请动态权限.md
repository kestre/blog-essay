---
title: Android一次申请多个动态权限
date: 2018-07-03 00:00:00
tag: [Android,note]
---
# Android一次申请多个动态权限
今天做一个短信群发软件，需要申请多个权限，遇到点小问题。
## 在 Android6.0（Api 23）以下的版本，申请权限。
- 只要在Manifest文件中添加权限：

```java
// 读写手机状态和身份权限 及 短信权限
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.SEND_SMS" />
```
## Android在6.0以后就要使用动态权限了，否则程序可能无法进行某些功能操作。 
- Android 6.0对应的Android SDK等级是23.所以一般是先判断手机的版本是否是6.0以上再进行动态请求权限。
- 动态申请权限如下:

```java
if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.READ_PHONE_STATE)
        != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(MainActivity.this, new String[]{Manifest.permission.READ_PHONE_STATE}, MY_PERMISSIONS_REQUEST_READ_PHONE_STATE);
} else {
    // TODU
}
```
- 其中MY_PERMISSIONS_REQUEST_READ_PHONE_STATE 是自定义的类常量，可以像下面这样在activity中定义：

```java
public final static int MY_PERMISSIONS_REQUEST_READ_PHONE_STATE = 1;
```
## 一个请求读写手机状态和身份权限和短信权限的实例
```java
package com.example.xiao.sms;

import android.Manifest;
import android.app.ActivityManager;
import android.app.PendingIntent;
import android.content.DialogInterface;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.net.Uri;
import android.os.Build;
import android.provider.Settings;
import android.support.annotation.NonNull;
import android.support.v4.app.ActivityCompat;
import android.support.v4.content.ContextCompat;
import android.support.v7.app.AlertDialog;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.telephony.SmsManager;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity {

    private EditText phoneNum;
    private EditText message;
    private Button sendSMS;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        phoneNum = (EditText)findViewById(R.id.phone_num);
        message = (EditText)findViewById(R.id.message);
        sendSMS = (Button) findViewById(R.id.send_message);

        // 这里直接在页面创建的时候请求权限，其实不太好
        // 一般是在触发某个事件的时候再请求动态权限
        // 比如点击按钮跳转到一个打电话页面，如果权限通过就跳转，否者提示说没有权限！
        if (Build.VERSION.SDK_INT >= 23) {// 6.0才用动态权限
            initPermission();
        }

        sendSMS.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                    String[] everyNumber = phoneNum.getText().toString().split("，");
                    String content = message.getText().toString();
                    for (String anEveryNumber : everyNumber) {
                        sendMessage(anEveryNumber, content);
                    }
                    Toast.makeText(MainActivity.this, "发送成功！", Toast.LENGTH_LONG).show();
                }
        });
    }

    public void sendMessage(String phoneNumber,String content){
        SmsManager sms = SmsManager.getDefault();
        ArrayList<String> mSMSMessage = sms.divideMessage(content);
        int messageCount = mSMSMessage.size();
        android.telephony.SmsManager smsManager = android.telephony.SmsManager.getDefault();
        Intent itSend = new Intent("demo_sms_send_action");
        itSend.putExtra("phone_num", phoneNumber);
        ArrayList<PendingIntent> sentIntents = new ArrayList<PendingIntent>(messageCount);
        smsManager.sendMultipartTextMessage(phoneNumber, null, mSMSMessage, sentIntents, null);
    }

    // 申请两个权限，读写手机状态和身份权限 及 短信权限
    // 首先声明一个数组permissions，将需要的权限都放在里面
    String[] permissions = new String[]{Manifest.permission.READ_PHONE_STATE,
            Manifest.permission.SEND_SMS};
    // 创建一个mPermissionList，逐个判断哪些权限未授予，未授予的权限存储到mPerrrmissionList中
    List<String> mPermissionList = new ArrayList<>();
    private final int mRequestCode = 100;// 权限请求码

    private void initPermission() {
        mPermissionList.clear();// 清空没有通过的权限

        // 逐个判断你要的权限是否已经通过
        for (int i = 0; i < permissions.length; i++) {
            if (ContextCompat.checkSelfPermission(this, permissions[i]) != PackageManager.PERMISSION_GRANTED) {
                mPermissionList.add(permissions[i]);// 添加还未授予的权限
            }
        }
        // 申请权限
        if (mPermissionList.size() > 0) {// 有权限没有通过，需要申请
            ActivityCompat.requestPermissions(this, permissions, mRequestCode);

        }else{
            Toast.makeText(MainActivity.this, "已授权！", Toast.LENGTH_LONG).show();
            // 说明权限都已经通过，可以做你想做的事情去
        }
    }

    // 请求权限后回调的方法
    // 参数： requestCode  是我们自己定义的权限请求码
    // 参数： permissions  是我们请求的权限名称数组
    // 参数： grantResults 是我们在弹出页面后是否允许权限的标识数组，
    // 数组的长度对应的是权限名称数组的长度，数组的数据0表示允许权限，-1表示我们点击了禁止权限
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        boolean hasPermissionDismiss = false;// 有权限没有通过
        if (mRequestCode == requestCode) {
            for (int i = 0; i < grantResults.length; i++) {
                if (grantResults[i] == -1) {
                    hasPermissionDismiss = true;
                }
            }
            // 如果有权限没有被允许
            if (hasPermissionDismiss) {
                showPermissionDialog();// 跳转到系统设置权限页面，或者直接关闭页面，不让他继续访问
            }else{
                // 全部权限通过，可以进行下一步操作。。。
            }
        }
    }


    /**
     * 不再提示权限时的展示对话框
     */
    AlertDialog mPermissionDialog;
    String mPackName = "com.example.xiao.sms";

    private void showPermissionDialog() {
        if (mPermissionDialog == null) {
            mPermissionDialog = new AlertDialog.Builder(this)
                    .setMessage("已禁用权限，请手动授予")
                    .setPositiveButton("设置", new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            cancelPermissionDialog();

                            Uri packageURI = Uri.parse("package:" + mPackName);
                            Intent intent = new Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS, packageURI);
                            startActivity(intent);
                        }
                    })
                    .setNegativeButton("取消", new DialogInterface.OnClickListener() {
                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            // 关闭页面或者做其他操作
                            cancelPermissionDialog();

                            // 进程式关闭软件
                            android.os.Process.killProcess(android.os.Process.myPid());
                            System.exit(0);
                            ActivityManager manager = (ActivityManager) getSystemService(ACTIVITY_SERVICE);
                            manager.killBackgroundProcesses(getPackageName());
                        }
                    })
                    .create();
        }
        mPermissionDialog.show();
    }

    // 关闭对话框
    private void cancelPermissionDialog() {
        mPermissionDialog.cancel();
    }
}
```
示例代码根据实际情况做简单修改就可以用在各种动态权限下。
**说明：**上面的权限数组可以请求多个权限，也可以只请求一个权限

# 参考
- [Android一次申请多个动态权限](https://blog.csdn.net/wenzhi20102321/article/details/80487975)