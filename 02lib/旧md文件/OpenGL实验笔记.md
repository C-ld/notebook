# OpenGL实验笔记

## 实验一、画三角形

### 步骤一、初始化

GLFW的初始化：

```c++
	glfInit();	//初始化GLFW
	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR,3);
	//OpenGL版本为3.3，设置主版本号为3
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR,3);
	//OpenGL版本为3.3，设置次版本号为3
	glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
	//使用核心模式（不向后兼容）
	//glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE); Mac系统需要加上这行
	glfwWindowHint(GLFW_RESIZABLE, FALSE);
	//窗口大小不可变
```

创建窗口：

```C++
	int screen_width = 1080;
	int screen_height = 720;
	auto window = glfwCreateWindow(screen_width, screen_height, "Computer graphics", nullptr, nullptr);
	if(window == nullptr){
        cout << "Fail to Create OpenGL Context" << endl;
        glfwTerminate();
        return -1;
    }
	glfwMakeContextCurrent(window);	//窗口的上下文设置为当前线程的主上下文
```

初始化GLAD：

```C++
	if(!gladLoadGLLoader((GLADLoadproc)glfwGetProcAddress)){
        cout << "Failed to initialize GLAD" << endl;
        return -1;
    }
```

创建视口：

```C++
	glViewport(0, 0, screen_width, screen_height);
	//指定当前视口尺寸，前两个参数为左下角位置，后面是宽和高
```



### 步骤二、数据处理

顶点数据：

```C++
	const float triangle[] = {
        -0.5f, -0.5f, 0f,
        0.5f, -0.5f, 0f,
        0.0f, 0.5f, 0f
    }
```

生成和绑定VAO和VBO，并设置属性指针：

```c++
	GLuint vertex_array_object;	//VAO
	glGenVertexArrays(1, &vertex_array_object);
	glBindVertexArray(vertex_array_object);
	GLuint vertex_buffer_object; //VBO
	glGenBuffers(1, &vertex_buffer_object);
	glBindBuffers(GL_ARRAY_BUFFER, vertex_buffer_object);
	//将顶点数据绑定到当前默认的缓冲中
	glBufferData(GL_ARRAY_BUFFER, sizeof(triangle), triangle, GL_STATIC_DRAW);
	//最后一个参数表示三角形位置不会被改变
```

属性设置：

```C++
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3*sizeof(float), (void*)0);
	//参数含义依次为：顶点着色器的位置值；顶点属性向量长度；顶点的类型；是否希望数据被标准化；步长，连续顶点属性间的间隔；顶点数据的偏移量
	glEnableVertexAttribArray(0);
	//开启0通道（默认是关闭的）
```

解绑VAO和VBO：

```C++
	glBindVertexArray(0);
	glBindBuffer(GL_ARRAY_BUFFER, 0);
```



### 步骤三、着色器着色

顶点和片段着色器源码：

```C++
	const char* vertex_shader_source = 
        "#version 330 core\n"
        "layout (location = 0)in vec3 aPos;\n"
        "void main(){\n"
        "	gl_Position = vec4(aPos, 1.0);\n"
        "}\n\0";
	const char* fragment_shader_source = 
        "#version 330 core\n"
        "out vec4 FragColor;\n"
        "void main(){\n"
        "	FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
        "}\n\0";
```

生成和编译着色器：

```C++
	int vertex_shader = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vertex_shader, 1, &vertex_shader_source, NULL);
	glCompileShader(vertex_shader);
	int success;
	char info_log[512];
	//检查着色器是否成功编译
	glGetShaderiv(vertex_shader, GL_COMPILE_STATUS, &success);
	if(!success){
        glGetShaderInfoLog(vertex_shader, 512, NULL, info_log);
        cout << "ERROR: Compile failed " << info_log << endl;
    }

	int fragment_shader = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fragment_shader, 1, &fragment_shader_source, NULL);
	glCompileShader(fragment_shader);
	//检查着色器是否成功编译
	glGetShaderiv(fragment_shader, GL_COMPILE_STATUS, &success);
	if(!success){
        glGetShaderInfoLog(fragment_shader, 512, NULL, info_log);
        cout << "ERROR: Compile failed " << info_log << endl;
    }
```

链接着色器程序：

```C++
	int shader_program = glCreateProgram();
	glAttachShader(shader_program, vertex_shader);
	glAttachShader(shader_program, fragment_shader);
	glLinkProgram(shader_program);
	//检查着色器是否成功链接
	glGetProgramiv(shader_program, GL_LINK_STATUS, &success);
	if(!success){
    	glGetProgramInfoLog(shader_program, 512, NULL, info_log);
        cout << "ERROR: Link failed " << info_log << endl;
    }
```

删除着色器：

```C++
	glDeleteShader(vertex_shader);
	glDeleteShader(fragment_shader);
```



### 步骤四、渲染

渲染：

```C++
	while(!glfwWindowShouldClose(window)){
        //清空颜色缓冲
        glClearColor(0.0f, 0.0f, 0.0f, 1.0f);	//用黑色的背景色清空颜色缓冲
        glClear(GL_COLOR_BUFFER_BIT);
        
        //使用着色器程序
        glUseProgram(shader_program);
        
        //绘制三角形
        glBindVertexArray(vertex_array_object);//绑定VAO
        glDrawArrays(GL_TRIANGLES, 0, 3);	//绘制三角形
        glBindVertexArray(0);				//解绑VAO
        
        //交换缓冲并且检查是否有触发事件
        glfwSwapBuffers(window);
        glfwPollEvents();
    }
```

善后工作：

```C++
	glDeleteVertexArrays(1, &vertex_array_object);
	glDeleteBuffers(1, &vertex_buffer_object);

	glfwTerminate();//清理所有资源并退出程序
	return 0;
```



## 实验二、

