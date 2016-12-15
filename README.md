# YYFaceDetector
Core Image之人脸识别
####核心代码
----
```swift
func detect() {
	// 将ImageView中的图片转换为CIImage（使用CoreImage时需要用CIImage）
    guard let personciImage = CIImage(image: personPic.image!) else { return }
    // 设置图像处理能力（CIDetectorAccuracyHigh：处理能力强，精确度高）
    let accuracy = [CIDetectorAccuracy: CIDetectorAccuracyHigh]
    // 定义一个属于CIDetector类的faceDetector变量，并输入之前创建的accuracy变量
    let faceDetector = CIDetector(ofType: CIDetectorTypeFace, context: nil, options: accuracy)
    // 调用faceDetector的features(in: image)方法，识别器会找到所给图像中的人脸，最后返回一个人脸数组
    let faces = faceDetector?.features(in: personciImage)

    // 转换坐标系
    // 这里需要说明一下：UIView的坐标系和CoreImage的坐标系不一样，想要正确的识别人脸必须要转换坐标系
    let ciImageSize = personciImage.extent.size
    var transform = CGAffineTransform(scaleX: 1, y: -1)
    transform = transform.translatedBy(x: 0, y: -ciImageSize.height)
	
	// 遍历人脸数组中的人脸 
    for face in faces as! [CIFaceFeature] {
        print("Found bounds are \(face.bounds)")     
        // 应用变换转换坐标
        var faceViewBounds = face.bounds.applying(transform)
        // 在图像视图中计算矩形的实际位置和大小
        let viewSize = personPic.bounds.size
        let scale = min(viewSize.width / ciImageSize.width, viewSize.height / ciImageSize.height)
        let offsetX = (viewSize.width - ciImageSize.width * scale) / 2
        let offsetY = (viewSize.height - ciImageSize.height * scale) / 2

        faceViewBounds = faceViewBounds.applying(CGAffineTransform(scaleX: scale, y: scale))
        faceViewBounds.origin.x += offsetX
        faceViewBounds.origin.y += offsetY
		// 创建面部识别方框
        let faceBox = UIView(frame: faceViewBounds)
        faceBox.layer.borderWidth = 3
        faceBox.layer.borderColor = UIColor.red.cgColor
        faceBox.backgroundColor = UIColor.clear 
        personPic.addSubview(faceBox)

        if face.hasLeftEyePosition {
            print("Left eye bounds are \(face.leftEyePosition)")
        }

        if face.hasRightEyePosition {
            print("Right eye bounds are \(face.rightEyePosition)")
        }
    }
}
```
运行效果：

详细介绍请参看我的简书 [点这里传送](http://www.jianshu.com/p/e371099f12bd)
