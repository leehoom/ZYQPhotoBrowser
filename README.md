# ZYQPhotoBrowser ![](http://cocoapod-badges.herokuapp.com/v/ZYQPhotoBrowser/badge.png) ![](http://cocoapod-badges.herokuapp.com/p/ZYQPhotoBrowser/badge.png)

ZYQPhotoBrowser is a new implementation based on [MWPhotoBrowser](https://github.com/mwaterfall/MWPhotoBrowser).

We've added both user experience and technical features inspired by Facebook's and Tweetbot's photo browsers.

## Screenshots

![Screenshot1](http://heroims.github.io/ZYQPhotoBrowser/Untitled1.gif "Screenshot1") 

![Screenshot2](http://heroims.github.io/ZYQPhotoBrowser/Untitled2.gif "Screenshot1") 

![Screenshot2](http://heroims.github.io/ZYQPhotoBrowser/Untitled3.gif "Screenshot1") 

## Usage

See the code snippet below for an example of how to implement the photo browser.

First create a photos array containing ZYQPhoto objects:

``` objective-c
// URLs array
NSArray *photosURL = @[
                      [NSURL URLWithString:@"https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1498113175410&di=bd10bcd2b2a45906259f4e823bbf9df6&imgtype=0&src=http%3A%2F%2Fpic.58pic.com%2F58pic%2F14%2F27%2F45%2F71r58PICmDM_1024.jpg"]
                      ,[NSURL URLWithString:@"https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1498113236506&di=004225eba24d74087feaeaa9484db047&imgtype=0&src=http%3A%2F%2Fic.topit.me%2Fc%2F3c%2Ff6%2F1110230348da8f63cco.jpg"]
                      ,[NSURL URLWithString:@"https://timgsa.baidu.com/timg?image&quality=80&size=b10000_10000&sec=1498103182&di=eb660bada9ced24acbc9153464aaedcd&src=http://www.bz55.com/uploads/allimg/141120/139-141120151946.jpg"]
                      ,[NSURL URLWithString:@"https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1498139264538&di=6e5cedbfecc6a0e4bce2fc0005f56785&imgtype=0&src=http%3A%2F%2Fimg3.a0bi.com%2Fupload%2Fttq%2F20140719%2F1405761279700.gif"]
                      ];
    
// Create an array to store ZYQPhoto objects
NSMutableArray *photos = [NSMutableArray new];

for (NSURL *url in photosURL) {
	ZYQPhoto *photo = [ZYQPhoto photoWithURL:url];
	[photos addObject:photo];
}
	
// Or use this constructor to receive an NSArray of ZYQPhoto objects from your NSURL objects
NSArray *photos = [ZYQPhoto photosWithURLs:photosURL];
````

There are two main ways to presente the photoBrowser, with a fade on screen or with a zooming effect from an existing view.

Using a simple fade transition:

``` objective-c    
ZYQPhotoBrowser *browser = [[ZYQPhotoBrowser alloc] initWithPhotos:photos];
``` 

Zooming effect from a view:

``` objective-c    
ZYQPhotoBrowser *browser = [[ZYQPhotoBrowser alloc] initWithPhotos:photos animatedFromView:sender];
```

When using this animation you can set the `scaleImage` property, in case the image from the view is not the same as the one that will be shown on the browser, so it will dynamically scale it:

``` objective-c    
browser.scaleImage = buttonSender.currentImage;
```

Presenting using a modal view controller:

``` objective-c
[self presentViewController:browser animated:YES completion:nil];
```

### Customization

##### Toolbar

You can customize the toolbar. There are three boolean properties you can set: displayActionButton (default is YES), displayArrowButton (default is YES) and displayCounterLabel (default is NO). If you dont want the toolbar at all, you can set displayToolbar = NO.

Toolbar setup example:
``` objective-c     
browser.displayActionButton = NO;
browser.displayArrowButton = YES;
browser.displayCounterLabel = YES;
```

It is possible to use your own image on the toolbar arrows:
``` objective-c     
browser.leftArrowImage = [UIImage imageNamed:@"PhotoBrowser_customArrowLeft.png"];
browser.rightArrowImage = [UIImage imageNamed:@"PhotoBrowser_customArrowRight.png"];
browser.leftArrowSelectedImage = [UIImage imageNamed:@"PhotoBrowser_customArrowLeftSelected.png"];
browser.rightArrowSelectedImage = [UIImage imageNamed:@"PhotoBrowser_customArrowRightSelected.png"];
```

If you want to use custom actions, set the actionButtonTitles array with the titles for the actionSheet. Then, implement the photoBrowser:didDismissActionSheetWithButtonIndex:photoIndex: method, from the ZYQPhotoBrowser delegate

``` objective-c    
browser.actionButtonTitles = @[@"Option 1", @"Option 2", @"Option 3", @"Option 4"];
```

#### Others

Others customizations you can make are: use white background color, don't display the done button and change the done button background image:
``` objective-c    
browser.useWhiteBackgroundColor = YES;
browser.displayDoneButton = NO;
browser.doneButtonImage = [UIImage imageNamed:@"PhotoBrowser_customDoneButton.png"];
```

If you want to keep the interface shown when the user is scrolling :
``` objective-c
browser.autoHideInterface = NO;
```

You can use a smooth zoom animation when presenting and dismissing a photo:
``` objective-c
browser.useZoomAnimation = YES;
```

If the presenting view controller doesn't have a status bar, in some cases you can force it to be hidden:
``` objective-c
browser.forceHideStatusBar = YES;
```

It's possible to disable the vertical dismiss swipe gesture:
``` objective-c
browser.disableVerticalSwipe = YES;
```

Dismiss the photo browser with a touch (instead of showing/hiding controls):
``` objective-c
browser.dismissOnTouch = YES;
```
You can use custom backgroud
``` objective-c
browser.customBackgroud=[[BackgroudBlurView alloc] init];
```

### Photo Captions

Photo captions can be displayed simply by setting the `caption` property on specific photos:
``` objective-c
ZYQPhoto *photo = [ZYQPhoto photoWithFilePath:[[NSBundle mainBundle] pathForResource:@"photo2l" ofType:@"jpg"]];
photo.caption = @"Campervan";
```

No caption will be displayed if the caption property is not set.

#### Custom Captions

By default, the caption is a simple black transparent view with a label displaying the photo's caption in white. If you want to implement your own caption view, follow these steps:

1. Optionally use a subclass of `ZYQPhoto` for your photos so you can store more data than a simple caption string.
2. Subclass `ZYQCaptionView` and override `-setupCaption` and `-sizeThatFits:` (and any other UIView methods you see fit) to layout your own view and set it's size. More information on this can be found in `ZYQCaptionView.h`
3. Implement the `-photoBrowser:captionViewForPhotoAtIndex:` ZYQPhotoBrowser delegate method (shown below).

Example delegate method for custom caption view:
``` objective-c
- (ZYQCaptionView *)photoBrowser:(ZYQPhotoBrowser *)photoBrowser captionViewForPhotoAtIndex:(NSUInteger)index {
	ZYQPhoto *photo = [self.photos objectAtIndex:index];
	MyCaptionViewSubclass *captionView = [[MyCaptionViewSubclass alloc] initWithPhoto:photo];
	return captionView;
}
```
#### Custom  Opensource libraries
You need to implement protocol
``` objective-c
//ZYQPhotoBrowser's
@protocol ZYQPhotoBrowserCustomProtocol <NSObject>

@required
- (void)custom_animateView:(UIView *)view toFrame:(CGRect)frame completion:(void (^)(void))completion;

@end

//ZYQPhoto's
@protocol ZYQPhotoCustomLoadProtocol <NSObject>

@required
-(void)loadImageWithURL:(NSURL*)url updateProgressBlock:(void (^)(CGFloat progress))updateProgressBlock completedBlock:(void (^)(UIImage *image,NSError *error))completedBlock;
-(id)loadImageWithFile:(NSString *)path;

@end

//ZYQZoomingScrollView's
@class ZYQPhotoBrowser;
@protocol ZYQZoomingScrollViewCustomProtocol <NSObject>

@required
-(id)getCustomProgressViewWithBrowser:(ZYQPhotoBrowser*)browser;

@end

//Your ProgressView's
@protocol ZYQProgressViewProtocol <NSObject>

@required
@property(nonatomic,assign)CGFloat zyq_progress;

@end

```
Example

add `pod 'YYWebImage'` to your Podfile.

``` objective-c

@interface ZYQPhoto (Custom)<ZYQPhotoCustomLoadProtocol>

@end

@implementation ZYQPhoto (Custom)

-(void)loadImageWithURL:(NSURL*)url updateProgressBlock:(void (^)(CGFloat progress))updateProgressBlock completedBlock:(void (^)(UIImage *image,NSError *error))completedBlock{
    [[YYWebImageManager sharedManager] requestImageWithURL:url options:YYWebImageOptionAllowBackgroundTask progress:^(NSInteger receivedSize, NSInteger expectedSize) {
        if (updateProgressBlock) {
            updateProgressBlock(receivedSize/expectedSize*1.0);
        }
    } transform:nil completion:^(UIImage * _Nullable image, NSURL * _Nonnull url, YYWebImageFromType from, YYWebImageStage stage, NSError * _Nullable error) {
        if (completedBlock) {
            completedBlock(image,error);
        }
    }];
}

-(id)loadImageWithFile:(NSString *)path{
    return [YYImage imageWithContentsOfFile:path];
}

@end

@interface ZYQZoomingScrollView (Custom)<ZYQZoomingScrollViewCustomProtocol>

@end

@implementation ZYQZoomingScrollView (Custom)

-(id)getCustomProgressViewWithBrowser:(ZYQPhotoBrowser *)browser{
    CGRect screenBound = [[UIScreen mainScreen] bounds];
    CGFloat screenWidth = screenBound.size.width;
    CGFloat screenHeight = screenBound.size.height;

    DACircularProgressView *tmpProgressView = [[DACircularProgressView alloc] initWithFrame:CGRectMake((screenWidth-35.)/2., (screenHeight-35.)/2, 35.0f, 35.0f)];
    [tmpProgressView setProgress:0.0f];
    tmpProgressView.tag = 101;
    tmpProgressView.thicknessRatio = 0.1;
    tmpProgressView.roundedCorners = NO;
    tmpProgressView.trackTintColor    = browser.trackTintColor    ? browser.trackTintColor    : [UIColor colorWithWhite:0.2 alpha:1];
    tmpProgressView.progressTintColor = browser.progressTintColor ? browser.progressTintColor : [UIColor colorWithWhite:1.0 alpha:1];
    
    _progressView=tmpProgressView;
    
    return tmpProgressView;
}

@end

@interface DACircularProgressView (Custom)<ZYQProgressViewProtocol>

@end

@implementation DACircularProgressView (Custom)

-(void)setZyq_progress:(CGFloat)zyq_progress{
    [self setProgress:zyq_progress animated:YES];
}

-(CGFloat)zyq_progress{
    return [self progress];
}

@end
```


## Adding to your project

### Using CocoaPods

Just add `pod 'ZYQPhotoBrowser'` to your Podfile.

If you want to use custom libraries

Just add  `pod 'ZYQPhotoBrowser-NoDependency'` to your Podfile.

### Including Source Directly Into Your Project

#### ZYQPhotoBrowser Opensource libraries used

- [SDWebImage](https://github.com/rs/SDWebImage)
- [DACircularProgress](https://github.com/danielamitay/DACircularProgress)

## Licence

This project uses MIT License.
