# 房间的3D重建+3D装修 相关技术方案

## 居住空间模型的重建
### 多幅图像的的房间解构恢复和物体识别
通过被动光源的房屋解构恢复的问题及应用是我们关注的问题，👇这个篇文章讲了一个完整的框架
Understanding the 3D Layout of a Cluttered Room From Multiple Images
http://svl.stanford.edu/assets/papers/bao_wacv2014.pdf

大致分成以下几个步骤：
1. strcuture from motion

    通过

Occupancy Networks: Learning 3D Reconstruction in Function Space
https://github.com/autonomousvision/occupancy_networks


基于 RGBD 的方法
HouseScan: Building-scale interior 3D reconstruction with KinectFusion
https://www.doc.ic.ac.uk/teaching/distinguished-projects/2014/n.hambuechen.pdf


## 全景图的渲染
VR photography
https://en.wikipedia.org/wiki/VR_photography

https://www.panoramic-photo-guide.com/virtual-tour-360-photography/how-to-create-virtual-tour-360-summary.html

https://medium.com/game-development-stuff/how-to-make-a-360%C2%BA-image-viewer-with-unity3d-b1aa9f99cabb

https://onix-systems.com/blog/how-to-use-360-equirectangular-panoramas-for-greater-realism-in-games

# 从cubemap生成equirectangular图像
一个网站可以离线生成图像
https://www.360toolkit.co/convert-cubemap-to-spherical-equirectangular

TODO list
1. 找一个现成的cubemap图
2. 将cubemap映射成equirectanglar图

    有两种做法，一个是
    * 从 cube 坐标系 到 equirectanglar 图的坐标的映射关系
    * 将 cube 图中的每一点映射到equirectanglar下的坐标，将像素值填充到该位置上。

    这种做法的问题是cube的坐标往往不能正好映射到整数的贴图坐标上，它的值应当是分散到它映射后的几个临近的像素格上，所以最佳的操作方法是，另外一个
    * 或者从 equirectanglar 图中的坐标，映射到cube中的坐标
    * 将 equirectanglar 映射到cube中的坐标，取临近的点的混合

    work list
    * 函数根据equrectanglar的resolution:(h,w) 生成 (theta, phi)
    * 函数将(theta, phi) => (x,y,z)
    * (x,y,z) => (faceIndx, x, y)
3. 根据场景模型生成cubemap


## texture from cubemap
https://www.cnblogs.com/alps/p/7112872.html

cubemap direction
https://www.cnblogs.com/dragon2012/p/6100802.html

### python library for convert
https://github.com/sunset1995/py360convert

## OpenGL render image
https://medium.com/@shintaroshiba/saving-3d-rendering-images-without-displays-on-python-opengl-f534a4638a0d


# 渲染引擎的选择

blender 2.80EEVEE下实时渲染室内效果图
https://tutorial.blendercn.org/?p=1638

blender 手册
https://docs.blender.org/manual/zh-hans/latest/


blender 场景制作视频
https://www.bilibili.com/video/av27992129/

blender 的脚本参考
https://github.com/njanakiev/blender-scripting
https://www.cnblogs.com/qmdlinux/p/5086831.html 渲染器设置

cycles 渲染效果图
https://www.cycles-renderer.org/

cycles 渲染教程
http://www.aigei.com/item/00_01_intro_2.html


室内模型
https://www.blendercn.org/4794.html

eevee 

