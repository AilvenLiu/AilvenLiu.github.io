---
layout:     post
title:      Series Articles of Java Learning  -- 06
subtitle:   Java学习实录04 -- Thumbnails压缩图片到指定大小

date:       2021-04-28
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - 学习
    - Java
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>  


```java   
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;
import java.math.BigDecimal;
 
import javax.imageio.ImageIO;
 
import net.coobird.thumbnailator.Thumbnails;
 
import org.apache.commons.lang3.StringUtils;
 
public class PicUtils {
     /**
     * 根据指定大小和指定精度压缩图片
     * 
     * @param srcPath
     *            源图片地址
     * @param desPath
     *            目标图片地址
     * @param desFilesize
     *            指定图片大小，单位kb
     * @param accuracy
     *            精度，递归压缩的比率，建议小于0.9
     * @return
     */
    public static String commpressPicForScale(String srcPath, 
        String desPath, long desFileSize, double accuracy) {
        if (StringUtils.isEmpty(srcPath) || StringUtils.isEmpty(srcPath)) {
            return null;
        }
        if (!new File(srcPath).exists()) {
            return null;
        }
        try {
            File srcFile = new File(srcPath);
            long srcFileSize = srcFile.length();
            System.out.println("源图片：" + srcPath + "，大小：" + srcFileSize / 1024
                    + "kb");
 
            // 1、先转换成jpg
            Thumbnails.of(srcPath).scale(1f).toFile(desPath);
            // 递归压缩，直到目标文件大小小于desFileSize
            commpressPicCycle(desPath, desFileSize, accuracy);
 
            File desFile = new File(desPath);
            System.out.println("目标图片：" + desPath + "，大小" + desFile.length()
                    / 1024 + "kb");
            System.out.println("图片压缩完成！");
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
        return desPath;
    }
 
    private static void commpressPicCycle(String desPath, long desFileSize,
            double accuracy) throws IOException {
        File srcFileJPG = new File(desPath);
        long srcFileSizeJPG = srcFileJPG.length();
        // 2、判断大小，如果小于500kb，不压缩；如果大于等于500kb，压缩
        if (srcFileSizeJPG <= desFileSize * 1024) {
            return;
        }
        // 计算宽高
        BufferedImage bim = ImageIO.read(srcFileJPG);
        int srcWdith = bim.getWidth();
        int srcHeigth = bim.getHeight();
        int desWidth = new BigDecimal(srcWdith).multiply(
                new BigDecimal(accuracy)).intValue();
        int desHeight = new BigDecimal(srcHeigth).multiply(
                new BigDecimal(accuracy)).intValue();
 
        Thumbnails.of(desPath).size(desWidth, desHeight)
                .outputQuality(accuracy).toFile(desPath);
        commpressPicCycle(desPath, desFileSize, accuracy);
    }
 
}

```