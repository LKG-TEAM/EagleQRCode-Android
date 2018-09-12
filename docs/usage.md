## 使用说明

EagleQRCode主要包含两个功能，生成二维码和扫描二维码

相关界面实现类主要有两个

- QRGenerateActivity:  生成二维码 并且可以保存到本地
- QRScannerActivity:   扫描二维码

外部使用的时候 可以直接使用navigateTo

```java
  navigateTo(Constants.ROUTER.MEDIA_SCAN_QRCODE, 2001);
``` 

## 生成二维码

生成二维码主要有两种方式

>1.通过跳转到`QRGenerateActivity`生成

```java
 Bundle bundle = new Bundle();
  bundle.putString("data", "测试生成二维码");
  bundle.putBoolean("useDefaultIcon", true); 
  bundle.putString("iconUrl", "");
  navigateTo(Constants.ROUTER.MEDIA_GENERATE_QRCODE, bundle);
```
 
### Bundle参数说明

- data `String` | 要生成二维码的文字。
- useDefaultIcon `Boolean` | 生成的二维码中间是否用默认的图片，true的话则为R.mipmap.ic_launcher，false则为0
- iconUrl  `String` | logo图片的路径，若不为空则中间的logo图片使用这个。

>2.通过`QRGenerateUtil`来生成二维码

该方法是异步的 ，所以需要一个callback来获取生成二维码结果

```java
  QRGenerateUtil.createQRCode(String iconUrl, int drawableIdm, String code, String color,new QRGenerateUtil.QRCallback() {
            @Override
            public void onQRSuccess(Bitmap generatedBitmap) { 
            }

            @Override
            public void onQRFail() {
                Toast.makeText(QRGenerateActivity.this, "生成二维码失败", Toast.LENGTH_SHORT).show();
            }
        });
```

### 参数说明

- code `String` | 要生成二维码的文字
- drawableIdm `int` | 生成的二维码中间图片的资源id，若iconUrl为空则使用这个
- iconUrl  `String` | logo图片的路径，若不为空则中间的logo图片使用这个
- color  `String` | 二维码图片的前景色,若传空则为默认的`#000000`
- qrCallback `QRGenerateUtil.QRCallback` | 二维码生成结果回调，onQRSuccess表示生成成功，onQRFail生成二维码失败
 
 
## 扫描二维码

要扫描二维码直接跳转到二维码扫描界面`QRScannerActivity`进行扫描

```java
   navigateTo(Constants.ROUTER.MEDIA_SCAN_QRCODE,   int requestCode);
```

### 参数说明
- path `String` | 二维码扫描界面路径地址，此处应填 `Constants.ROUTER.MEDIA_SCAN_QRCODE`
- requestCode `int` | 界面跳转的requestCode,获取到扫描结果返回后在初始界面的 onActivityResult中通过这个requestCode来获取结果

获取二维码扫描结果

```java
  @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == 2001 && resultCode == RESULT_OK) {
            String result = data.getStringExtra("result");
            ToastUtil.info("接受到callback里的result:  " + result);
        }
    }
```

### 参数说明

- result `String` | 二维码扫描结果