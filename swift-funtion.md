1. 控制图片360度自身旋转

```objc
// 第一种方法
func startSync() -> Void {

 let endAngle = CGAffineTransformMakeRotation(angle * (CGFloat(M_PI) / 180.0))

 UIView.animateWithDuration(0.01, delay: 0, options: UIViewAnimationOptions.Repeat, animations: {

 self.footerButton.transform = endAngle

 }) { (finished: Bool) in

 self.angle += 2

 self.startSync()
 }

// 第二种方法
func startSync() -> Void {

let animation:CABasicAnimation = CABasicAnimation(keyPath: "transform.rotation")

 animation.byValue = (M_PI*2)

 animation.duration = 1.0

 animation.repeatCount = Float.infinity

 animation.cumulative = true

 animation.removedOnCompletion = false

 self.footerButton.layer.addAnimation(animation, forKey: "rotation")
}


// 停止动画
func endSync() -> Void {

 self.footerButton.layer.removeAnimationForKey("rotation")

 }

```
