title: IOS-UI-Tips(-)之UITabelviewCell-Height
date: 2015-08-18 11:36:07
type: "tags"
tags: UI

---
## UITableView中cell高度计算问题之
如何解决类似于微博cell高度问题

### 首先我们先确定一下两个函数问题

	- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath

	- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath

根据我们一开始学习IOS时候的经验，height肯定是在UITableViewCell前面运行的。
实际上height虽说是在cell前面运行的，但是，cell运行完了以后，会再一次调用height,这个height才是cell的最终高度。这也就是说在整个tableview的刷新过程中每一个cell对应的height函数会调用2次,一前一后,这也是今天如何解决不规则cell高度的关键。

### 我们拿到数据源 DataArray 执行 [self.tableView reloadData]会执行

	- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath

这个会遍历数组中的所有object 这个时候我们就可以在这里计算高度了(label、button、imageview)等,然后保存在mode中
等循环结束以后会执行

	- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath

最后再调用

	- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath

现实在UITableView上。有些同学会觉得，运行了2次是不是很麻烦,首先呢 ，我想说的是，你知道这个调用顺序以后 可以优化时间 比如第一次计算高度的时候 就可以保存在模型中。后面2次都可以直接拿过来用。
其次,对于文字高度比较好计算，但是对于单张图片的高度 ，这个就有点头疼了。曾经我问过一个朋友，他说服务器会直接反悔image的size 好吧 ，这个有这些数据的朋友们，当我没说。
其次呢，没有怎么办呢，这个就是2次运行height函数的好处了。

### 对于第一次函数 我们可以下载图片的缩略图以便在后面的计算高度中可以直接使用