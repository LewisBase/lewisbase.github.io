---
layout: post
title: Python处理PDF文件
subtitle: 简译与总结
date: 2019-04-19
author: lewisbase
header-img:
categories: 
    - Python
    - pyPDF
    - Translation
---

最近看到一篇介绍Python中pyPDF模块的文章，详细介绍了使用pyPDF模块获取PDF文件信息，合并拆分PDF文件等功能。很方便，在此搬运分享以下：

[How to Work With a PDF in Python](https://realpython.com/pdf-python/)  

全文介绍了以下几方面的功能

* 提取文件信息
* 旋转页面
* 合并文件
* 拆分文件
* 添加水印
* 加密文件

这里我主要尝试了前几个功能的实现，添加水印与加密文件不是很用得上就不再详细尝试了。

## pyPdf，PyPDF2以及PyPDF4的发展历程

最初的pyPdf模块发布与2005年，但并不支持Python3。PyPDF2目前也基本停用，最新版本的PyPDF4支持PyPDF2的大多数功能，但也有部分功能不兼容。原文中使用的是PyPDF2模块，此处我改用最新的PyPDF4进行尝试。

## 安装

如果你已经安装了Anaconda，可以使用pip或者conda直接安装：
    pip install PyPDF4
    
## 功能实现

### 提取PDF文件信息

我们可以通过`PdfFileReader`来实现对以下信息的提取：

* 作者
* 创建者
* 生产商
* 主题
* 题目
* 页数

代码如下：

    from PyPDF4 import PdfFileReader，PdfFileWriter
    
    def extract_information(pdf_path):
        with open(pdf_path,'rb') as f:
            pdf=PdfFileReader(f)
            information=pdf.getDocumentInfo()
            number_of_pages=pdf.getNumPages()
            
        txt=f"""
        Information about {pdf_path}
        
        Author: {information.author}
        Creator: {information.creator}
        Producer: {information.producer}
        Subject: {information.subject}
        Title: {information.title}
        Number of pages: {number_of_pages}
        """
        print(txt)
        return information
    
    if __name__ == '__main__':
        path='visa_letter.pdf'
        extract_information(path)

主要通过`getDocumentInfo()`与`getNumPages()`两个函数来实现读取信息的功能。如果想进一步读取PDF中的文本信息，可以参照[PDFMiner.six](https://github.com/pdfminer/pdfminer.six)（Python3支持）

`if __name__ == '__main__'`则放在自己创建的模块底部，指明当该模块作为main函数调用时的执行情况。

### 旋转页面

旋转页面功能需要导入PdfFileWriter模块，上文中以导入，此处不再重复，代码如下：

    def rotate_pages(pdf_path,pages=[0],direction='right'):
        """Parameters: filename, pages want to rotate and rotate direction"""
        pdf_writer=PdfFileWriter()
        pdf_reader=PdfFileReader(pdf_path)
        outputname=pdf_path[:-4]+'-rotate.pdf'
        for page in pages:
            # Rotate page 90 degrees to the right
            if direction=='right':
                page_r=pdf_reader.getPage(page).rotateClockwise(90)
                pdf_writer.addPage(page_r)
            # Rotate page 90 degrees to the left
            elif direction=='left':
                page_r=pdf_reader.getPage(page).rotateCounterClockwise(90)
                pdf_writer.addPage(page_r)
            else:
                print("Encountered an improper argument! Input right or left.")
        
        with open(outputname,'wb') as fh:
            pdf_writer.write(fh)

旋转页码通过`getPage()`和`rotateClockwise() rotateCounterClockwise()`实现，`getPage()`获取要旋转页的索引，`rotateClockwise() rotateCounterClockwise()`控制旋转方向。

`addPage()`将要输出的页添加到流（不清楚该如何严谨表述，依稀记得C++里是这么称呼的）中，最后的新文件通过`write()`创建。

这里我将原文的函数改进了以下，加入两个表明要旋转的页码以及旋转方向的关键字参数，页码通过列表传递，默认将第一页顺时针旋转90度。

这里的`else`情况似乎抛出个异常更为合理，但我还没有掌握异常的处理方法。

### 合并文件

合并文件的核心则是对每个文件的每一页进行枚举，再逐次添加到流里，最后统一输出：

    def merge_pdfs(paths,output):
        pdf_writer=PdfFileWriter()
        for path in paths:
            pdf_reader=PdfFileReader(path)
            for page in range(pdf_reader.getNumPages()):
                pdf_writer.addPage(pdf_reader.getPage(page))
                
        with open(output,'wb') as out:
            pdf_writer.write(out)

要合并的文件以列表的形式传入即可。

### 拆分文件

这里遇到了个问题，使用PyPDF4在拆分循环中会出现`PdfFileWriter' object has no attribute 'stream' `的错误，似乎是个bug。

这可能是因为将读入文件步骤写在了循环外导致写出过程紊乱，在循环内再此读入文件可以解决，详情见[PyPDF2 PdfFileWriter has no attribute stream](https://stackoverflow.com/questions/40168027/pypdf2-pdffilewriter-has-no-attribute-stream)。

PyPDF2有时也会遇到同样的错误，但此处使用PyPDF2不会有该错误。

    def split(pdf_path,name_of_split):
        pdf_reader=PdfFileReader(pdf_path)
        for page in range(pdf_reader.getNumPages()):
            pdf_reader=PdfFileReader(pdf_path)
            pdf_writer=PdfFileWriter()
            pdf_writer.addPage(pdf_reader.getPage(page))
            output=f'{name_of_split}{page}.pdf'
            with open(output,'wb') as output_pdf:
                pdf_writer.write(output_pdf)

## 总结

至此一个具有简易功能的PDF编辑器就基本实现了，在使用时可以将PDFeditor.py最后一部分修改为：

    def main():
        pass
    
    if __name__ == '__main__':
        main()

防止直接调用，具体调用方法则为：

    import PDFeditor
    split('visa_letter.pdf','s')
    merge_pdfs(['s0.pdf','s1.pdf'],'m.pdf')
    rotate_pages('m.pdf',[0,1],'left')
    extract_information('m.pdf')

