---
layout: post
title: "图片&amp;视频窗口手势缩放"
date: 2015-07-11 09:46:57 +0800
comments: true
categories: 
---
图片的手势缩放拖拽网上资料很多，但基本只介绍了UIGestureRecognizer相关的添加删除;  
在项目中有需要对视频图像进行手势缩放拖拽，在Android上调整纹理坐标系实现;  
在iOS上，通过调整view实现，实现时考虑到以下几点:  
1. 缩小不小于可需要显示区域  
2. 基于2个触点中心坐标进行缩放  
3. 缩小时，视频区域部分缩至显示区域内的处理  

```c
- (void)handlePan:(UIPanGestureRecognizer *)recognizer
{
    CGPoint translation = [recognizer translationInView:self.playView];
    
    recognizer.view.center = CGPointMake(recognizer.view.center.x + translation.x, recognizer.view.center.y + translation.y);
    [recognizer setTranslation:CGPointZero inView:self.playView];
    
    if (!CGRectContainsRect(self.openGLView.frame, [self getCurrentDisplayRect])) {
        [UIView animateWithDuration:0.3 animations:^{
            recognizer.view.center = CGPointMake(recognizer.view.center.x - translation.x, recognizer.view.center.y - translation.y);
            [recognizer setTranslation:CGPointZero inView:self.playView];
        }];
    }
}
- (void)handlePinch:(UIPinchGestureRecognizer *)recognizer
{
    if ([recognizer state] == UIGestureRecognizerStateBegan) {
        lastScale = 1.0;
    }
    CGPoint center = recognizer.view.center;
    recognizer.scale = recognizer.scale - lastScale + 1;
    CGAffineTransform oldTransform = self.openGLView.transform;
    [UIView animateWithDuration:0.3 animations:^{
        // 缩放，先判断是否有2个触点
        int touchCount = recognizer.numberOfTouches;
        if (touchCount == 2) {
            // 不可缩小至小于显示窗口大小
            if (self.openGLView.frame.size.width * recognizer.scale < [self getCurrentDisplayRect].size.width) {
                return;
            }
            // 1.先计算两个触点中心
            // 2.计算触点中心与原view中心偏移量
            // 3.通过触点中心与(2)缩放后偏移量计算出缩放后中心
            // 4.如果处理后view不包含显示区域，需要对view位置进行调整
            // (1)
            CGPoint point1 = [recognizer locationOfTouch:0 inView:self.view];
            CGPoint point2 = [recognizer locationOfTouch:1 inView:self.view];
            CGPoint tapCenter = CGPointMake((point1.x + point2.x)/2, (point1.y + point2.y)/2);
            // (2)
            CGFloat x = tapCenter.x - center.x;
            CGFloat y = tapCenter.y - center.y;
            // (3)
            CGPoint newCenter = CGPointMake(tapCenter.x - x *recognizer.scale, tapCenter.y - y * recognizer.scale);
            recognizer.view.center = newCenter;
            CGFloat newWidth = self.openGLView.frame.size.width *recognizer.scale;
            CGFloat newHeight = self.openGLView.frame.size.height * recognizer.scale;
            CGRect newRect = CGRectMake(newCenter.x - newWidth / 2, newCenter.y - newHeight / 2, newWidth, newHeight);

            self.openGLView.transform = CGAffineTransformScale(oldTransform, recognizer.scale, recognizer.scale);
            lastScale = recognizer.scale;
            // (4)
            [UIView animateWithDuration:0.3 animations:^{
                if (!CGRectContainsRect(newRect, [self getCurrentDisplayRect])) {
                    CGRect currentDisplayRect = [self getCurrentDisplayRect];
                    CGFloat newCenterX = newCenter.x;
                    CGFloat newCenterY = newCenter.y;
                    if (CGRectGetMinX(newRect) > CGRectGetMinX(currentDisplayRect)) {
                        newCenterX -= CGRectGetMinX(newRect) - CGRectGetMinX(currentDisplayRect);
                    }
                    if (CGRectGetMinY(newRect) > CGRectGetMinY(currentDisplayRect)) {
                        newCenterY -= CGRectGetMinY(newRect) - CGRectGetMinY(currentDisplayRect);
                    }
                    if (CGRectGetMaxX(newRect) < CGRectGetMaxX(currentDisplayRect)) {
                        newCenterX += CGRectGetMaxX(currentDisplayRect) - CGRectGetMaxX(newRect);
                    }
                    if (CGRectGetMaxY(newRect) < CGRectGetMaxY(currentDisplayRect)) {
                        newCenterY += CGRectGetMaxY(currentDisplayRect) - CGRectGetMaxY(newRect);
                    }
                    recognizer.view.center = CGPointMake(newCenterX, newCenterY);
                }
            }];
        }
    }];
}

```
