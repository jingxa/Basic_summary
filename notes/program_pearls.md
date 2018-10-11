

# 字符串

## 1. 单词

统计一篇文章中的单词字数,假设本书为《圣经》，圣经中有29131个不同的单词

- 使用map
- 使用hashtable

```c++

int main(){
	map<string, int> M;
	map<string, int> ::iterator j;
	string t;
	while(cin>>t){
		M[t]++;
	}
	
	for(j = M.begin();j != M.end();j++){
		cout<<j->first <<" "<<j->second<<endl;
	}

	return 0;
}

```

为了减少处理时间，使用散列表来处理

- 哈希拉链法，定义一个链表结构

```c++
typedef  struct node   *nodeptr;

typedef struct node {
	char *word;		// 字符串
	int count;		// 计数
	nodeptr next;   // 下一个节点

}node;



#define NHASH 29989   // 最近总数的质数

#define MULT 31

nodeptr bin[NHASH];   // 将所有单词哈希到这个数组中



unsigned int hash(char *p){		// 哈希函数
	unsigned int h = 0;
	for(;*p;p++){
		h = MULT * h + *p;
	}
	
	return h % NHASH;

}


// 查找单词
void incword(char* s){

	unsigned int h = hash(s);
	nodeptr p;
	for(p = bin[h]; p != NULL; p = p->next){   // 如果单词已经存在，线性查找并且计数
		if(strcmp(s,p->word) == 0)
		{
			(p->count)++;
			return ;
		}
	}
	
	p  = (nodeptr)malloc(sizeof(node));    // 不存在的话就新建一个节点加入到hash表中
	p->count = 1
	p->word = (char*)malloc(strlen(s) + 1);
	strcpy(p->word, s);
	
	p->next = bin[h];
	
	bin[h] = p;
	
	
	


}

```




## 2. 短语

在一个很大的文本中搜索“几个单词组成的短语”

- 在执行搜索之前对文本内容进行预处理，建立一个散列表或者链表。为每个不同的单词建立索引，然后为每个单词的每次出现建立一个链表

```c++

#define MAXN 5000000   // 假设处理最大单词

char c[MAXN], *a[MAXN];

// 使用一种后缀数组存储


while((ch = getchar() != '\0'){
	a[n]

}




```













