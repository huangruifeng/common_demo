#### Pdf文件格式
> PDF是一种结构化的文件格式，一个PDf文件是由多个对象模块组成的。每个对象都有一个数字来标识，这样就可以被其他对象所引用了。这些对象不需要按照顺序出现在PDF文档里面，出现的顺序可以是任意的，比如一个PDF文件有3页，第3页可以出现在第1页以前，对象按照顺序出现唯一的好处就是能够增加文件的可读性，如果你不会用文本编辑器来阅读PDF结构，那么大可不必关心该顺序。正是因为页与页之间的不相关性，就能够对PDF文件的页面进行随机的访问.

##### 概要
一个PDF文件可以分为四个部分：
- 文件头 DPF所遵循的版本号。
- 文件体 PDF文件的主要部分。（由对象组成）
- 交叉引用表。
- 文件尾 指明文件的跟对象，找到文件中的各个对象的位置，达到随机访问，以及其他信息。

##### 文件头
文件的第一行，指明这个pdf文件所遵循的版本号，例如：
```
%PDF-1.0
%PDF-1.5
```
#####  基本语法
PDF中对象的定义：
一个对象的一个对象的第一行一般有两个数字和关键字obj。
```
4 0 obj  
<<
/TYpe/Pages
/Count 1
>>
endobj
```
第一个数字是对象号，唯一标识一个对象。第二个用来记录，它被创建后所进行的第几次修改，刚创建就是0。对象的内容在<<>>之间，最后以endobj结束。

##### HelloWorld

```
%PDF-1.0
1 0 obj
<<
/Type /Catalog
/Pages 3 0 R
/Outlines 2 0 R
>>
endobj
```
/Type /Catalog说明该对象的类型为/Catalog，/Pages 3 0 R，这里/Pages指的是这个根对象包含的/Pages的目标是对象号为3的对象，3 0 R的意思是对对象3的引用。
```
2 0 obj
<<
/Type /Outlines
/Count 0
>>
endobj
```
outline对象（此处它的计数为0，说明没有书签）

```
3 0 obj
<<
/Type /Pages
/Count 1
/Kids [4 0 R]
>>
endobj
```
pages对象（页面组对象），/Type /Pages 说明自身的属性，对象的类型为页码，/Count 1说明页码数量为1，/Kids [4 0 R]说明它的孩子、页的对象号为4,如果有多个页面，就有多个页面对象的引用，例如/Kids [4 0 R 10 0 R], 就说明该PDF的第一页的对象号是4,第二页的对象号是10

```
4 0 obj
<<
/Type /Page
/Parent 3 0 R
/Resources << /Font << /F1 7 0 R >> /ProcSet 6 0 R >>
/MediaBox [0 0 612 792]
/Contents 5 0 R
>>
endobj
```
页对象，/Parent 3 0 R说明其父对象的对象号为3，及Pages对象，/Resources << /Font << /F1 7 0 R >> /ProcSet 6 0 R >>说明该页所要包含的资源，包括字体和内容的类型，/MediaBox [0 0 612 792]说明页面的显示大小（以象素为单位），/Contents 5 0 R说明页面内容对象的对象号为5。
```
5 0 obj
<< /Length 44 >>
stream
BT
/F1 24 Tf
100 100 Td (Hello World) Tj
ET
endstream
endobj
```
<< /Length 44 >>说明stream对象为字节数，从BT开始，ET结束，包括中间的行结束符。
Stream说明一个流对象的开始。
BT说明一个文字对象的开始。
/F1 24 Tf，Tf说明True font对象，字体明为F1, 大小为24个象素。
100 150 Td (Hello World) Tj，100 100 说明这一行文字放置的位置，对于Td, 我们可以这样理解，我们的当前X,Y坐标分别加上100和150就是文本的位置，因为在该例子中只有一个对象，那么它的位置就是(100,150), 如果下个对象位置信息为100, 50 Td, 那么它的位置应该就是（100+100, 150+50）也就是（200，200）。（Hello World） Tj说明文本的内容，当然，如果这里是文本的内容写成16进制，则用<>包含。
ET说明文字对象的结束标志。
Endstream为流对象的结束标志
```
6 0 obj
[/PDF /Text]
Endobj
```
[/PDF /Text]说明PDF的内容类型仅仅为文本，如果有图片则为[/PDF /Image]
```
7 0 obj
<<
/Type /Font
/Subtype /Type1
/Name /F1
/BaseFont /Helvetica
>>
endobj
```
字体对象。

```
xref
0 8
0000000000 65535 f
0000000009 00000 n
0000000074 00000 n
0000000120 00000 n
0000000179 00000 n
0000000322 00000 n
0000000415 00000 n
0000000445 00000 n
```
ref说明一个交叉引用表的开始，交叉引用表的第一行0 8 说明下面各行所描述的对象号是从0开始，并且有8个对象.
0000000000 65535 f，一般每个PDF文件都是以这一行开始交叉应用表的，说明对象0的起始地址为0000000000,最大修改次数为65535 f表明对象是free(头)。
0000000009 00000 n就是表示对象1，也就是catalog对象了，0000000009是其偏移地址，00000为5位产生号（最大为65535），0表明该对象未被修改过,  n表示该对象在使用。

```
Trailer
<<
/Size 8
/Root 1 0 R
>>
startxref
553
%%EOF
```
trailer说明文件尾trailer对象的开始。
/Size 8说明该PDF文件的对象数目。
/Root 1 0 R说明根对象的对象号为1。
Startxref 553说明交叉引用表的偏移地址，从而可以找到PDF文档中所有的对象的相对地址，进而访问对象
%%EOF为文件结束标志。


##### 结构描述

文件尾（Trail），说明根对象的对象号，并且说明交叉引用表的位置，通过对交叉引用表的查询可以找到目录对象(Catalog)。这个目录对象是该PDF文档的根对象，包含PDF文档的大纲(outline)和页面组对象（pages）引用。大纲对象是指PDF文件的书签树；页面组对象（pages）包含该文件的页面数，各个页面对象(page)的对象号。页面（page）对象作为PDF中最重要的对象，包含如何显示该页面的信息，例如使用的字体，包含的内容（文字，图片等），页面的大小。里面的信息可以直接给出，当然里面的子项更多的是对其他对象的引用，真正的信息存放在其他对象里面。页面中包含的信息是包含在一个称为流（stream）的对象里，这个流的长度（字节数）必须直接给出或指向另外一个对象（包含一个整数值，表明这个流的长度）

---
##### 参考资料

[官网](http://www.adobe.com/content/dam/acom/en/devnet/acrobat/pdfs/pdf_reference_1-7.pdf)
[PDF文件结构（一）](http://blog.csdn.net/bobob/article/details/4328426)
[PDF文件结构（二）](http://blog.csdn.net/bobob/article/details/4328450)
[一个简单的PDF文件结构](http://blog.csdn.net/pdfMaker/article/details/573990)