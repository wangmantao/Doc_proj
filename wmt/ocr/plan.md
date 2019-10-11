# OCR项目
## 资源
1. ocr引擎 tesseract
    
2. 7段LED ssocr


## 
2. 162408 例子：
    http://blog.itpub.net/26736162/viewspace-2285595/
    还讲到tesseract的用法及训练
    训练大体流程为：安装jTessBoxEditor -> 获取样本文件 -> Merge样本文件 –> 生成BOX文件 -> 定义字符配置文件 -> 字符矫正 -> 执行批处理文件 -> 将生成的traineddata放入tessdata中
    
1. 基本命令用法
    tesseract test.png outputFile [–l eng]
