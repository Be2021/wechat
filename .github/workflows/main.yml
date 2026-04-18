#import <UIKit/UIKit.h>
#import <QuartzCore/QuartzCore.h>

// 微信首页聊天列表Cell类名（通常为MMTableViewCell或类似）
// 这里使用通用hook方式捕获所有UITableViewCell的点击

%hook UITableViewCell

- (void)setSelected:(BOOL)selected animated:(BOOL)animated {
    %orig(selected, animated);
    
    // 判断是否在WeChat进程中且是首页的Cell
    if (![NSBundle.mainBundle.bundleIdentifier isEqualToString:@"com.tencent.xin"]) {
        return;
    }
    
    // 获取当前ViewController判断是否在首页
    UIViewController *vc = [self currentViewController];
    if (!vc) return;
    
    // 微信首页通常为"MMMainViewController"或"MainFrameViewController"
    NSString *vcName = NSStringFromClass([vc class]);
    if (![vcName containsString:@"Main"] && ![vcName containsString:@"Session"]) {
        return;
    }
    
    if (selected) {
        // 按压动画 - 缩小
        [UIView animateWithDuration:0.1
                              delay:0
                            options:UIViewAnimationOptionCurveEaseInOut
                         animations:^{
            self.contentView.transform = CGAffineTransformMakeScale(0.96, 0.96);
            self.contentView.alpha = 0.8;
        } completion:nil];
    } else {
        // 松开动画 - 弹回
        [UIView animateWithDuration:0.2
                              delay:0
             usingSpringWithDamping:0.6
              initialSpringVelocity:0.8
                            options:UIViewAnimationOptionCurveEaseInOut
                         animations:^{
            self.contentView.transform = CGAffineTransformIdentity;
            self.contentView.alpha = 1.0;
        } completion:nil];
    }
}

// 辅助方法：获取当前ViewController
%new
- (UIViewController *)currentViewController {
    UIWindow *keyWindow = nil;
    for (UIWindow *window in [UIApplication sharedApplication].windows) {
        if (window.isKeyWindow) {
            keyWindow = window;
            break;
        }
    }
    
    UIViewController *rootVC = keyWindow.rootViewController;
    UIViewController *presentedVC = rootVC.presentedViewController;
    
    while (presentedVC) {
        rootVC = presentedVC;
        presentedVC = rootVC.presentedViewController;
    }
    
    if ([rootVC isKindOfClass:[UINavigationController class]]) {
        return [(UINavigationController *)rootVC topViewController];
    }
    
    if ([rootVC isKindOfClass:[UITabBarController class]]) {
        UIViewController *selectedVC = [(UITabBarController *)rootVC selectedViewController];
        if ([selectedVC isKindOfClass:[UINavigationController class]]) {
            return [(UINavigationController *)selectedVC topViewController];
        }
        return selectedVC;
    }
    
    return rootVC;
}

%end

// 备用方案：直接hook touchesBegan/touchesEnded实现更即时的反馈
%hook UITableViewCell

- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    %orig(touches, event);
    
    if (![NSBundle.mainBundle.bundleIdentifier isEqualToString:@"com.tencent.xin"]) {
        return;
    }
    
    // 立即执行缩小动画
    [UIView animateWithDuration:0.08 animations:^{
        self.contentView.layer.transform = CATransform3DMakeScale(0.95, 0.95, 1.0);
    }];
}

- (void)touchesEnded:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    %orig(touches, event);
    
    if (![NSBundle.mainBundle.bundleIdentifier isEqualToString:@"com.tencent.xin"]) {
        return;
    }
    
    // 弹性恢复
    [UIView animateWithDuration:0.25
                          delay:0
         usingSpringWithDamping:0.5
          initialSpringVelocity:1.0
                        options:UIViewAnimationOptionCurveEaseOut
                     animations:^{
        self.contentView.layer.transform = CATransform3DIdentity;
    } completion:nil];
}

- (void)touchesCancelled:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    %orig(touches, event);
    
    if (![NSBundle.mainBundle.bundleIdentifier isEqualToString:@"com.tencent.xin"]) {
        return;
    }
    
    [UIView animateWithDuration:0.2 animations:^{
        self.contentView.layer.transform = CATransform3DIdentity;
    }];
}

%end
