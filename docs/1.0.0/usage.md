## 使用说明

手势密码相关逻辑主要在`GestureLockActivity`中实现

跳转到该界面的router为`GestureConstants.ROUTER.GESTUREPASSWORDMODULE_GESTURELOCKACTIVITY`

不过跳转逻辑在 `GestureHolder`中做了封装，所以尽量不要直接利用navigateTo跳转  

## 手势密码保存策略

手势密码的保存包含两种策略，一种就是app层级的手势密码，一种就是账号层级的手势密码

```java
//由于钱包app  不存在帐号概念  同一个手机上的所有钱包共用一个手势密码，所以需要区分一下保存层级
    public enum STORE_STRATEGY {
        STRATEGY_APP,//手势密码保存模式  app层级保存
        STRATEGY_ACCOUNT//手势密码保存模式  帐号层级保存
    }
```

> GestureConstants.STORE_STRATEGY.STRATEGY_APP

手势密码保存模式  app层级保存，即一个app 有且仅有一个手势密码

> GestureConstants.STORE_STRATEGY.STRATEGY_ACCOUNT

手势密码保存模式， 账号层级保存，即一个app 多个账号可以有多个手势密码

!>该策略通过以下代码来设置,不设置的话默认为 STRATEGY_APP，即app层级保存

```java
  GestureHolder.getInstance().setStoreStrategy(GestureConstants.STORE_STRATEGY.STRATEGY_ACCOUNT);//如果是app层级就不需要调这个
```


## 手势密码界面跳转

手势密码界面为`GestureLockActivity`,设置手势密码和验证都在这个界面

`navigateToGesturePassword` 方法作用就是将跳转逻辑封装起来方便外部调用，实际代码如下

```java
 public static void navigateToGesturePassword(Activity activity, int gestureMode, String storeName, String data2Encrypt, int verifyLimit, int requestCode) {
        Bundle bundle = new Bundle();
        bundle.putInt(GestureConstants.KEY_GESTURE_MODE, gestureMode);
        bundle.putString(GestureConstants.KEY_GESTURE_STORE_NAME, storeName);//用来区分是哪个账户的手势密码
        bundle.putString(GestureConstants.KEY_GESTURE_DES_DATA_2_ENCRYPT, data2Encrypt); //要对称加密的数据
        if (verifyLimit > 0) {
            bundle.putInt(GestureConstants.KEY_GESTURE_VERIFY_ERROR_LIMIT, verifyLimit);//错误次数上限
        }

        ActivityStack.getInstance().navigateTo(activity, GestureConstants.ROUTER.GESTUREPASSWORDMODULE_GESTURELOCKACTIVITY,
                bundle, requestCode);
    }
```

### 参数

- activity:      `Activity` | navigateTo使用，当前要跳转到手势密码的界面的`Activity` 类 
- gestureMode    `int` | 手势密码模式，有两种  1. GestureConstants.MODE_SET_GESTURE 设置首诗面膜  2. GestureConstants.MODE_VERIFY_GESTURE 验证手势密码
- storeName      `String` | 手势密码相关数据保存地址名，保存一些store相关的数据，在手势密码的策略为 STRATEGY_ACCOUNT 的时候，包括手势密码在内的所有数据都保存在这个store里
- data2Encrypt   `String` | 要对称加密的数据，如果不为空 则设置手势密码的时候会把该数据加密放到store中
- verifyLimit    `int` | 错误次数上限，验证手势密码时最大错误次数
- requestCode    `int` | 自定义requestCode ，方便在onActivityResult中区分
 
由于该方法参数还是太多了，所以简化了两个方法来更方便的调用

1.这种方式默认不设置验证错误次数限制，此时默认使用5次的限制（此方法requestCode默认和gestureMode相同）

```java
public static void navigateToGesturePassword(Activity activity, int gestureMode, String storeName, String data2Encrypt) {
        //1，verifyLimit为0 则默认使用错误次数上限 5次 2.没有设置requestCode时 默认使用gestureMode为requestCode
        navigateToGesturePassword(activity, gestureMode, storeName, data2Encrypt, 0, gestureMode);
    }

```

2.这个方式也是默认不设置错误次数限制，另外可以自定义requestCode
 
```java

    public static void navigateToGesturePassword(Activity activity, int gestureMode, String storeName, String data2Encrypt, int requestCode) {
        //自己设置requestCode 以应对不同的情况
        navigateToGesturePassword(activity, gestureMode, storeName, data2Encrypt, 0, requestCode);
    }

```


## 创建手势密码

```java
 GestureHolder.navigateToGesturePassword(getActivity(),
                        GestureConstants.MODE_SET_GESTURE, "mockStore1", "mockPrivateKey1",3);
```

## 验证手势密码

首先切换到手势密码保存的store然后查询是否存在手势密码，存在就去验证

```java
  GestureHolder.getInstance().setStoreStrategy(GestureConstants.STORE_STRATEGY.STRATEGY_ACCOUNT);//如果是app层级就不需要调这个
  GestureHolder.getInstance().switchStore("mockStore1");//切换store
  GestureHolder.getInstance().getCurrentStore().cleanByTime(GestureConstants.DEFAULT_CLEAR_DURATION);//根据时间清空store内容
  if (TextUtils.isEmpty(GestureHolder.getInstance().getEncryptedGesturePassword())) {
    ToastUtil.normal("未找到手势密码！");
     return;
  }
  GestureHolder.navigateToGesturePassword(getActivity(), GestureConstants.MODE_VERIFY_GESTURE, "mockStore1", null);
```


## 获取对称加密数据

在进入手势密码界面的时候可以设置要堆成加密的数据，同时也有解密数据的方法

```java
GestureHolder.getInstance().getCurrentStore().getDesDecryptData()
```
 