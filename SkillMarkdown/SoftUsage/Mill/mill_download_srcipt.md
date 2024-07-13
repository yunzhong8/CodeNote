```shell
#!/usr/bin/env sh
# This is a wrapper script, that automatically download mill from GitHub release pages
# You can give the required mill version with MILL_VERSION env variable
# If no version is given, it falls back to the value of DEFAULT_MILL_VERSION

set -e # 设置脚本内某一个指令执行失败，则停止执行

# 如果没有定义DEFAULT_MILL_VERSION这个环境变量，则定义该变量并设置值为0.11.7
if [ -z "${DEFAULT_MILL_VERSION}" ] ; then
  DEFAULT_MILL_VERSION=0.11.7
fi

# 如果没有定义MILL_Version这个环境变量，则设置mill_version的值
# 能从其他文件获取则获取，不能则设置为默认值
if [ -z "$MILL_VERSION" ] ; then
  # 如果当前文件存在.mill-version存在
  # 则获取.mill-verison的第一行，如果head - n 1 .mill-version执行错误，
  # 则将其标准错误输出到/dev/null中
  # 获取mill-version中的版本号
  if [ -f ".mill-version" ] ; then
    MILL_VERSION="$(head -n 1 .mill-version 2> /dev/null)"
   # 如果当前目录存在.congi/mill-version，存在则
  elif [ -f ".config/mill-version" ] ; then
    MILL_VERSION="$(head -n 1 .config/mill-version 2> /dev/null)"
  # 如果mill存在，但是这个脚本名称不是mill
  elif [ -f "mill" ] && [ "$0" != "mill" ] ; then
#从mill文件查找包含字符DEFAULT_MILL_VERSION=的行，
#按照=切割，去后切割后的第二个元素
    MILL_VERSION=$(grep -F "DEFAULT_MILL_VERSION=" "mill" | head -n 1 | cut -d= -f2)
  else
    # 都没有设置以默认的mill版本作为mill_version的值
    MILL_VERSION=$DEFAULT_MILL_VERSION
  fi
fi


# 设置mill下载路径
# 如果存在XDG_CACHE_HOME这个环境变量，则设置mill的下载路径为XDG_CACHE_HOME/mill/dpwnlad
if [ "x${XDG_CACHE_HOME}" != "x" ] ; then
  MILL_DOWNLOAD_PATH="${XDG_CACHE_HOME}/mill/download"
else
# 如果没有这个环境变量，则设置home目录下的.cache作为下载路径
  MILL_DOWNLOAD_PATH="${HOME}/.cache/mill/download"
fi

# 设mill的执行路径为：下载路径/mill_版本
# MILL_EXEC_PATH=/home/.cache/mill/download/0.11.7
MILL_EXEC_PATH="${MILL_DOWNLOAD_PATH}/${MILL_VERSION}"

#将版本号字符串化
version_remainder="$MILL_VERSION"

#设置mill的主版本号MILL_MAJOR_VERSION=0
#version_remainder=11.7
MILL_MAJOR_VERSION="${version_remainder%%.*}"; version_remainder="${version_remainder#*.}"

#设置mill的小版本号MILL_MAJOR_VERSION=11
#version_remainder=7
MILL_MINOR_VERSION="${version_remainder%%.*}"; version_remainder="${version_remainder#*.}"


# 如果$MILL_EXEC_PATH指定的文件不存在这创建它

if [ ! -s "$MILL_EXEC_PATH" ] ; then
  mkdir -p "$MILL_DOWNLOAD_PATH" # 创建下载路径

  # 如果$MILL_MAJOR_VERSION>0,或者$MILL_MINOR_VERSION>=5
  #则设置ASSEMBLY 值为"-assembly"
  if [ "$MILL_MAJOR_VERSION" -gt 0 ] || [ "$MILL_MINOR_VERSION" -ge 5 ] ; then
    ASSEMBLY="-assembly"
  fi
  #DownLoad_File = /home/.cache/mill/download/0.11.7-tmp-download
  DOWNLOAD_FILE=$MILL_EXEC_PATH-tmp-download # 设置下载后文件的名称
  #获取mill的版本号mill_version_tag = 0.11
  MILL_VERSION_TAG=$(echo $MILL_VERSION | sed -E 's/([^-]+)(-M[0-9]+)?(-.*)?/\1\2/')

  # 设置mill的原创下载连接
  MILL_DOWNLOAD_URL="https://repo1.maven.org/maven2/com/lihaoyi/mill-dist/$MILL_VERSION/mill-dist-$MILL_VERSION.jar"
  #这段代码的作用是通过 curl 命令从 Maven 仓库下载特定版本的 mill-dist 工具的 JAR 文件，并将其保存为指定的文件名中。
  curl --fail -L -o "$DOWNLOAD_FILE" "$MILL_DOWNLOAD_URL"

  chmod +x "$DOWNLOAD_FILE" # 赋予下载的文件的执行权

  #DownLoad_File = /home/.cache/mill/download/0.11.7-tmp-download
  #MILL_EXEC_PATH=/home/.cache/mill/download/0.11.7
  mv "$DOWNLOAD_FILE" "$MILL_EXEC_PATH" # 将下载文件改名


  # 删除环境变量
  unset DOWNLOAD_FILE 
  unset MILL_DOWNLOAD_URL
fi

# 设置 MILL_MAIN_CLI=mill
if [ -z "$MILL_MAIN_CLI" ] ; then
  MILL_MAIN_CLI="${0}"
fi

#设置第一个参数为空
MILL_FIRST_ARG=""

#这段代码的作用是检查脚本的第一个参数是否匹配指定的选项，如果匹配，则将其保存到 MILL_FIRST_ARG 变量中，并将其从参数列表中移除
 # first arg is a long flag for "--interactive" or starts with "-i"
if [ "$1" = "--bsp" ] || [ "${1%"-i"*}" != "$1" ] || 
   [ "$1" = "--interactive" ] || [ "$1" = "--no-server" ]
   || [ "$1" = "--repl" ] || [ "$1" = "--help" ] ; then
  # Need to preserve the first position of those listed options
  MILL_FIRST_ARG=$1
  shift
fi

# 删除环境变量
unset MILL_DOWNLOAD_PATH
unset MILL_VERSION

#运行下载的可执行程序
exec $MILL_EXEC_PATH $MILL_FIRST_ARG -D "mill.main.cli=${MILL_MAIN_CLI}" "$@"
```
