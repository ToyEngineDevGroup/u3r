# 统一3D绘制接口（Unified 3D Render API）

## 介绍

暂时不要按这个写，目前还需要更新。

该接口使用C++编写，提供渲染图形的C++类和函数接口，目的是使引擎同时支持OpenGL和DirectX，甚至Vulkan。这里只给出模板，不给出具体实现。

因为数据的具体存储方式可能与实现有关，故不列出私有变量列表。

## 基本内容

### Class U3R::Instance

用于环境的搭建和销毁，以及管理和绘制可绘制对象、着色器等。以及设置全局属性：如光照、相机等全局Uniform变量。

无公共属性。

公共函数：
```cpp
void initialize(); // 初始化
void finalize(); // 终止
void set_camera(U3R::Camera * cam); // 相机
void set_light_source(U3R::Light * light); // 光源
void enable_light(bool enable); // 允许光照
void render(U3R::RenderObject * obj); // 绘制
```

### Class U3R::Camera

```cpp
void look_at(U3R::Vector p, U3R::Vector t);
```

### Class U3R::Light

```cpp
void set_position(U3R::Vector p);
void set_light(U3R::Color ambient, U3R::Color diffuse, U3R::Number shininess);
```

### Class U3R::RenderObject

表示一个可绘制的多边形对象。

无公共属性。

公共常量：
```cpp
ATTRIB_VERTEX = 1;
ATTRIB_NORMAL = 2;
ATTRIB_TEX_COORD = 4;
ATTRIB_TEXTURE = 8;
ATTRIB_NORMAL_MAP = 16;
ATTRIB_TRANSFORM = 32;
```

公共函数：

```cpp
RenderObject();
void resize(unsigned int size); // 设置顶点个数
void set_attrib(unsigned int attr); // 设置有哪些参数
void set_vectices(const U3R::Number * data, unsigned int count); // 设置顶点坐标
void set_normals(const U3R::Number * data, unsigned int count); // 设置顶点法向
void set_tex_coords(const U3R::Number * data, unsigned int count); // 设置顶点纹理坐标
void set_shader(U3R::Shader * shader); // 设置着色器
void set_texture(U3R::Texture * texture); // 设置纹理贴图
void set_normal_map(U3R::Texture * texture); // 设置法线贴图
void set_faces(const U3R::Number * face_data); // 设置面数据
void set_transform(U3R::Transform * transform); // 设置变换
~RenderObject();
```

如果认为以上限制太多了,可以使用这种方法:
```cpp
void add_input(unsigned int location unsigned int count);
void set_input(unsigned int location, const U3R::Number * data);
void set_uniform_vector(unsigned int location, U3R::Vector v);
void set_uniform_transform(unsigned int location, U3R::Transform t);
void set_uniform_sampler(unsigned int sampler, U3R::Texture tex);
```
### Class U3R::Shader

无公共属性。

友元：
```cpp
friend class Instance;
```

公共函数：
```cpp
Shader();
void compile_from_file(const char * filename); // 读取着色器文件并编译
void compile_from_string(const char * str); // 从字符串中读取并编译（可不选）
void load_from_file(const char * filename); // 读取编译好的着色器文件（可不选）
void load_from_string(const char * str); // 从字符串中读取（可不选）
~Shader();
```

### Class U3R::Texture

无公共属性。

友元：
```cpp
friend class Instance;
```

公共枚举类型：

```cpp
REPEAT_REPEAT = 0;
REPEAT_MIRRORED_REPEAT = 1;
REPEAT_CLAMP_TO_EDGE = 2;
REPEAT_CLAMP_TO_BORDER = 3;

FILTER_NEAREST = 0;
FILTER_LINEAR = 1;

COLOR_GRAY = 0;
COLOR_RGB = 1;
COLOR_RGBA = 2;

DATA_TYPE_U8 = 0;
DATA_TYPE_U16 = 1;
```
公共函数：

```cpp
Texture();
void set_repeat(unsigned int mode); // 设置重复类型
void set_filter(unsigned int filter); // 设置插值方式
void set_data(unsigned int width, unsigned int height, unsigned int color_mode, unsigned int data_type); // 写入图像数据
~Texture();
```

### 其他

```cpp
typedef float Number;
```

### 可选

Class U3R::Window:

用于存储窗口指针。

Class U3R::Input:
```cpp
Input();
bool get_key_down(unsigned int key_code);
int get_mouse_x();
int get_mouse_y();
float get_linear_input(const char * input_desc_str); // 获取线性的输入，如摇杆的竖直坐标，通过get_linear_input("joy-v")得到
~Input();
```

Class U3R::Image:
```cpp
Image();
void load_from_file(const char * filename);
~Image();
```

Class U3R::Model:
```cpp
Model();
void load_from_file(const char * filename);
~Model();
```

