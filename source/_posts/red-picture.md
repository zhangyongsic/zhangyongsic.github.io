---
title: JAVA处理不正确处理图片ICC信息蒙上红色的问题 放弃ImageIO.read()
date: 2018-08-03 16:31:53
tags:
---

# 放弃 BufferedImage image = ImageIO.read(inputStream)

## ImageIO.read()方法读取图片时可能存在不正确处理图片ICC信息的问题，ICC为JPEG图片格式中的一种头部信息，导致渲染图片前景色时蒙上一层红色。

# 使用 JDK中提供的Image src=Toolkit.getDefaultToolkit()

```java
 Image src=Toolkit.getDefaultToolkit().getImage(file.getPath());

 Image imageTookit = Toolkit.getDefaultToolkit().createImage(bytes);

 BufferedImage image=BufferedImageBuilder.toBufferedImage(src);//Image to BufferedImage

```

# BufferedImageBuilder

```java
package com.core.util;

import javax.swing.*;
import java.awt.*;
import java.awt.image.BufferedImage;

public class BufferedImageBuilder {
    public static BufferedImage toBufferedImage(Image image) {
        if (image instanceof BufferedImage) {
            return (BufferedImage) image;
        }
        image = new ImageIcon(image).getImage();
        BufferedImage bimage = null;
        GraphicsEnvironment ge = GraphicsEnvironment
                .getLocalGraphicsEnvironment();
        try {
            int transparency = Transparency.OPAQUE;
            GraphicsDevice gs = ge.getDefaultScreenDevice();
            GraphicsConfiguration gc = gs.getDefaultConfiguration();
            bimage = gc.createCompatibleImage(image.getWidth(null),
                    image.getHeight(null), transparency);
        } catch (HeadlessException e) {
        }
        if (bimage == null) {
            int type = BufferedImage.TYPE_INT_RGB;
            bimage = new BufferedImage(image.getWidth(null),
                    image.getHeight(null), type);
        }
        Graphics g = bimage.createGraphics();
        g.drawImage(image, 0, 0, null);
        g.dispose();
        return bimage;
    }
}


```


# 特别鸣谢

## https://blog.csdn.net/xz0125pr/article/details/52442717