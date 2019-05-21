# VR使用()

## 下载地址
[Google VR for Android](https://github.com/googlevr/gvr-android-sdk)

## VR类型
VR展示分为全景图和视频两种样式

## 全景图展示流程
加载布局
```
<com.google.vr.sdk.widgets.pano.VrPanoramaView
        android:id="@+id/vr_pano"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
```

调用代码加载
```
VrPanoramaView vrPano = (VrPanoramaView) findViewById(R.id.vr_pano);

VrPanoramaView.Options options = new VrPanoramaView.Options();
options.inputType = VrPanoramaView.Options.TYPE_STEREO_OVER_UNDER;//图像类型为立体图像
(VrPanoramaView.Options.TYPE_MONO图片类型为单通道图片）

vrPano.loadImageFromBitmap(bitmap, options);
```

事件监听
```
vrPano.setEventListener(mVrPanoramaEventListener);

private VrPanoramaEventListener mVrPanoramaEventListener = new VrPanoramaEventListener() {
    /**
     * 点击回调
     */
    @Override
    public void onClick() {
        super.onClick();
    }

    /**
     * 加载数据成功回调
     */
    @Override
    public void onLoadSuccess() {
        super.onLoadSuccess();
    }

    /**
     * 加载数据失败回调
     */
    @Override
    public void onLoadError(String errorMessage) {
        super.onLoadError(errorMessage);
    }
};
```

## 视频播放流程
加载布局
```
 <com.google.vr.sdk.widgets.video.VrVideoView
        android:id="@+id/vr_video"
        android:layout_width="match_parent"
        android:layout_height="150dp" />
```

调用代码加载
```
VrVideoView vr_video = (VrVideoView) findViewById(R.id.vr_video);

VrVideoView.Options options=new VrVideoView.Options();
options.inputType=VrVideoView.Options.TYPE_STEREO_OVER_UNDER;//视频类型为立体视频
options.inputFormat=VrVideoView.Options.FORMAT_DEFAULT;
	try {
		vr_video.loadVideo(Uri.parse(play),options);
		} catch (IOException e) {
		e.printStackTrace();
		}

```

## 捆绑生命周期
全景图和视频绑定周期api是一样的（如下是全景图的）
```
@Override
protected void onPause() {
    vrPano.pauseRendering();
    super.onPause();
}

@Override
protected void onResume() {
    super.onResume();
    vrPano.resumeRendering();
}

@Override
protected void onDestroy() {
    vrPano.shutdown();
    super.onDestroy();
}
```

事件监听
```
private VrVideoEventListener mVrEventListener = new VrVideoEventListener() {

    @Override
    public void onLoadError(String errorMessage) {
        Toast.makeText(VrVideoActivity.this, "onLoadError", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onLoadSuccess() {
        Toast.makeText(VrVideoActivity.this, "onLoadSuccess", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void onNewFrame() {
        //视频持续播放下一帧回调，可用作更新进度条功能
    }

    @Override
    public void onCompletion() {
        Toast.makeText(VrVideoActivity.this, "onCompletion", Toast.LENGTH_SHORT).show();
        mVrVideoView.seekTo(0);//播放结束后重新开始播放
    }

    @Override
    public void onClick() {
        togglePause();//点击暂停或者播放
    }
};
```

# 注意
1.由于全景图占内存较大，当加载多张全景图时可能存在内存溢出的情况，所以这里开启largeHeap。
```
<application
    android:largeHeap="true">
</application>
```

2.最小版本支持Android4.4，也就是api19

3.需要依赖支持库
```
  compile 'com.google.android.exoplayer:exoplayer:r1.5.10'
  compile 'com.google.protobuf.nano:protobuf-javanano:3.0.0-alpha-7'
```