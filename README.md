# hunnuoj

### 项目地址

```
前端：https://github.com/Siryuanshao/HUNNUOJFE
后端：https://github.com/Siryuanshao/HUNNUOJBE
判题服务：https://github.com/Siryuanshao/JudgeServer
判题沙盒：https://github.com/Siryuanshao/Judger(fork from QingdaoU/Judger)
```



### 部署demo

建议使用nginx做反向代理，对前端进行gzip压缩(nginx.conf)




后端配置上传文件写入磁盘的路径(backend.properties)

```
avatar_absolute_path=/www/hunnuoj/public/static/avatar
avatar_relative_path=/static/avatar
image_absolute_path=/www/hunnuoj/public/static/images
image_relative_path=/static/images
secure_token=GFIUVGFRTCVYTIREXRWGKJI
```

redis服务用docker拉一个镜像

```shell
docker pull redis:latest
docker run -itd -p 127.0.0.1:6379:6379 redis
```

运行JudgeServer服务，其中judge-server是用JudgeServer手动打包的镜像，需要自行打包

```shell
sudo docker run -i -t -d -v /test_case_base_dir:/test_case_base_dir -v /dev/shm:/dev/shm -p 127.0.0.1:8233:8233/udp judge-server /bin/bash
```



### 判题文件

判题文件可以放置在宿主机的任意目录，在docker启动 JudgeServer的时候挂载到judge.conf 中test_case_base_dir指定的目录中。

#### 普通题目放置目录

/test_case_base_dir/problem/:problemId

#### 比赛题目放置目录

/test_case_base_dir/contest/:contestId/problem/:problemId

 #### 判题数据格式

如果题目不是spj，判题数据则为判题文件路径下所有前缀一一对应的.in和.out文件。

如果题目为spj，判题数据则为该目录下所有.in结尾的文件，并且在该目录下必须包含名称为spj的可执行程序，例如：

```
/test_case_base_dir/problem/10000/sample.in
/test_case_base_dir/problem/10000/sample.out

/test_case_base_dir/contest/2/problem/3/demo.in
/test_case_base_dir/contest/2/problem/3/demo.out

/test_case_base_dir/problem/10000/spj
/test_case_base_dir/contest/2/problem/3/spj
```



### SPJ示例

```c++
#include <stdio.h>
#include <math.h>
#define PI acos(-1.0)
#define AC 0
#define WA 1
int main(int argc,char *args[])
{
    // 题目输入文件
    FILE * f_in=fopen(args[1],"r");
    // 题目输出文件
    FILE * f_out=fopen(args[2],"r");
    // 用户输出文件
    FILE * f_user=fopen(args[3],"r");
    int ret=AC;
/**************自己写判题逻辑**************/
    double a,b,c,ans1,ans2;
    fscanf(f_in,"%lf",&a);
    ans2=2.0*PI*a;
    ans1=PI*a*a;
    fscanf(f_user,"%lf%lf",&b,&c);
    if (fabs(ans1-b)<0.0001 && fabs(ans2-c)>0.0001) ret=WA;
/************************************/
    fclose(f_in);
    fclose(f_out);
    fclose(f_user);
    return ret;
}
```

