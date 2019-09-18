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

# blender 的 场景组成

blender 数据格式
https://www.barneyparker.com/blender-json-import-export-plugin/
blender 带材质的模型
https://blog.csdn.net/lyq19870515/article/details/80498667
export three.js
https://blog.csdn.net/qq_30100043/article/details/79606355

threejs schema
https://github.com/mrdoob/three.js/wiki
https://github.com/mrdoob/three.js/wiki/JSON-Model-format-3

另外一个web3d的框架
https://github.com/BabylonJS/BlenderExporter

## 数据标准
https://www.khronos.org/gltf/
https://github.com/KhronosGroup/glTF/blob/master/README.md

gltf 的 模型库
https://sketchfab.com/3d-models?features=downloadable&sort_by=-likeCount


资源的定义:meshes, materials, textures, images
场景树的定义是:nodes

### blender 的 gltf 导入导出库的源代码
https://github.com/KhronosGroup/glTF-Blender-IO


### scene 和 node的关系

nodes 具有嵌套的树状结构，它只是逻辑的数据，表示各空间的嵌套关系。
它的叶子节点会具体引用某些资源。

nodes 用一个 list 表示一个树。
node 通过children 指向自己的孩子节点。
每个节点的id就是list中的index。
node 通过 rotation & translation & scale 定义子空间在父空间的位置和尺寸。

node 引用的某个mesh, 表示 这个 mesh 对象 放入到 node 这个空间中。

### buffer & bufferView & accessors
buffer 包含了一组二进制数据

bufferView 是buffer中的一个片段
??? bufferView 中的target 事实上指定的是数据对象的存储模型： 顶点(Vertex:34962)还是顶点索引(VertexIndex:34963)
buffer 的数据被分割成许多部分，每一部分被定义在bufferView中

accessors 是对bufferView的进一步分割
componentType 表示bufferView中的原子数据的类型，从二进制数据转化为实际的逻辑数据类型: UINT8, UINT16, FLOAT 等等
type 表示基于逻辑数据组成二阶的数据对象类型: SCALAR, VEC3
count 表示有几个type类型的数据对象。
max, min 表示数据的边界，常常用于boundingbox的快速确定。
sparse 保存的是 变化数据的index(=> refer to bufferView) 和 value(=> refer to bufferView)

一切数据全都保存在buffer 和 bufferView中
accssors 记录数据的解析过程和协议

### mesh
mesh 又称 triangle mesh, 三角网格,
### mesh.primitive 
mesh.primitive 是组成mesh的更小的单元，构建mesh的基础块。
mesh.primitive 的 attributes 包含 "POSITION" 是顶点的accessors
mesh.primitive 的 attributes 包含 "NORMAL" 是顶点的法线的accessors

mesh.primitive.mode 默认是 TRIANGLES, index的组成顺序

mesh.primitive 的 attributes 包含 "TEXCOORD_0" ... "TEXCOORD_I" 定义多组 文理坐标。这也说明有多张纹理在使用。

### material & texture & sampler & image

material instance的property定义的是物体光线计算的参数，如在metallic-roughness模型下的计算参数

```json
"pbrMetallicRoughness": {
        "baseColorFactor": [ 1.000, 0.766, 0.336, 1.0 ],
        "metallicFactor": 0.5,
        "roughnessFactor": 0.1
      }
```

texture => smapler
        => image[source]

上述的"baseColorFactor" => "baseColorTexture" 可以用texture进行替换这样颜色测采样会更丰富。
```json
"baseColorTexture" : {
  "index" : 0, // refer to textures[i]
  "texCoord": 2 // refer to mesh.primitive.attributes.TEXCOORD_I
}
```
说明不同材质的使用固定的 TEXCOORD_I 这样增加文理的通用性

关于material 有许多计算模型，这里可能需要根据，实际情况逐一了解各种计算模型吧。

