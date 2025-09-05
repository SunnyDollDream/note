## Minio的基础概念

#### 概念

object:存储到 Minio 的基本对象，如文件、字节流，Anything...

Block:数据进行分块的单位,用于EC码模式划分数据块和校验块,一个Block默认是10M,不足10M的文件就只会占用自身大小的Block,大于的才会按10M一个块划分.

Bucket:用来存储 Object 的逻辑空间。每个 Bucket 之间的数据是相互隔离的。对于客户端而言，就相当于一个存放文件的顶层文件夹。

Drive:即存储数据的磁盘，在 Minio 启动时，以参数的方式传入。Minio 中所有的对象数据都会存储在Drive 里。

Set :即一组 Drive 的集合，分布式部署根据集群规模自动划分一个或多个 set，每个 Set 中的 Drive 分布在不同位置。一个对象存储在一个 Set 上(即一个set中的每一个drive都会存储一份文件)。(For example: {1...64} is divided into 4 sets each of size 16.)

- 一个对象存储在一个set上
    
- 一个集群划分为多个Set
    
- 一个Set包含的Drive数量是固定的，默认由系统根据集群规模自动计算得出
    
- 一个SET中的Drive尽可能分布在不同的节点上
    

#### Minio优点

- 部署简单:一个single二进制文件即是一切，还可支持各种平台。
    
- minio支持海量存储，可按zone扩展(原zone不受任何影响)，支持单个对象最大5TB;
    
- 兼容Amazon s3接口，充分考虑开发人员的需求和体验;
    
- 低冗余且磁盘损坏高容忍，标准且最高的数据冗余系数为2(即存储一个1M的数据对象，实际占用磁盘空间为2M)。但在任意n/2块disk损坏的情况下依然可以读出数据(n为一个纠删码集合(Erasure Coding set)中的disk数量)。并且这种损坏恢复是基于单个对象的,而不是基于整个存储卷的。
    
- 读写性能优异
    

#### 纠删码EC(Erasure code)

> 纠删码是一种恢复丢失和损坏数据的数学算法，Minio采用Reed-Solomon code将对象拆分成N/2数据和N/2 奇偶校验块。 这就意味着如果是12块盘，一个对象会被分成6个数据块、6个奇偶校验块，你可以丢失任意6块盘(不管其是存放的数据块还是奇偶校验块)，你仍可以从剩下的盘中的数据进行恢复。

Minio使用纠删码机制来保证高可靠性，使用 highwayhash 来处理数据损坏(Bit Rot Protection)。关于纠删码，简单来说就是可以通过数学计算，把丢失的数据进行还原，它可以将n份原始数据，增加m份数据，并能通过n+m份中的任意n份数据，还原为原始数据。即如果有任意小于等于m份的数据失效，仍然能通过剩下的数据还原出来。(至少需要四个drive)

#### 存储形式

文件对象上传到 Minio，会在对应的数据存储磁盘中，以 Bucket 名称为目录，文件名称为下一级目录，文件名下是和ec码下的part.1和x1.meta，前者是编码数据块及检验块，后者是元数据文件,而且会在奇数号磁盘中存储编码后的数据,偶数号磁盘存储原数据

#### 请求调用方式

![[Pasted image 20250715133228.png]]

## 部署

#### 单机部署(Docker)

```Shell
docker run -p 9000:9000(API端口) -p 9090:9090(图形化端口) \
--name minio \
-d --restart=always \
-e "MINIO_ACCESS_KEY=sunnydoll" \
-e "MINIO_SECRET_KEY=123456vbnm" \
-v /root/minio/data:/data \  (drive,可以挂好几个用以使用EC码模式)
-v /root/minio/config:/root/.minio \
minio/minio server \
/data --console-address ":9090" -address ":9000"
```

#### 分布式部署

启动一个分布式Minio实例，你只需要把硬盘位置做为参数传给minio server命令即可，然后，你需要在所有其它节点运行同样的命令:

`minio server` `http://host``{1...n}(ip端口)/export{1...m}(文件目录)`

```Shell
export MINIO_ROOT_USER=admin
export MINIO_ROOT_PASSWORD=12345678
minio server http://192.168.1.11/export1 http://192.168.1.12/export2
             http://192.168.1.13/export3 http://192.168.1.14/export4
             http://192.168.1.15/export5 http://192.168.1.16/export6
             http://192.168.1.17/export7 http://192.168.1.18/export8
```

- 分布式Minio里所有的节点需要有同样的access秘钥和secret秘钥，这样这些节点才能建立联接。为了实现这个，你需要在执行minio server命令之前，先将access秘钥和secret秘钥export成环境变量。
    
- 分布式Minio使用的磁盘里必须是干净的，里面没有数据。
    
- 分布式Minio里的节点时间差不能超过3秒，你可以使用NTP来保证时间一致。
    

分布式还需要一个统一入口,可以使用nginx做负载均衡,推荐使用Docker compose直接部署

#### 扩展分布式集群

Minio支持通过命令扩展

```Shell
export MINIO ROOT USER=admin
expOrt MINIO ROOT PASSWORD=12345678
minio server http://host{1...32}/export{1...32} http://host{33...64}/export{1..32}
```

## MC客户端

![[Pasted image 20250715133237.png]]

## 整合

1. 引入依赖
    

```XML
<dependency>
    <groupId>io.minio</groupId>
    <artifactId>minio</artifactId>
    <version>8.5.2</version>
</dependency>
<dependency>
    <groupId>me.tongfei</groupId>
    <artifactId>progressbar</artifactId>
    <version>0.10.0</version>
</dependency>
<dependency>
    <groupId>com.squareup.okhttp3</groupId>
    <artifactId>okhttp</artifactId>
</dependency>
```

2. 工具类
    

```Java
@Slf4j
public class MinioUtil {
    //创建客户端
    private static final MinioClient minioClient = MinioClient.builder()
                                                              .endpoint("http://101.126.153.13:9000")
                                                              .credentials("sunnydoll", "123456vbnm") //账号密码
                                                              .build();
    private static final String bucket = "oss";
    public static void uploadFile(MultipartFile[] files){
        try {
            //校验桶是否存在,不存在则创建
            boolean found = minioClient.bucketExists(BucketExistsArgs.builder().bucket(bucket).build());
            if (!found) {
                minioClient.makeBucket(MakeBucketArgs.builder().bucket(bucket).build());
            }
            for (MultipartFile file : files) {
                //重命名
                String originalFilename = file.getOriginalFilename();
                String ext = originalFilename.substring(originalFilename.lastIndexOf("."));
                String uuid = UUID.randomUUID().toString().replace("-", "");
                String newFileName = uuid + ext;
                InputStream inputStream = file.getInputStream();
                minioClient.putObject(
                        PutObjectArgs.builder()
                                .bucket(bucket)
                                .object(newFileName)
                                .stream(inputStream,file.getSize(),-1)
                                .contentType(file.getContentType())
                                .build()
                );
                inputStream.close();
                log.info(originalFilename + " is successfully uploaded as " + newFileName + " to bucket " + bucket);
            }
        } catch (MinioException e) {
            log.error("Error occurred: " + e);
            log.error("HTTP trace: " + e.httpTrace());
        } catch (IOException | NoSuchAlgorithmException | InvalidKeyException e) {
            throw new RuntimeException(e);
        }
    }
    public static String preview(String fileName){
        // 查看文件地址
        try {
            return minioClient.getPresignedObjectUrl(
                    GetPresignedObjectUrlArgs.builder()
                            .bucket(bucket)
                            .object(fileName)
                            .method(Method.GET)
                            .build());
        } catch (Exception e) {
            log.error(e.getMessage());
        }
        return null;
    }
    public static boolean remove(String fileName){
        try {
            minioClient.removeObject( 
                    RemoveObjectArgs.builder()
                            .bucket(bucket)
                            .object(fileName).build());
        }catch (Exception e){
            return false;
        }
        return true;
    }
}
```