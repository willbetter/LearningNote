# 屏幕显示图像原理与优化

![](../resourse/images/1121315-0d69540f99a2b731.png)



# **浅谈ios中图片的解压缩**

## 图片加载的工作流

概括来说，从磁盘加载一张图片，并将它显示到屏幕上，中间的主要工作流如下所示：

1. 假设我们使用`imageNamed：`方法加载图片，这个时候的图片并没有解压缩。
2. 然后将生成的UIImage赋值给UIImageView。
3. 接着系统捕捉到UIImageView图层树的变化。
4. 在主线程下一个runloop到来时，Core Animation 提交了这个隐式的 transaction ，这个过程可能会对图片进行 copy 操作，而受图片是否**字节对齐**等因素的影响，这个 copy 操作可能会涉及以下部分或全部步骤：
   1. 分配内存缓冲区用于管理文件IO和加压缩操作；
   2. 将文件数据从磁盘读到内存中；
   3. 将压缩的图片数据解码成未压缩的位图形式，这是一个非常耗时的CPU操作；
   4. 最后 Core Animation 使用未压缩的位图数据渲染 `UIImageView` 的图层。

在以上的步骤中，图片的解压缩是一个非常耗时的CPU操作，并且默认是在主线程中执行的。那么当需要加载的图片比较多时候，就会对我们的应用响应性造成严重的影响。

## 为什么需要解压缩？

**位图定义**：A bitmap image (or sampled image) is an array of pixels (or samples). Each pixel represents a single point in the image. JPEG, TIFF, and PNG graphics files are examples of bitmap images.

我们的图像要想显示出来，必须被解压成Bitmap格式的位图（使用数组表示图形每个点上的rgba色值），**事实上，JPEG、PNG都是压缩的位图格式，如果想显示出来就必须解压**。因此在将磁盘中图片渲染到屏幕之前，必须先得到图片的原始像素数据，才能执行后续的绘制操作，这也是需要对图片进行解压的原因。

## 强制解压原理

当未解压缩的图片将要渲染到屏幕时，系统会在主线程对图片进行解压缩，而如果图片已经解压缩了，系统就不会再对图片进行解压缩。因此，也就有了业内的解决方案，在子线程提前对图片进行强制解压缩。

强制解压缩的原理就是对图片进行重新绘制，得到一张新的解压缩后的位图。其中用到的核心函数就是`CGBitmapContextCreate`.

顾名思义，这个函数用于创建一个位图上下文，用来绘制一张宽 `width` 像素，高 `height`像素的位图。

**YYKit中关于图片解压函数的实现，如下所示：**

```c
CGImageRef YYCGImageCreateDecodedCopy(CGImageRef imageRef, BOOL decodeForDisplay) {
    ...

    if (decodeForDisplay) { // decode with redraw (may lose some precision)
        CGImageAlphaInfo alphaInfo = CGImageGetAlphaInfo(imageRef) & kCGBitmapAlphaInfoMask;

        BOOL hasAlpha = NO;
        if (alphaInfo == kCGImageAlphaPremultipliedLast ||
            alphaInfo == kCGImageAlphaPremultipliedFirst ||
            alphaInfo == kCGImageAlphaLast ||
            alphaInfo == kCGImageAlphaFirst) {
            hasAlpha = YES;
        }

        // BGRA8888 (premultiplied) or BGRX8888
        // same as UIGraphicsBeginImageContext() and -[UIView drawRect:]
        CGBitmapInfo bitmapInfo = kCGBitmapByteOrder32Host;
        bitmapInfo |= hasAlpha ? kCGImageAlphaPremultipliedFirst : kCGImageAlphaNoneSkipFirst;

        CGContextRef context = CGBitmapContextCreate(NULL, width, height, 8, 0, YYCGColorSpaceGetDeviceRGB(), bitmapInfo);
        if (!context) return NULL;

        CGContextDrawImage(context, CGRectMake(0, 0, width, height), imageRef); // decode
        CGImageRef newImage = CGBitmapContextCreateImage(context);
        CFRelease(context);

        return newImage;
    } else {
        ...
    }
}
```

