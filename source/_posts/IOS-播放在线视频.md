title: IOS-播放在线视频
date: 2015-08-24 09:13:41
tags: IOS

---
### 第一种：
//UIWebView 加在网络视频 在线播放  有声音

UIWebView *myWeb = [[UIWebView alloc] initWithFrame:self.view.bounds];

NSURL *url = [NSURLURLWithString:@"
http://devimages.apple.com/iphone/samples/bipbop/gear1/prog_index.m3u8"];
http://devimages.apple.com/iphone/samples/bipbop/gear1/prog_index.m3u8//这里也可以是 mp4

NSURLRequest *request = [NSURLRequest requestWithURL:url];

[myWeb setDelegate:self];

[myWeb loadRequest:request];

[self.view addSubview:myWeb];
### 第二种：

//AVPlayerLayer 播放在线视频  无声音

NSURL *sourceMovieURL = [NSURL URLWithString:@"http://clips.vorwaerts-gmbh.de/big_buck_bunny.mp4"];

AVAsset *movieAsset    = [AVURLAsset URLAssetWithURL:sourceMovieURLoptions:nil];

AVPlayerItem *playerItem = [AVPlayerItem playerItemWithAsset:movieAsset];

AVPlayer *player = [AVPlayer playerWithPlayerItem:playerItem];

AVPlayerLayer *playerLayer = [AVPlayerLayer playerLayerWithPlayer:player];

playerLayer.frame = self.view.layer.bounds;

playerLayer.videoGravity = AVLayerVideoGravityResizeAspect;

[self.view.layer addSublayer:playerLayer];

转载请标明出处，黄志勇的个人博客！

[player play];
### 第三种：

//MPMoviePlayerController 播放在线视频  无声音

NSURL *videoURL = [NSURLURLWithString:@"https://s3.amazonaws.com/adplayer/colgate.mp4"];

MPMoviePlayerViewcontroller *moviePlayerController = [[MPMoviePlayerViewcontroller alloc] initWithContentURL:videoURL];

moviePlayerController.moviePlayer.movieSourceType = MPMovieSourceTypeFile;

moviePlayerController.moviePlayer.shouldAutoplay = YES;

//moviePlayerController.moviePlayer.controlStyle = MPMovieControlStyleNone;

[moviePlayerController.moviePlayer prepareToPlay];

[moviePlayerController.moviePlayer play];

[[NSnotificationCenter defaultCenter]

addObserver: self

selector: @selector(myMovieFinishedCallback:)

name: MPMoviePlayerPlaybackDidFinishnotification

object: moviePlayerController];

[self presentMoviePlayerViewcontrollerAnimated:moviePlayerController];
### 题记
简单的视频在线播放,可以使用第三方框架MWphotoborwser