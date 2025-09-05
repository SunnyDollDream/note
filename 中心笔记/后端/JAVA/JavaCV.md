JavaCV是一个开源的Java框架，它提供了基于Java的接口，用于访问各种计算机视觉库和工具包，如OpenCV、FFmpeg等,还提供了与深度学习框架的集成，如TensorFlow和Caffe。

## 配置

**1.引入依赖**

```XML
<dependency>
    <groupId>org.bytedeco</groupId>
    <artifactId>javacv</artifactId>
    <version>1.5.6</version>
</dependency>
<dependencies>
    <dependency>
        <groupId>org.bytedeco</groupId>
        <artifactId>javacv-platform</artifactId>
        <version>1.5.6</version>
    </dependency>
</dependencies>
```

## FFmpeg的使用

#### 打开、读取和写入视频文件

1. 首先，需要创建FFmpegFrameGrabber(帧抓取器)对象，并通过该对象设置要打开的视频文件的路径、输入流和格式等信息(可以不设置)。(其中文件可以通过Path.of(文件路径)创建一个path对象,再调用其.tofile方法转为文件)
    

```Java
FFmpegFrameGrabber grabber = new FFmpegFrameGrabber(videoFile);
grabber.setOption("rtsp_transport", "tcp");
grabber.setFormat("rtsp");
```

2. 然后，可以通过调用grabber的start()方法开始读取视频数据，并可以获取视频的一些基本信息，如视频的宽度、高度、帧率等。
    

```Java
grabber.start();
int width = grabber.getImageWidth();
int height = grabber.getImageHeight();
double frameRate = grabber.getFrameRate();
```

3. 接下来，可以使用grabber的grabFrame()方法不断读取视频的每一帧数据，直到读取完所有的帧为止。(Frame指每一帧)
    

```Java
Frame frame;
while ((frame = grabber.grabFrame()) != null) {
    // 对每一帧进行处理
}
```

4. 处理完每一帧后，可以使用FFmpegFrameRecorder(帧录制器/帧推流器)对象将处理后的视频帧写入到指定的输出文件中，并设置输出的编码格式、帧率等参数。
    

```Java
FFmpegFrameRecorder recorder = new FFmpegFrameRecorder(outputFile, width, height);
recorder.setFormat("mp4");
recorder.setFrameRate(frameRate);
recorder.start();
// 对每一帧进行处理后，写入到输出文件中
recorder.record(frame);
```

5. 最后，需要在完成视频读取和写入操作后，调用grabber和recorder的stop()方法，释放资源。
    

> javaCV的对象创建方法似乎都是native的调用c代码,所以GC不会管理这些对象需要手动释放

```Java
grabber.stop();
recorder.stop();
```

综上所述，使用javacv打开、读取和写入视频文件需要创建FFmpegFrameGrabber和FFmpegFrameRecorder对象，分别完成视频的读取和写入操作，并在读取和写入操作完成后，及时释放资源。

#### 图像处理

## OpenCV的使用

#### 使用本地摄像头

1. 使用`avutil.`_`av_log_set_level`_`(avutil.`_`AV_LOG_INFO`_`);FFmpegLogCallback.`_`set`_`();`设置日志级别
    
2. 创建OpenCVFrameGrabber对象,直接new即可,形参可以传入摄像头序号,如果只有一个摄像头就是0
    
3. 调用grabber的setImageWidth和Height设置图像的大小,调用start启动
    
4. 调用grabber的greb方法抓取
    
5. 创建Frame和Mat的对象,写一个循环抓取每一帧,用Frame接收,循环内记得设置一定的间隔,看不出来频闪即可
    
6. `private final OpenCVFrameConverter.ToIplImage openCVConverter = new OpenCVFrameConverter.ToIplImage();`创建一个转换器,调用其converttomat方法将Frame转化为ma对象,之后再调用convert方法把mat转回去(如果不需要对图片进行加工也可以省略这一步)
    
7. 新建CanvasFrame对象,用于输出,调用showImage(Frame)方法展示帧
    

```Java
previewCanvas = new CanvasFrame("摄像头预览", CanvasFrame.getDefaultGamma() / grabber.getGamma());
previewCanvas.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
previewCanvas.setAlwaysOnTop(true);
```

这样就可以抓取本地摄像头并在可视化图窗上显示了

#### 将视频保存到本地

1. 先定义一个字符串用来保存要存放的地址
    
2. 创建一个FFmpegFrameRecorder对象
    

```Java
recorder = new FFmpegFrameRecorder(RECORD_FILE_PATH,        // 存放文件的位置
                                           getCameraImageWidth(),   // 分辨率的宽，与视频源一致
                                           getCameraImageHeight(),  // 分辨率的高，与视频源一致
                                           0);                      // 音频通道，0表示无
        // 文件格式
        recorder.setFormat("mp4");

        // 帧率与抓取器一致
        recorder.setFrameRate(getFrameRate());

        // 编码格式
        recorder.setPixelFormat(AV_PIX_FMT_YUV420P);

        // 编码器类型
        recorder.setVideoCodec(avcodec.AV_CODEC_ID_MPEG4);

        // 视频质量，0表示无损
        recorder.setVideoQuality(0);

        // 初始化
        recorder.start();
```

3. 调用record的record(Frame)方法保存