# YYFaceDetector
Core Image之人脸识别
####核心代码
----
```swift
func detect() {

    guard let personciImage = CIImage(image: personPic.image!) else { return }
    let accuracy = [CIDetectorAccuracy: CIDetectorAccuracyHigh]
    let faceDetector = CIDetector(ofType: CIDetectorTypeFace, context: nil, options: accuracy)
    let faces = faceDetector?.features(in: personciImage)

    // 转换坐标系
    let ciImageSize = personciImage.extent.size
    var transform = CGAffineTransform(scaleX: 1, y: -1)
    transform = transform.translatedBy(x: 0, y: -ciImageSize.height)

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
详细介绍请参看我的简书[点这里传送](http://www.jianshu.com/p/e371099f12bd)
