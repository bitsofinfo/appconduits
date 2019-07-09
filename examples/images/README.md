Build images
```
docker build --no-cache --build-arg appname=dogapp --build-arg appversion=1.0.0 -t bitsofinfo/dogapp:1.0.0 -f Dockerfile.dogapp .
docker push bitsofinfo/dogapp:1.0.0

docker build --no-cache --build-arg appname=dogapp --build-arg appversion=2.0.0 -t bitsofinfo/dogapp:2.0.0 -f Dockerfile.dogapp .
docker push bitsofinfo/dogapp:2.0.0

docker build --no-cache --build-arg appname=dogapp --build-arg appversion=3.0.0 -t bitsofinfo/dogapp:3.0.0 -f Dockerfile.dogapp .
docker push bitsofinfo/dogapp:3.0.0

docker build --no-cache --build-arg appname=dogapp --build-arg appversion=4.0.0 -t bitsofinfo/dogapp:4.0.0 -f Dockerfile.dogapp .
docker push bitsofinfo/dogapp:4.0.0



docker build --no-cache --build-arg appname=catapp --build-arg appversion=1.0.0 -t bitsofinfo/catapp:1.0.0 -f Dockerfile.catapp .
docker push bitsofinfo/catapp:1.0.0

docker build --no-cache --build-arg appname=catapp --build-arg appversion=2.0.0 -t bitsofinfo/catapp:2.0.0 -f Dockerfile.catapp .
docker push bitsofinfo/catapp:2.0.0

docker build --no-cache --build-arg appname=catapp --build-arg appversion=3.0.0 -t bitsofinfo/catapp:3.0.0 -f Dockerfile.catapp .
docker push bitsofinfo/catapp:3.0.0

docker build --no-cache --build-arg appname=catapp --build-arg appversion=4.0.0 -t bitsofinfo/catapp:4.0.0 -f Dockerfile.catapp .
docker push bitsofinfo/catapp:4.0.0
```
