### 属性创建
#### 常用类型
用已有类型变量初始化该类型数组：
```
matrix3 g = {1,1,1,1,1,1,1,1,1};
matrix3 p[] = array(g); //需要加array
```
字符串初始化时候可以用单、双引号。
#### 属性创建
houdini常用属性可以用“@”+属性名创建，不需要加类型。
属性赋值的大括号{}里面不能有变量，如
`@N = {1,0,0}` 是对的，但`@N = {1,@P.y,0}` 不对，要用一个function：set函数，`@N = set(1,@P,0)` 。
houdini创建常见属性时，如果@前面没有类型，则默认是float。
简写属性数组的创建，中括号写在@之前，如f[]@len = {1.0,0.0,0.0}，表示一个叫len的float类型数组。
非简写形式：float @len[]。
> 注意：非简写形式创建数组属性不能直接赋初值，不可以有算式，也不可以用函数 。

**简写与非简写用途区别**
当需要在两个结点中创建了相同名字的属性时，第二个结点再次创建时，如果是简写形式，将会覆盖掉第一次创建的值，而非简写形式将不会覆盖原属性值。

### 属性读取
#### @读取方法
`float y = @P.x; // (或@P.r或@P[0])`
对于其他输入端属性读取：
`float r = @opinput1_Cd.y; // op：结点，input：输入端，1：输入端序号，Cd：属性`
如果是houdini不认识的属性则@前面需要加属性类型：
`float r = v@opinput1_foo.y; // v代表向量类型`
除非是现在创建现在就读取的属性，@前不需要加类型。

#### 函数读取
如point、prim、vertex、detail，具体参考VEX Function。
`vector color = point(1, 'Cd', @ptnum); //ptnum是节点默认属性，可以用attribute VOP观察默认属性`

### 获取全局变量
全局变量：$开头的变量
 
### 读取节点参数
用ch*()读取各种类型的参数，ch表示通道，\*表示类型。
#### chramp()的使用
`@P.y = chramp('ramp', @P.x); //P.x是自变量， P.y是因变量，ramp需要自变量的范围在0-1之间，超出范围会循环`
fit()函数归一化。
默认chramp是浮点渐变，还有矢量ramp，也叫颜色ramp。
ch函数可以有第二个参数，表示时间。

### 点、线、面互访
线性顶点序号借助spreadsheet的顶点属性map offset观察。
> 可以借助display options - markers - vertices - markers和numbers观察顶点

![fc7457f8cc82578379c2eb8795d46e9c.png](en-resource://database/1625:1)

### Intrinsic属性
普通属性存储在几何体上，占用空间，intrinsic属性仅在需要的时候计算出来。只有primitive及detail有intrinsic属性。
