# Docker starter kit part 1: Getting Started, Container's lifecycle
Docker là một bước ngoặt lớn trong công việc phát triển phần mềm. Nhưng nó lại có thể trở thành trở ngại cho một lập trình viên để bắt đầu các khái niệm về container. Trong series các bài viết này sẽ trả lời cho lập trình viên đang muốn làm chủ cơ bản về Docker. Nó không phải là một khóa đào tạo về Docker hoàn chỉnh (điều này đã có đầy đủ tài liệu chính thức của Docker) mà là một cách tiếp cận nhanh nhất dành cho người mới bắt đầu. 

Sẽ không có một số lượng các phần cố định trong loạt series này vì tôi không chắc chắn sẽ đề cập đến bao nhiêu phần của Docker (ví dụ tôi không chắc chắn sẽ nói về Docker compose hoặc là Docker Warm) 

## Getting Started
### OS
Hệ điều hành hiện tại không thành vấn đề nữa. Docker hiện tại có sẵn nguyên bản trên Windows 10 (từ bản cập nhật 1511 tháng 11, Build 10586) OSX and Linux. Tôi sử dụng nó mà không có bất kỳ vấn đề nào trên OSX và debian tại nơi làm việc, cũng tương tự trên Windows 10 và Linux Mint lúc ở nhà. Những gì tôi gợi ý là để tránh các giải pháp gốc dành cho các phiên bản của hệ điều hành cũ OSX và Windows 7/10. Trải nghiệm của tôi có được với chúng thật tồi, có quá nhiều vấn đề, về sự bất ổn cũng như các biểu hiện không rõ ràng. 

### Let’s go!
Phần này thật đơn giản, truy cập trang chính thức và thực hiện loạt hướng dẫn: 
* [Installation](https://docs.docker.com/get-started/)
* [Images and Containers](https://docs.docker.com/get-started/)
* [Run your first image](https://docs.docker.com/get-started/)
* [Build your first image](https://docs.docker.com/get-started/)
* [Create your Docker Hub account](https://docs.docker.com/get-started/)
* [Push and Pull an image](https://docs.docker.com/get-started/)

Trong phần này, tôi hy vọng bạn sẽ hiểu cơ bản về những hướng dẫn mà tôi đã đưa ra. 
Ngoài ra, bạn cũng có thể tham thảo thêm từ blog của [Johnny Mkhael](http://jmkhael.io/) - tác giả của chuỗi series [Hello Docker](http://jmkhael.hopto.org/hello-docker/)

## Lifecycle of a container
### Overview
Một Docker Container có nhiều giai đoạn. Dưới đây là danh sách hoàn chỉnh các bước khác nhau cũng như các lệnh liên quan từ tài liệu chính thức. 

![Docker States](docker_state.png)

Các bước chính: 

created: container của bạn được khởi tạo. Điều đó cũng có nghĩa là bạn đã khởi tạo một thể hiện của một image. Bước này được thực hiện qua câu lệnh: docker create

running: container đang chạy điều đó cũng có nghĩa là nó đã được khởi tạo (docker create) và đã được bắt đầu (docker start). Bạn cũng có thể thực hiện bước này trực tiếp từ một image thông qua câu lệnh docker run. 

paused: một container đang tạm dừng là một giai đoạn tạm "đóng băng" nhưng ý nghĩa của nó là không bị dừng lại hoàn toàn. Container không cung cấp tài nguyên của nó. Từ một container đang chạy, bạn có thể thực hiện bước này thông qua câu lệnh docker pause. Ngược lại, sử dụng docker unpause.

stopped: Khi dừng 1 container, tín hiệu SIGTERM sẽ được gửi từ docker tới tiến trình chính. Sau 1 khoảng thời gian (mặc định khoảng 10 giây), tiến trình chính sẽ nhận được tín hiệu SIGKILL. Bạn có thể dừng một container thông qua lệnh docker stop. Bạn có thể truyền trực tiếp tín hiệu SIGKILL thông qua câu lệnh docker kill. Nó không phải là giải pháp tốt nếu bạn muốn khởi động lại container. Bạn có thể chuyển đổi trạng thái từ một stopped container sang running container bằng câu lệnh docker start. 


# Docker starter kit part 2: Anatomy of an Image and a Container
Trong phần đầu của chuỗi bài viết về Docker, chúng ta đã được theo dõi các hướng dẫn cơ bản về Docker và có cái nhìn tổng quan về vòng đời của một Docker Container. Trong bài viết này, chúng ta sẽ được một Docker Image và một Docker Container được tạo ra như thế nào.
## Anatomy of an Image
### Union File Systems
Union file systems, hoặc là UnionFS đều là những file hệ thống, cái mà được vận hành bởi quá trình tạo ra các layer, giúp chúng trở nên nhẹ và nhanh hơn. Docker sử dụng các file hợp nhất trong hệ thống để cung cấp cho các blocks tạo ra các containers.
> - Docker Official Documentation
### UnionFS and Docker Images
Một Docker Image được tạo ra bởi một vài layer. Mỗi layer tượng trưng là một file hệ thống khác nhau của layer phía dưới nó.

```
docker pull maven:3.3.9-jdk-8
```

Đừng quá bận tâm đến kết quả từ câu lệnh vừa rồi, chúng ta sẽ tìm hiểu nó cụ thể ở phần sau. Thay vào đó, sử dụng câu lệnh sau để kiểm tra tất cả các layers của một image:

```
docker history maven:3.3.9-jdk-8
```
Output: 
```
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
9997d8483b2f        13 months ago       /bin/sh -c #(nop)  CMD ["mvn"]                  0B                  
<missing>           13 months ago       /bin/sh -c #(nop)  ENTRYPOINT ["/usr/local/b…   0B                  
<missing>           13 months ago       /bin/sh -c #(nop)  VOLUME [/root/.m2]           0B                  
<missing>           13 months ago       /bin/sh -c #(nop) COPY file:b3fc14e8337e0079…   327B                
<missing>           13 months ago       /bin/sh -c #(nop) COPY file:e3aa30a24fcf60a5…   1.04kB              
<missing>           13 months ago       /bin/sh -c #(nop)  ENV MAVEN_CONFIG=/root/.m2   0B                  
<missing>           13 months ago       /bin/sh -c #(nop)  ENV MAVEN_HOME=/usr/share…   0B                  
<missing>           13 months ago       |2 MAVEN_VERSION=3.3.9 USER_HOME_DIR=/root /…   10MB                
<missing>           13 months ago       /bin/sh -c #(nop)  ARG USER_HOME_DIR=/root      0B                  
<missing>           13 months ago       /bin/sh -c #(nop)  ARG MAVEN_VERSION=3.3.9      0B                  
<missing>           13 months ago       /bin/sh -c /var/lib/dpkg/info/ca-certificate…   419kB               
<missing>           13 months ago       /bin/sh -c set -x  && apt-get update  && apt…   350MB               
<missing>           13 months ago       /bin/sh -c #(nop)  ENV CA_CERTIFICATES_JAVA_…   0B                  
<missing>           13 months ago       /bin/sh -c #(nop)  ENV JAVA_DEBIAN_VERSION=8…   0B                  
<missing>           13 months ago       /bin/sh -c #(nop)  ENV JAVA_VERSION=8u121       0B                  
<missing>           13 months ago       /bin/sh -c #(nop)  ENV JAVA_HOME=/usr/lib/jv…   0B                  
<missing>           13 months ago       /bin/sh -c {   echo '#!/bin/sh';   echo 'set…   87B                 
<missing>           13 months ago       /bin/sh -c #(nop)  ENV LANG=C.UTF-8             0B                  
<missing>           13 months ago       /bin/sh -c echo 'deb http://deb.debian.org/d…   55B                 
<missing>           13 months ago       /bin/sh -c apt-get update && apt-get install…   1.29MB              
<missing>           13 months ago       /bin/sh -c apt-get update && apt-get install…   123MB               
<missing>           13 months ago       /bin/sh -c apt-get update && apt-get install…   44.6MB              
<missing>           13 months ago       /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B                  
<missing>           13 months ago       /bin/sh -c #(nop) ADD file:4eedf861fb567fffb…   123MB               
```
Đầu tiên, hãy xem lại Dockerfile được sử dụng để tạo ra Image này: 
```
FROM openjdk:8-jdk

ARG MAVEN_VERSION=3.3.9
ARG USER_HOME_DIR="/root"

RUN mkdir -p /usr/share/maven /usr/share/maven/ref \
  && curl -fsSL http://apache.osuosl.org/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz \
    | tar -xzC /usr/share/maven --strip-components=1 \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

ENV MAVEN_HOME /usr/share/maven
ENV MAVEN_CONFIG "$USER_HOME_DIR/.m2"

COPY mvn-entrypoint.sh /usr/local/bin/mvn-entrypoint.sh
COPY settings-docker.xml /usr/share/maven/ref/

VOLUME "$USER_HOME_DIR/.m2"

ENTRYPOINT ["/usr/local/bin/mvn-entrypoint.sh"]
CMD ["mvn"]
```
