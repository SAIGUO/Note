## 2- GOAL OF THE PIPELINE
- 1st objective : Fill up the world（最基础的目标）
- 2nd objective : Consistent with terrain topology（适应地形变化）
- 3rd objective : Automated（保证不同的机器可以在夜晚不间断生成世界不同部分，保证用户每天早上更新世界数据）
- 4th objective : Deterministic（保证给定相同输入的情况下，不同构建机器可以得到相同结果）
- 5th objective : User friendly（为用户提供编辑工具，方便用户管理、使用）

## 3- THE TOOLS
- Freshwater
- Fences & Powerlines
- Cliffs
- Biomes
- Fog
- Worldmap

## 4- USERS POINT OF VIEW

### Terrain
用户使用Dunia编辑工具改变地形

### Freshwater
将curves and spline （曲线与样条）作为freshwater工具的输入，我们可以直接通过样条spline的宽度控制河流。

### Cliffs
生成悬崖网格及地形纹理。

该工具简单的基于地形陡度，用户只需要在期望的地形上运行生成。

### Biomes
用户可以使用biomes工具生成想要的生物群落。

### Points of Interest
#### Adding a road
添加人类对环境的影响。如用road spline添加道路。

在烘烤道路工具之后，用户可以刷新biomes工具以清除道路上的植被。

#### Setup a location
为了自定义位置，用户可以通过绘制像草一样的子生物群落并添加丛林来覆盖主要生物群落。 这将清除铺设建筑物等的方式。

### Fences
fence工具的输入依然是spline，用户只需在样条参数上设置他想要生成的栅栏类型并运行生成。

### Power lines

## 5- THE PIPELINE
使用可用的输入输出实现houdini与dunia之间的数据传输。
![fail](https://github.com/SAIGUO/Note/blob/master/image/data%20exchange.png)

### Input
#### From Dunia to Houdini
- 一些输入通过python脚本从Dunia发送到Houdini

  - World information
  - File Paths
  - Terrain sectors
  - Splines and Shapes
 
- 其他输入只是在磁盘上提取，可以使用Dunia提供的文件路径从Houdini读取

  - Height maps (.raw)
  - Biome painter (.png)
  - 2D terrain masks (.png)
  - Houdini Geometry (.geo or .bgeo)
 
**Terrain是主要输入**
