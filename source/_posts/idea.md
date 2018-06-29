---
title: IDEA 中的坑
date: 2018-06-29 15:02:34
tags:
---

# IDEA maven项目中 src下的xml 无法编译到 classes 如：mapper.xml 文件

## 原因：IDEA的maven项目中，默认源代码目录下的xml等资源文件并不会在编译的时候一块打包进classes文件夹，而是直接舍弃掉。

## 解决方案：

### 第一种 建立src/main/resources文件夹，将xml等资源文件放置到这个目录中。maven工具默认在编译的时候，会将resources文件夹中的资源文件一块打包进classes目录中。

### 第二种 配置 maven的pom 文件 在pom 文件中 找到  build 编译配置

```xml

    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
        </resources>
    </build>

```