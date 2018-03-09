title: springboot kafka集成
tags:
  - spring
  - kafka
categories:
  - java
date: 2018-03-08 14:31:00
---
##### 1、pom配置

##### 2. codeblock
```c++
class Solution {
public:
	void replaceSpace(char *str,int length) {
		if(str==NULL||length<0) return ;
        int numOfBlank=0;
        int newLength;
		int original=0;       
       int i=0;
        while(str[i]!='\0'){
            original++;
            if(str[i]==' '){
                numOfBlank++;
            }
            i++;
        }
        newLength = original+2*numOfBlank;
        if(newLength>length) return;
		while(original>=0&&newLength>original){
            if(str[original]==' '){
                str[newLength--]='0';
                str[newLength--]='2';
                str[newLength--]='%';
            }else{
               str[newLength--]=str[original];
            }
            original--;
        }	        
	}
};
```