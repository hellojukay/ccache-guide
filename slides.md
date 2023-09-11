---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

# Ccache 介绍

研发效能 - 李聪

---

## Ccache 是什么？
Ccache是​​编译器缓存。它通过缓存以前的编译并检测何时再次进行相同的编译来加速重新编译 。Ccache 是一个开源项目，他的 Github 地址是 [Ccache](https://github.com/ccache/ccache)
, 目前有 2k stars。 Ccache 有一些特点

- 使用简单, 无侵入
- 支持多种缓存方式
- 支持多种缓存策略

Read more about [Ccache 官网](https://ccache.dev/)

---

### 编译器支持
- GCC
- Clang
- Microsoft Visual C++

--- 

### 操作系统支持
- Windows
- Linux , Unix-like

---

### 缓存存储方式
- Local
- Redis
- HTTP
- Google Cloud Storage
- NFS

---

## 安装 Ccache

## Ubuntu
```
apt install ccache
```

### Mac OS
```
brew install ccache
```
ccache 命令
```
$ ccache-guide $ ccache --version
ccache version 4.8.2
Features: file-storage http-storage redis+unix-storage redis-storage

Copyright (C) 2002-2007 Andrew Tridgell
Copyright (C) 2009-2023 Joel Rosdahl and other contributors

See <https://ccache.dev/credits.html> for a complete list of contributors.

This program is free software; you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation; either version 3 of the License, or (at your option) any later
version.
```
---

# 通常构建工具
1. 手动编译
```
gcc main.c -o a.out
g++ main.ccc -o a.out
```

2. make 编译
```
make
```
或者
```
./configure
make
```
或者更现代的 cmake
```
cmake .
make
```
---

## 问题?
Request: 当一台机器上安装了多种编译器，make 和 cmake 是用哪个编译器编译代码呢 ?  

Answer: 使用环境变量 CC 控制 C 代码的编译器，使用环境变量 CXX 控制 CPP 代码的编译器。
```
CC=gcc CXX=g++ make
```
或者
```
make CC=gcc CXX=g++
```
---

# 缓存使用方式
1. alias 编译命令
```
alias gcc="ccache gcc"
alis g++="ccache gcc"
```

2. 软连接
```
ln -snf /usr/bin/ccache /usr/local/bin/gcc
ln -snf /usr/bin/ccache /usr/local/bin/g++
export PATH=/usr/local/bin/;$PATH
```
3. 或者
```
mkdir /opt/bin/
cp /usr/bin/ccache /opt/bin/gcc
cp /usr/bin/ccache /opt/bin/g++
export PATH=/opt/bin;$PATH
```

---

## Ccache 与 常用构建工具
1. 为 make设置 CC 和 CXX 环境变量
```
export CC="ccache gcc"
export CXX="ccache g++"
make
```

2. cmake 使用 ccache 

cmake 3.4 版本
```
cmake -DCMAKE_CXX_COMPILER_LAUNCHER=ccache
```
cmake 3.7 版本
```
export CMAKE_C_COMPILER_LAUNCHER=ccache
export CMAKE_CXX_COMPILER_LAUNCHER=ccache
cmake
```
---

## 常用命令
1. 设置缓存大小
```
ccache -M 30G
```
2. 清理缓存
```
ccache -c -C -z
```
3. 设置存储
```
export CCACHE_REMOTE_STORAGE=redis://pass@YOUR_SERVER
export CCACHE_REMOTE_STORAGE=http://YOUR_SERVER/cache/
```
4. 查看配置
```
$ ccache -p
(default) absolute_paths_in_stderr = false
(default) base_dir =
(default) cache_dir = /Users/licong/Library/Caches/ccache
(default) compiler =
(default) compiler_check = mtime
...
```

---

## 缓存命中情况
```
$ ccache -s 
Cacheable calls:   3632 / 5103 (71.17%)
  Hits:            3285 / 3632 (90.45%)
    Direct:        3284 / 3285 (99.97%)
    Preprocessed:     1 / 3285 ( 0.03%)
  Misses:           347 / 3632 ( 9.55%)
Uncacheable calls: 1471 / 5103 (28.83%)
Local storage:
  Cache size (GB):  0.0 /  8.0 ( 0.00%)
Remote storage:
  Hits:            3285 / 3632 (90.45%)
  Misses:           347 / 3632 ( 9.55%)
  Errors:           348
```
---

# 其他
Ccache 还有一个老表 [sccache](https://github.com/mozilla/sccache),Rust 编译也是非常耗时的， sccache 提供了对 Rust 的支持。

Ccache 经常和 [distcc](https://www.distcc.org/) 一起配合使用， distcc 是一个分布式编译系统，能在多台机器上分发编译任务。

---

# 相关链接
- https://github.com/ccache/ccache
- https://ccache.dev/
- https://github.com/mozilla/sccache
- https://www.distcc.org/
- https://stackoverflow.com/questions/1815688/how-to-use-ccache-with-cmake