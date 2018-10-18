# c++实现的面试题



## 1. String的实现

```c++

class String
{
public:
	String(const char *str = NULL);// 普通构造函数  
	String(const String &other);// 拷贝构造函数  
	~String(void);// 析构函数  
	String & operator = (const String &other);// 赋值函数  
private:
	char *m_data;// 用于保存字符串  

};




//普通构造函数  
String::String(const char *str)
{
	if (str == NULL)
	{
		m_data = new char[1];// 得分点：对空字符串自动申请存放结束标志'\0'的，加分点：对m_data加NULL判断  
		*m_data = '\0';
	}
	else
	{
		int length = strlen(str);
		m_data = new char[length + 1];// 若能加 NULL 判断则更好
		strcpy(m_data, str);
	}
}
 
 
// String的析构函数  
String::~String(void)
{
	delete[] m_data; // 或delete m_data;  
}
 
 
//拷贝构造函数  
String::String(const String &other)// 得分点：输入参数为const型  
{		 
	int length = strlen(other.m_data);
	m_data = new char[length + 1];// 若能加 NULL 判断则更好  
	strcpy(m_data, other.m_data);
}
 
 
//赋值函数  
String & String::operator= (const String &other) // 得分点：输入参数为const型  
{
	if (this == &other)//得分点：检查自赋值  
		return *this; 
	if (m_data)
	    delete[] m_data;//得分点：释放原有的内存资源  
	int length = strlen(other.m_data);
	m_data = new char[length + 1];//加分点：对m_data加NULL判断  
	strcpy(m_data, other.m_data);
	return *this;//得分点：返回本对象的引用    

}
	
		
```



## 2. c函数的实现

#### strcpy

```c++
char* strcpy(char* dst, const char* src){
	assert((dst != NULL && (src != NULL));
	char* tmp = dst;
	
	while((*dst++ = *src++) != '\0');
	return tmp;
}

```

- 考虑重叠

```c++
char* strcpy(char* dst, const char* src){
	assert(dst != NULL && src != NULL);
	char* ret = dst;
	my_memecpy(dst, src, strlen(src)+1);
	return ret;
}

char* my_memecpy(char *dst, char *src , int cnt){
	assert( dst != NULL && src != NULL );
	char *ret = dst;
	if(dst >=src && dst <= src + cnt-1)
	{
		dst = dst + cnt-1;
		src = src + cnt -1;
		while(cnt -- )
			*dst-- = *src--;
	}else{
	while(cnt--)
		*dst++ = *src++;
	}
	return ret;
	
}


```
#### strncpy

```c++
char* strncpy(char *dst, const char *src, size_t n){
	char *ret = dst;
	size_t i=0;
	while(n >0 && *src != '\0')
	{
		*dst++ = *src++;
		n--;
	}
	while(n-->0)
		*dst++ = '\0';
	return ret;
}


```


#### strcat

- 把src所指字符串添加到dest结尾处(覆盖dest结尾处的'\0')。
- src和dest所指内存区域不可以重叠且dest必须有足够的空间来容纳src的字符串。

```c++
char* strcat(char* dst, const char* src){

	char* ret = dst;
	while(*dst++ != '\0');
	while(*src != '\0')
		*dst++ = *src++;
	*dst = '\0';
	return ret;

}
```


#### strncat

```c++
char* strncat (char *dst, const char* src , size_t n){
	char* ret = dst;
	size_t i, j;
	for(j=0;dst[j] != '\0';++j);
	for(i=0;i<n && src[i] != '0';)
	{
		dst[j++] = src[i++]; 
	}
	for(;i<n;i++)dst[j] = '\0';
	return ret;
}

```

#### strlen 


```c++
sizt_t strlen(const char* s)
{
	assert(str != NULL);
    const char *eos = str;

    while( *eos++ ) ; // eos 为'\0'后面一个地址

    return( eos - str - 1 );
	
	
}

```



## 3. 智能指针的实现

原始指针和引用计数都采用指针分别指向堆里面分配的元素和计数。

```c++

template<typename T>
class SmartPointer {
private:
    T* _ptr;				// 原始指针
    size_t* _count;			// 引用计数
public:
    SmartPointer(T* ptr = nullptr) :     // 构造函数
            _ptr(ptr) {
        if (_ptr) {
            _count = new size_t(1);
        } else {
            _count = new size_t(0);
        }
    }

    SmartPointer(const SmartPointer& ptr) {    // 复制拷贝函数
            this->_ptr = ptr._ptr;
            this->_count = ptr._count;
            (*(this->_count))++;
    }

    SmartPointer& operator=(const SmartPointer& ptr) {    // 赋值拷贝函数
        if (this->_ptr == ptr._ptr) {
            return *this;
        }

        if (this->_ptr) {			// 判断是否存在其他智能指针
            (*this->_count)--;
            if (this->_count == 0) {
                delete this->_ptr;
                delete this->_count;
            }
        }

        this->_ptr = ptr._ptr;
        this->_count = ptr._count;
        (*this->_count)++;
        return *this;
    }

    T& operator*() {
        assert(this->_ptr == nullptr);
        return *(this->_ptr);

    }

    T* operator->() {
        assert(this->_ptr == nullptr);
        return this->_ptr;
    }

    ~SmartPointer() {
        (*this->_count)--; 
        if (*this->_count == 0) {
            delete this->_ptr;
            delete this->_count;
        }
    }

    size_t use_count(){
        return *this->_count;
    }
};



```


