# LYTFTP
##注意：
在iOS 手机上通过FTP 下载和上传文件    
在电脑上开启FTP server 功能 需要关闭防火墙。以免产生问题。   
OS X系统上开启FTP server 功能使用：   
sudo -s launchctl load -w /System/Library/LaunchDaemons/ftp.plist  
OS X系统上关闭FTP server 功能使用：    
sudo -s launchctl unload -w /System/Library/LaunchDaemons/ftp.plist   
测试FTP server是否正常：    
在终端命令行使用命令,输入登录mac的电脑账号和密码。也可以自己在<用户和群组>创建FTP专用的账号。   
```
>MacBook-Pro:~ xxx$ ftp 127.0.0.1  
>Connected to 127.0.0.1 
>220 127.0.0.1 FTP server (tnftpd 20100324+GSSAPI) ready.  
>Name (127.0.0.1:xxx):   
>password:
```
##导入
    只需要导入  LxFTPRequest这个类倒入 项目
##支持

Both support iOS and Mac OS X platforms.
Minimum support iOS version: iOS 5.0
Minimum support OS X version: Mac OS X 10.7


###获取资源列表:
```
LxFTPRequest * request = [LxFTPRequest resourceListRequest];
request.serverURL = [[NSURL URLWithString:FTP_SCHEME_HOST]URLByAppendingPathComponent:SUB_DIRECTORY];
request.username = USERNAME;
request.password = PASSWORD;
request.progressAction = ^(NSInteger totalSize, NSInteger finishedSize, CGFloat finishedPercent) {

NSLog(@"totalSize = %ld, finishedSize = %ld, finishedPercent = %f", totalSize, finishedSize, finishedPercent); 
};
request.successAction = ^(Class resultClass, id result) {

NSArray * resultArray = (NSArray *)result;
NSLog(@"resultArray = %@", resultArray);  
};
request.failAction = ^(CFStreamErrorDomain domain, NSInteger error, NSString *errorMessage) {

NSLog(@"domain = %ld, error = %ld", domain, error);
};
[request start];
```

###下载资源:
```
/**
To implement breakpoint continuingly, you only need to guarantee
the file downloaded part has not been modified in any way, 
the ftp server support breakpoint continuingly
and the file on server not change. 
The download will continue from the last time progress.
If you want to download resource from begin, you should delete the local downloaded part.
[[NSFileManager defaultManager]removeItemAtPath:LOCAL_FILE_PATH error:&error];
*/
LxFTPRequest * request = [LxFTPRequest downloadRequest];
request.serverURL = [NSURL URLWithString:FTP_RESOURCE_ADDRESS];
request.localFileURL = [NSURL fileURLWithPath:LOCAL_FILE_PATH];
request.username = USERNAME;
request.password = PASSWORD;
request.progressAction = ^(NSInteger totalSize, NSInteger finishedSize, CGFloat finishedPercent) {

NSLog(@"totalSize = %ld, finishedSize = %ld, finishedPercent = %f", totalSize, finishedSize, finishedPercent); 
};
request.successAction = ^(Class resultClass, id result) {

NSLog(@"resultClass = %@, result = %@", resultClass, result);  
};
request.failAction = ^(CFStreamErrorDomain domain, NSInteger error, NSString *errorMessage) {

NSLog(@"domain = %ld, error = %ld", domain, error);
};
[request start];
```

###上传资源:
```
LxFTPRequest * request = [LxFTPRequest uploadRequest];
request.serverURL = [NSURL URLWithString:FTP_SCHEME_HOST]URLByAppendingPathComponent:FILE_PATH];  
request.localFileURL = [NSURL fileURLWithPath:LOCAL_FILE_SAVE_PATH];
request.username = USERNAME;
request.password = PASSWORD;         
request.progressAction = ^(NSInteger totalSize, NSInteger finishedSize, CGFloat finishedPercent) {

NSLog(@"totalSize = %ld, finishedSize = %ld, finishedPercent = %f", totalSize, finishedSize, finishedPercent); 
};
request.successAction = ^(Class resultClass, id result) {

NSLog(@"resultClass = %@, result = %@", resultClass, result);
};
request.failAction = ^(CFStreamErrorDomain domain, NSInteger error, NSString *errorMessage) {

NSLog(@"domain = %ld, error = %ld", domain, error);
};
[request start];
```

###Create file or directory on ftp server:
```
LxFTPRequest * request = [LxFTPRequest createResourceRequest];
request.serverURL = [NSURL URLWithString:FTP_RESOURCE_PATH];    // directory path should be end up with '/'
request.username = USERNAME;
request.password = PASSWORD;
request.successAction = ^(Class resultClass, id result) {

NSLog(@"resultClass = %@, result = %@", resultClass, result);
};
request.failAction = ^(CFStreamErrorDomain domain, NSInteger error, NSString *errorMessage) {

NSLog(@"domain = %ld, error = %ld", domain, error);
};
[request start];
```

###Delete file or directory on ftp server:

```
/**
The directory which is not empty CANNOT BE DELETED !!!
*/

LxFTPRequest * request = [LxFTPRequest destoryResourceRequest];
request.serverURL = [NSURL URLWithString:FTP_RESOURCE_PATH];
request.username = USERNAME;
request.password = PASSWORD;
request.successAction = ^(Class resultClass, id result) {

NSLog(@"resultClass = %@, result = %@", resultClass, result);
};
request.failAction = ^(CFStreamErrorDomain domain, NSInteger error, NSString *errorMessage) {

NSLog(@"domain = %ld, error = %ld", domain, error);
};
[request start];
```

#Be careful            
Demo must config FTP address, username, password correctly.

