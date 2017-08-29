# fishtrip docker images


```sh
#!/bin/sh
set -e

IMAGE_NAME=ruby
VERSION=2.4-fishtrip
REMOTE_URL=registry-internal.cn-beijing.aliyuncs.com/fishtrip
SOURCES_LIST_FILE="../../aliyun.sources.list.online"
EXTRA_HOSTS_FILE="../../hosts"
EXTRA_HOSTS=""

echo "#### Start build image $IMAGE_NAME:$VERSION"
echo

cp $SOURCES_LIST_FILE ./sources.list
while read -r line
do
  [[ "$line" =~ ^#.*$ ]] && continue
  if [ ! -z "$line" ]; then
    str=`echo $line | awk '{printf "--add-host %s:%s", $2, $1}'`
    EXTRA_HOSTS="$EXTRA_HOSTS $str"
  fi
done < $EXTRA_HOSTS_FILE

BUILD_COMMAND="docker build -t $IMAGE_NAME:$VERSION $EXTRA_HOSTS ."
echo "#### Build command: $BUILD_COMMAND"

eval $BUILD_COMMAND

rm -rf ./sources.list

echo
echo "#### Start push image to remote $REMOTE_URL/$IMAGE_NAME:$VERSION"
echo
docker tag $IMAGE_NAME:$VERSION $REMOTE_URL/$IMAGE_NAME:$VERSION
docker push $REMOTE_URL/$IMAGE_NAME:$VERSION
```
